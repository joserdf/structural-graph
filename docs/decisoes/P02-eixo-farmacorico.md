# P02 — Eixo farmacofórico: BaseFeatures.fdef (RDKit) + extensões

Data: 2026-10-01. Status: decidida. Etapa: 1 (fontes).

## Decisão

**Primário: RDKit `BaseFeatures.fdef`** (famílias Gobbi) para 6 das 7 features. **Extensão própria: halogênio-bond donor** (SMARTS custom, `regra_ver` própria). Refinos: hidrofobicidade via Wildman–Crippen, doador/aceptor cruzado com tipos MMFF94S.

## Por quê (opções avaliadas)

| Opção | Features cobertas (das 7) | Formato | Licença | Veredito |
|---|---|---|---|---|
| RDKit `BaseFeatures.fdef` (Gobbi & Poppinger 1998, fingerprints farmacofóricos 2D; `Pharm2D.DefaultSigFactory`, `Gobbi_Pharm2D`) | 6/7: Donor, Acceptor, NegIonizable (ânion), PosIonizable (cátion), Aromatic, Hydrophobe/LumpedHydrophobe | .fdef versionado c/ RDKit | **BSD-3** | **primário** |
| Wildman–Crippen (`rdMolDescriptors.CalcCrippenDescriptors`; Wildman & Crippen, JCICS 1999) | hidrofobicidade quantitativa (logP/MR por átomo) | embutido RDKit | BSD-3 | refino do Hydrophobe |
| MMFF94S tipos atômicos (Halgren, J Comput Chem 1996–1999) | doador/aceptor percepção (mesmo que `docktorch/.../perception.py` usa) | tabelas MMFF + código docktorch | BSD-3 (docktorch é do grupo) | cross-check doador/aceptor |
| ChEBI roles | papéis bioquímicos (doador etc. indireto) | OWL | EBI livre c/ atribuição | fallback semântico |
| pmapper / Pharmer / MOE / Phase / OpenEye | variados | misto | misto/proprietário **[verificar um a um; MOE/Phase/OpenEye fora por licença]** | fora do MVP |
| SMARTS custom de halogênio (sigma-hole: C–X···O, X=Cl/Br/I) | 1/7: halogen-bond donor | SMARTS próprio | nossa | **extensão R6** (pós-MVP, critério PLIP ≤3.5 Å/≥140° como ponto de partida) |

Gap conhecido do `BaseFeatures.fdef`: **não há família halogênio** — por isso R6 é extensão pós-MVP com SMARTS próprio, consistente com a decisão do docktorch (sem validação, sem detector).

## Consequências

1. `fragment` eixo 2 = família fdef + `fdef_ver=<versão RDKit>`.
2. Um `atom_instance` pode mapear p/ N features (ex. OG Ser = Donor + Acceptor) — duas arestas `part_of` p/ dois `fragment`s, como já previsto.
3. Carga de HIS segue protonação do CCD/HET, nunca nome do resíduo (lição docktorch).

## Referências

* Gobbi, A.; Poppinger, D. Biotechnol. Bioeng. 1998 (fingerprints farmacofóricos 2D).
* RDKit Pharm2D: https://www.rdkit.org/docs/source/rdkit.Chem.Pharm2D.html
* Wildman, S. A.; Crippen, G. M. JCICS 1999, 39, 868–873.
* Halgren, T. A. J Comput Chem 1996–1999 (série MMFF94 I–VIII).
* PLIP (Salentin et al., NAR 2015; Schake, Bolz et al., NAR 2025): https://github.com/pharmai/plip
