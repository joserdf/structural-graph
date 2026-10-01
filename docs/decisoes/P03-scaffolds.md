# P03 — Scaffolds dinâmicos: L1 + L2 no MVP, L3 como extensão

Data: 2026-10-01. Status: decidida. Etapa: 1 (fontes).

## Decisão

**MVP: L1 Murcko clássico + L2 Murcko genérico lado a lado** (`GetScaffoldForMol`, `MakeScaffoldGeneric`, `includeChirality=False`). **L3 Scaffold Network (BRICS) como extensão** pós-MVP.

## Por quê (opções avaliadas)

| Opção | O que é | Prós | Contras | Veredito |
|---|---|---|---|---|
| L1 Bemis–Murcko clássico (Bemis & Murcko, J Med Chem 1996: frameworks de 117k compostos; `MurckoScaffoldSmiles`) | anéis + linkers, remove cadeias laterais | padrão da área, reproduzível, 1 função | macrociclos viram 1 scaffold gigante inútil; peptídeos/oligossacarídeos idem | **MVP** (só HETs pequenos) |
| L2 Murcko genérico (`MakeScaffoldGeneric`: átomos→C, ligações→simples) | esqueleto topológico | recall p/ scaffold hopping (QEs de similaridade) | perde heteroátomos (precisão) | **MVP, ao lado do L1** (dois `scaffold` nós por ligante, `nivel=L1/L2`) |
| L3 Scaffold Network (Varin et al., JCIM 2011; `rdScaffoldNetwork`, Krämer et al., JCIM 2021 — regras BRICS, scaffolds genéricos, counts) | fragmentação iterativa → rede (nós=fragmentos, arestas=operações) | navegação multi-nível, counts p/ priorizar; a rede **é** um grafo (converge com o nosso) | custo + complexidade; overkill p/ 30 PDBs | extensão |
| BRICS standalone (Degen et al., ChemMedChem 2008) | 16 regras de quebra sinteticamente factíveis | fragmentos relevantes p/ síntese | granularidade fina demais p/ `scaffold` (é nível `fragment`) | usar p/ validar L3, não como scaffold |
| RECAP (Lewell et al., JCICS 1998) | 11 regras retrosintéticas | clássico medicinal | superado pelo BRICS em cobertura | referência |
| Scaffold Tree / Hunter (Schäfer/Wetzel et al., 2007–2008) | hierarquia anel-por-anel | navegação visual | implementação externa, sem RDKit nativo | fora |

Pitfalls registrados: sais/multi-fragmento → `keepOnlyFirstFragment` (ou descartar HET salino no MVP); quiralidade achatada no MVP (`flattenChirality`, HETs PDB raramente têm estereo confiável); **polímeros e branched NÃO ganham scaffold** (Murcko de peptídeo/oligossacarídeo = backbone inteiro, sem valor) — scaffold só p/ `nonpolymer` pequenos.

## Consequências

Campos do nó `scaffold`: `murcko_smiles, generic_smiles, nivel {L1,L2}, rdkit_ver, n_aneis, n_heavy, regra_ver`. `scaffold_instance part_of monomer_instance` (1 HET → 1–2 scaffolds).

## Referências

* Bemis, G. W.; Murcko, M. A. J Med Chem 1996, 39, 288–296.
* Varin, T. et al. JCIM 2011 (scaffold networks).
* Krämer, M. et al. rdScaffoldNetwork. JCIM 2021. doi:10.1021/acs.jcim.0c00296. https://www.rdkit.org/docs/source/rdkit.Chem.Scaffolds.html
* Degen, J. et al. ChemMedChem 2008, 3, 1503–1507 (BRICS).
* Lewell, X. Q. et al. JCICS 1998, 38, 511–522 (RECAP).
