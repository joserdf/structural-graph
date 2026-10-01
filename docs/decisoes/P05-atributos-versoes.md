# P05 — Critério nó-vs-atributo + versões congeladas

Data: 2026-10-01. Status: decidida (tabela de atributos vale como proposta; revisão final na etapa de modelagem lógica). Etapa: 1 (fontes).

## Regra de bolso

> **Vira nó** o que tem identidade compartilhada (dedup), ancora aresta, ou é consultado por valor entre estruturas. **Vira atributo** a medida de uma ocorrência. **Vira prop de `distance`** a geometria entre ocorrências.

Base: guias de modelagem Neo4j (graph data modeling: modelar por perguntas), Robinson–Webber–Eifrem, *Graph Databases* (O'Reilly 2015); Angles & Gutierrez, ACM Comput Surv 2008 (modelos de grafos); Hogan et al., ACM Comput Surv 2021 (knowledge graphs, reificação vs. propriedades); lição LPG-vs-RDF: reificar tudo explode — propriedade com unidade e fonte basta p/ medida.

## Tabela atributo a atributo

| Valor | Decisão | Onde | Motivo |
|---|---|---|---|
| xyz | atributo | `atom_instance.{x,y,z}` (futuro: Parquet/Zarr, grafo guarda ponteiro) | nunca consultado por valor; array |
| B-factor | atributo | `atom_instance.b_factor` | medida da ocorrência (+QE7 agrega, não navega) |
| ocupação | atributo | `atom_instance.ocupacao` | idem |
| altloc | atributo + regra | `atom_instance.altloc`; MVP: só altloc A + `tem_altloc=true` | variantes extras só se participarem de interação por regra |
| carga/protonação | atributo | `atom_instance.carga`, `monomer_instance.protonacao` (fonte CCD/HET, **nunca** nome do resíduo — lição docktorch/HIS) | estado da ocorrência |
| hibridização | atributo | `atom.tipo` (nível tipo) | define o tipo atômico |
| backbone/sidechain | atributo derivado | `atom_instance.eh_backbone` (N,CA,C,O) | acelera QE sem join |
| bulk (água) | atributo | `monomer_instance.bulk` | flag de linhagem da HOH |
| aromaticidade/ordem | prop de `distance` (tipo) | `ordem, aromatica?, conjugada?` | relação entre 2 `atom`s |
| scores de validação | atributo | `chain_instance`/`monomer_instance` (RSCC etc.) | medida da ocorrência |
| posição na sequência | chave | `num_seq, icode` na chave da instância | identidade |
| ângulos/diedros | **nó** `angle` | ver P04 | ancora `distance`, compara cross-estrutura |
| grupos/scaffolds | **nó** | ver P01/P03 | identidade compartilhada (dedup) |

Discussão que fica p/ a etapa lógica: `element`-params (raios) como props (decidido: sim, são do tipo) e validação por resíduo (atributo vs. vista).

## Versões congeladas (verificado 2026-10-01)

| Dependência | Versão | Licença | Registro |
|---|---|---|---|
| mendeleev (PyPI) | **1.1.0** (MIT) | MIT | `versoes.md`: `mendeleev==1.1.0`, `elements.db` interno |
| RDKit | **2026.03.5** (docs) / wheels PyPI `rdkit` (builds não-oficiais kuelumbus; oficial = conda) | BSD-3-Clause | `rdkit_ver` em todo `scaffold`/`fragment`; preferir conda p/ reprodutibilidade |
| CCD | rolling (wwPDB, ~semanal) | público | **gravar `ccd_dump_date`** em todo `monomer` |
| PLIP | **3.0.1** (PyPI) / 3.0.0 (GitHub 2025) | **GPL-2.0 → oráculo via container, NUNCA vendorizar código**; thresholds (números) livres; docktorch reimplementa | `plip_ver` em todo `Contact` de validação |
| RCSB GraphQL + `files.rcsb.org/download/{PDB}-{assembly}.cif` | estável (código docktorch/docktdata em produção) | público | `assembly_id` + `pdb_ver` na chave |
| SmartChemist | commit hash a travar | BSD-3-Clause | `fonte_commit` em todo `fragment` |
| Gobbi fdef | = versão RDKit | BSD-3 | `fdef_ver=rdkit_ver` |

Formato do manifesto: `docs/versoes.md` (tabela acima) + cada nó tipo carrega `*_ver` da fonte que o gerou. Sem `*_ver`, o fato não entra.
