# Manifesto de versões (congelar antes do loader)

Última verificação: 2026-10-01. Detalhes e licenças: `decisoes/P05-atributos-versoes.md`.

| Dependência | Versão travada | Licença | Campo de linhagem nos nós |
|---|---|---|---|
| mendeleev | 1.1.0 | MIT | `mendeleev_ver` em `element` |
| RDKit | 2026.03.5 (conda oficial; wheels PyPI são builds terceiros) | BSD-3-Clause | `rdkit_ver` em `scaffold`/`fragment`/`fdef_ver` |
| CCD `components.cif.gz` | dump semanal wwPDB — **gravar data** | público | `ccd_dump_date` em `monomer` |
| PLIP (oráculo) | 3.0.1 | GPL-2.0 (container; sem vendorizar) | `plip_ver` na validação |
| RCSB GraphQL + assembly CIF | endpoints estáveis | público | `assembly_id`, `pdb_ver` na chave |
| SmartChemist padrões | commit hash a travar | BSD-3-Clause | `fonte_commit` em `fragment` |

Regra: fato sem `*_ver` não entra no grafo.
