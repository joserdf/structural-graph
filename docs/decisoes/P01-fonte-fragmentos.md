# P01 — Fonte de grupos funcionais: SmartChemist + alternativas

Data: 2026-10-01. Status: decidida. Etapa: 1 (fontes).

## Decisão

**Primária: SmartChemist** (Gutermuth et al., JCIM 2025). **Fallback: RDKit Fragments + ChEBI.**

## Por quê (opções avaliadas)

| Fonte | Tamanho | Formato | Hierarquia | Licença | Veredito |
|---|---|---|---|---|---|
| SmartChemist (Gutermuth et al., JCIM 2025; Rarey group; `torbengutermuth/SmartChemist`, `chemist.smarts.plus`) | 40.940 padrões: 158 FG manuais (Gold Book) + 40.724 cíclicos (PubChem+RDKit) + 58 bio-relevantes | SMARTS + nomes | sim: mostra o mais específico, resto vira *overshadowed* (subset escondido) | **BSD-3-Clause (verificado no GitHub)** → pode vendorizar com atribuição | **primária**: cobre eixo químico + cíclicos + regra de overshadow pronta (= nossa dupla classificação §4) |
| Daylight SMARTS examples (`daylight.com/dayhtml_tutorials/languages/smarts/`) | dezenas (exemplos didáticos: fenol em 3 átomos, C quiral, ligação rotacionável) | SMARTS | não (padrões p/ busca, não p/ descrição precisa — o próprio artigo SmartChemist faz essa ressalva) | consulta livre; redistribuição sem licença explícita **[a verificar]** | referência, não fonte |
| ChEBI (Hastings et al., 2016; EBI) | 180k+ entidades bio-relevantes + papéis (roles) | OWL/OBO/SMILES | sim (ontologia) | dados EBI livres c/ atribuição **[confirmar texto da licença no uso]** | fallback p/ eixo bio-relevante |
| ClassyFire / ChemOnt (Djoumbou Feunang et al., J Cheminform 2016) | milhares de categorias c/ SMARTS + Markush | SMARTS/Markush + taxonomia | sim, hierárquica | código GPL; dados via API **[a verificar p/ vendorização]** | alternativa p/ categorização hierárquica |
| RDKit Fragments (`rdMolDescriptors`, grupos de Ertl) | ~100 fragmentos funcionais | SMARTS embutido | não | **BSD-3 (é RDKit)** | **fallback**: zero dependência extra, cobre FG comuns |
| exmol (pacote p/ explicar predições black-box) | pequeno, p/ visualização | SMARTS | não | MIT **[a verificar]** | só referência |

## Consequências

1. Vendorizar os 158 SMARTS de FG + metadados (nome, eixo) com `fonte=SmartChemist`, `licenca=BSD-3-Clause`, `commit=<hash>`, citação JCIM 2025.
2. Regra de overshadow do SmartChemist vira nossa regra de classificação: match subset → escondido por padrão, recuperável por flag.
3. Cíclicos (40.724) **não** vendorizados no MVP — consulta sob demanda (são nomes de anéis p/ `fragment`, não p/ `scaffold`, que é dinâmico via RDKit).

## Referências

* Gutermuth, T. et al. SmartChemist — Simplifying Communication About Organic Chemical Structures. JCIM 2025. https://pubs.acs.org/jcisd8/article/65/17/9075/3687786/SmartChemist-xe5f8-Simplifying-Communication
* Repo: https://github.com/torbengutermuth/SmartChemist (BSD-3-Clause, verificado 2026-10-01)
* Daylight SMARTS: https://www.daylight.com/dayhtml_tutorials/languages/smarts/
* ChEBI: Hastings et al., NAR 2016. https://www.ebi.ac.uk/chebi/
* ClassyFire: Djoumbou Feunang et al., J Cheminform 2016, 8:61.
* RDKit Fragments: https://www.rdkit.org/docs/source/rdkit.Chem.Fragments.html
