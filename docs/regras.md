# Regras de interação R1–Rn (a congelar)

Só vira aresta `distance` em instância o que passa aqui. Cada aresta carrega `regra_id` + `regra_ver`. Raios de referência vivem nos nós `element`.

| ID | Classe | Proposta inicial (ajustar) | Ângulo | Notas |
|---|---|---|---|---|
| R1 | `ponte_h` | D–A ≤ 3.5 Å | D–H–A > 120° (nó `angle`) | intra + inter |
| R2 | `salina` | N–O ≤ 4.0 Å | — | Glu/Asp × Lys/Arg/His+ |
| R3 | `hidrofobico` | C–C ≤ 4.5 Å | — | só apolar × apolar |
| R4 | `stacking` | centroides ≤ 5.5 Å | diedro registrado em `angle` | aromático × aromático |
| R5 | `agua_mediada` | X–HOH e HOH–Y ≤ 3.5 Å, mesma `ponte_id` | > 120° | HOH como nó |
| R6 | `halogenia` | X–O/N ≤ 3.5 Å | C–X···O > 140° | extensão pós-MVP |
| R7 | `cation_pi` | cátion–centroide ≤ 6.0 Å | — | extensão pós-MVP |
| R0 | `covalente_excecao` | só peptídica, dissulfeto, desvio CCD | `angle` peptídica | template continua no tipo |

MVP: R0–R5. R6–R7 fora.

## Decisões pendentes

1. H contados explicitamente ou inferidos? (define R1 sem ambiguidade)
2. Cutoffs por par de `element` (soma vdW + tolerância) ou fixos?
3. `bulk=true` participa de R5 ou só água de contato?
