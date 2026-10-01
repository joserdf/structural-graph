# Modelo — nós, arestas, chaves

Espelho compacto do plano canônico. Fonte da verdade: `../trabalhos/proposta-2-grafos-estrutural-plano.md`.

## Nós (16)

| label | tipo/inst | chave | props | exemplo |
|---|---|---|---|---|
| `element` | sempre tipo | `símbolo` | Z, massa, raios cov/vdW, eletronegatividade | C, O, ZN |
| `atom` | tipo | `atom_tipo_id` | elemento, hibridização, papel | C.aromático, O.aceptor |
| `atom_instance` | instância | `pdb,assembly,modelo,cadeia_assembly,seq,serial` | xyz, B-factor, ocupação, altloc, `h_ausente` | OG Ser123 1A2K/ass1 |
| `angle` | sempre inst | `pdb,assembly,angle_seq` | `valor_deg, ideal_deg, desvio` | N–CA–C 111.2° |
| `fragment` | tipo | `fragment_id+eixo` | eixo químico/farmacofórico, SMARTS, `rdkit_ver` | HIDROXILA / DOADOR_H |
| `fragment_instance` | instância | `pdb,assembly,frag_seq` | conformação, membros | hidroxila de Ser123 |
| `scaffold` | tipo | `murcko_smiles` | anéis, linkers | Murcko imatinib |
| `scaffold_instance` | instância | `pdb,assembly,het` | RMSD | scaffold de LIG401 |
| `monomer` | tipo | `aa3/cc_id` | template CCD | SER, HOH, ZN |
| `monomer_instance` | instância | `pdb,assembly,modelo,cadeia_assembly,seq,icode` | chi, `bulk`, ocupação | Ser123, HOH501 |
| `chain` | tipo | `uniprot_ac` | sequência | P00533 |
| `chain_instance` | instância | `pdb,assembly,cadeia_assembly,modelo` | cobertura, cópia | 1A2K/ass1/A |
| `molecule` | tipo | `molecule_tipo_id` | classeدا proteína/ligante/solvente | P00533, SOLVENT |
| `molecule_instance` | instância | `pdb,assembly,mol_seq` | centro, RG | protômero A |
| `assembly` | sempre inst/raiz | `pdb,assembly_id` | composição, REMARK350 | 1A2K/ass1 |
| `pdb` | proveniência | `pdb_id` | método, resolução, versão | 1A2K 1.8 Å |

## Arestas (3)

| tipo | padrão | props |
|---|---|---|
| `instance_of` | `*_instance → *` (atom, fragment, scaffold, monomer, chain, molecule) | — |
| `part_of` | inst: `atom→fragment→monomer→chain→molecule→assembly→pdb`; tipo: `atom→fragment→monomer→chain→molecule` + `atom→element`; ângulo: `atom→angle→monomer` | `posição` (1/2/3 em angle) |
| `distance` | tipo: `atom–atom` ideal; inst: `atom_instance–atom_instance` por regra | `valor_Å, classe, ordem?, intra/inter, regra_id/ver, ponte_id?` |

## Convenções

* `element` de uma instância deriva por `atom_instance -instance_of→ atom -part_of→ element`.
* Água: N `monomer_instance`(HOH) → 1 `molecule_instance`(solvente) → `assembly`; bulk com `bulk=true`.
* HET pequeno: `atom_instance→fragment_instance→scaffold_instance→monomer_instance→molecule_instance→assembly`.
* `angle`: 3× `atom_instance -part_of→ angle -part_of→ monomer_instance`.
* Água mediada: 2× `distance{classe:agua_mediada, ponte_id}` passando pela HOH como nó.
