# Etapa 1 — Mapeamento de fontes por nó

Data: 2026-10-01. Status: mapeado, a congelar versões antes de qualquer loader.

## Tabela nó → fonte

| Nó | Fonte | Acesso | O que extrair | Código para reusar |
|---|---|---|---|---|
| `element` | **mendeleev** (`pip install mendeleev`, SQLite `elements.db` versionado) | `element('O')`, `fetch_table('elements')` | Z, massa, raios cov/vdW, eletronegatividade (Pauling + 15 escalas), estados de oxidação, raios iônicos, `propertymetadata` (unidades, citações) | — |
| `fragment` (eixo químico) | **SmartChemist** — 158 grupos funcionais handcrafted, checados no Gold Book (JCIM 2025, Gutermuth/Rarey) | GitHub `torbengutermuth/SmartChemist` + `chemist.smarts.plus` | 158 SMARTS + nomes; regra de *overshadowed* (match subset escondido, mostra só o mais específico) | Lógica de overshadow = nossa regra de dupla classificação §4 |
| `fragment` (eixo farmacofórico) | A definir (RDKit pharmacophore features ou SmartChemist como base) | — | doador/aceptor-H, hidrofóbico, aromático, cátion, ânion | Pendência: escolher entre RDKit `Pharmacophore` e curadoria própria |
| `fragment` (cíclicos) | **SmartChemist** — 40.724 padrões cíclicos auto-extraídos PubChem via RDKit | mesmo repo acima | nomes triviais de anéis (ex. xantina vs. IUPAC) | Suporte a nomes de scaffold |
| `scaffold` | **RDKit dinâmico**, 3 níveis: L1 Murcko clássico → L2 Murcko genérico → L3 Scaffold Network (BRICS) | `rdkit.Chem.Scaffolds.MurckoScaffold` (`GetScaffoldForMol`, `MakeScaffoldGeneric`), `rdScaffoldNetwork` (`BRICSScaffoldParams`, `CreateScaffoldNetwork`) | L1 SMILES, L2 C-genérico, L3 rede (nós + arestas + counts) | Rede de scaffolds é um grafo — converge com `scaffold_instance part_of` |
| `monomer` | **CCD** `https://files.wwpdb.org/pub/pdb/data/monomers/components.cif.gz` | stream + cache local, parse 1x | template por `comp_id`: átomos, ligações + ordem, `pdbx_leaving_atom_flag`, `pdbx_stereo_config`, coords ideais 3D (→ ângulos ideais) | `docktdata/.../peptide/monomer_library.py` (topologia-only, quiralidade via `AssignStereochemistryFrom3D`, sem conformer p/ InChIKey) + `monomer.py` (`MonomerTemplate`) |
| `chain` | **RCSB PDB GraphQL** `polymer_entities` | `entity_poly.pdbx_seq_one_letter_code_can`, `rcsb_entity_polymer_type` (Protein/RNA/DNA) | sequência canônica por entidade | `docktdata/.../transform/PDB/entities.py`, `extract/PDB/db_writer.py` |
| `molecule` pequena | **RCSB** `nonpolymer_entities` (1 CCD = 1 monômero) | `rcsb_nonpolymer_entity_container_identifiers` + InChIKey | 1 monômero + 1 cadeia (asym) + 1 molécula | Mesmo `entities.py` (`preprocess_nonpolymer_entity_instance`: `asym_id`/`auth_asym_id` por instância) |
| `molecule` polímero | **RCSB** `polymer_entities` | N monômeros lineares por entidade | cadeia = sequência de monômeros | `preprocess_polymer_entity_instance` (+ `auth_to_entity_poly_seq_mapping`) |
| `molecule` branch | **RCSB** `branched_entities` (`pdbx_entity_branch` = BRANCHED, oligossacarídeos) | N monômeros com ramificação | ligações inter-monoméricas fora da backbone | `preprocess_branched_entity_instance`; no grafo: ramificação = `distance` covalente exceção (já previsto) |
| `assembly`, `pdb` + instâncias | **RCSB** assemblies + CIF por assembly | `https://files.rcsb.org/download/{PDB}-{assembly}.cif`; instâncias `*_entity_instances` por assembly | `assembly_id` (REMARK 350), `asym_id`/`auth_asym_id`, `modeled_residue_count` | `docktdata/.../load/preparation/core.py::download_assembly_cif_block`, `prepwizard.py::_download_assembly_cif` |
| `distance` + `angle` (regras) | **docktorch** `core/interactions/` + PLIP 3.0.1 | `base.py` (`Contact`, `InteractionParams`), `perception.py` (tipos MMFF94S + cargas), `detect.py` (`detect_interactions`) | `Contact` mapeia 1:1 p/ nosso modelo (kind→classe, átomos, resíduo/cadeia/id, distância Å, ângulo radianos, offset, backbone, doador) | Thresholds PLIP padrão (ver `regras.md`); precedência salt>hbond>pication>pistack>hydrophobic; redução hydrophobic-patch |
| `angle` ideal | **CCD** coords ideais 3D | mesmo parse do `monomer` | ângulos de ligação ideais por template | — |
| `angle` diedro | medido do xyz da instância (4 átomos) | — | torções de backbone/cadeia lateral, rotâmeros | Referência: `docktorch/core/torsion_tree.py` (genes diedrais); **decisão**: estender `angle` p/ 4 átomos (`posição=1..4`) ou criar `dihedral` |

## Interações: ponto de partida (docktorch, validado vs. PLIP)

`docktorch/core/interactions/base.py::InteractionParams`: hydrophobic C–C 4.0, hbond D–A 4.1 + doador ≥100°, salt-bridge 5.5 (centroides), stacking 5.5 + tol 30° + offset 2.0, pication 6.0, min 0.5. Alternativa estrita em `docs/adr/0023`: H···A ≤2.80, doador ≥120°, aceptor ≥90°, halogênea ≤3.5/140°, salina ≤4.0. Halogênea/metal **fora** (sem validação nos inputs — mesma decisão do nosso R6/R7 pós-MVP). Caveat conhecido: carga de HIS por nome de resíduo gera salt-bridges espúrias — carregar estado de protonação do CCD/HET, não do nome.

## Pendências desta etapa — resolvidas em `decisoes/`

1. Licença SmartChemist → **P01**: BSD-3-Clause, pode vendorizar os 158 SMARTS c/ atribuição.
2. Eixo farmacofórico → **P02**: `BaseFeatures.fdef` (6/7) + SMARTS próprio de halogênio (R6).
3. Nível de scaffold do MVP → **P03**: L1 + L2 lado a lado; L3 extensão; sem scaffold p/ polímeros/branched.
4. `angle` de 4 átomos p/ diedros → **P04**: estender `angle` (`classe`, `posicao=1..4`).
5. Versões → **P05** + `versoes.md` (tabela travada 2026-10-01).
6. Atributo-vs-nó → **P05** (tabela; revisão final na modelagem lógica).
