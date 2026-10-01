# structural-graph

Grafo estrutural multi-escala **átomo → assembly**, sem bioatividade. GA-009 Banco de Dados, LNCC.

Submódulo de `ga-009`. Plano canônico: `../trabalhos/proposta-2-grafos-estrutural-plano.md` (espelho em `docs/plano.md`).

## Modelo em 30 segundos

* **16 nós:** `element` + 6 pares tipo/instância (`atom`, `fragment`, `scaffold`, `monomer`, `chain`, `molecule`) + `angle` + `assembly` + `pdb`.
* **3 arestas:** `instance_of`, `part_of`, `distance`.
* **Unidade de armazenamento:** `assembly` (unidade biológica, REMARK 350), não asymmetric unit.
* **Regras:** só vira `distance` o que passa em R1–Rn versionadas (`docs/regras.md`). Sem vizinhança persistida.
* **Templates:** ligações covalentes vivem no tipo, instância herda via `instance_of`. Exceção (peptídica, dissulfeto, desvio CCD) vira `distance` em instância.

## Estrutura

```
structural-graph/
├── README.md
└── docs/
    ├── plano.md      # espelho do plano canônico
    ├── modelo.md     # nós + arestas + chaves
    ├── regras.md     # R1–Rn (a congelar)
    └── mvp.md        # recorte GA-009
```

## Próximo passo

Congelar `docs/regras.md` antes de qualquer loader. Sem R1–Rn não há ingestão.
