# P04 — Diedros: estender o nó `angle` para 4 átomos (opção A)

Data: 2026-10-01. Status: decidida. Etapa: 1 (fontes).

## Decisão

**Opção A: `angle` com 3 ou 4 átomos** (`posicao=1..4`, `classe={ligacao, diedro}`, `valor_deg, ideal_deg`). Sem novo label.

## Por quê (opções avaliadas)

| Opção | Prós | Contras | Veredito |
|---|---|---|---|
| A. `angle` 3–4 átomos | 1 label; padrão de consulta único (“ângulos do resíduo X” p/ QE2/QE7/QE8); mantém 3 tipos de aresta; diedros/resíduo ~8 nós (phi/psi/omega + ≤4 chi + peptídicas) — custo ok | `angle` com 4 membros exige `posicao` (já previsto) | **escolhida** |
| B. novo nó `dihedral` | separação semântica limpa | +1 label, +regras de `part_of`, bifurca QEs; ganho zero p/ o MVP | rejeitada |
| C. diedro como propriedade | zero nós extras | não ancora `distance` nem comparação cross-estrutura (Ramachandran, rotâmeros viram string) | rejeitada |

Base bibliográfica:

* Campos de força decompõem energia em **ligação / ângulo / torção / out-of-plane** como termos independentes (MMFF94: Halgren, J Comput Chem 1996–1999, série I–VIII; AMBER: Cornell et al., JACS 1995; CHARMM: MacKerell et al., J Phys Chem B 1998) — diedro é corpo de 1ª classe, merece nó, não propriedade (contra C).
* GNNs geométricas ganham ao adicionar corpos superiores: DimeNet (Klicpera et al., NeurIPS 2020 — mensagens direcionais c/ ângulos) → GemNet (Klicpera et al., NeurIPS 2021 — **torções como features**, SOTA em QM9/MD17) → SphereNet/ComENet (representações esféricas completas). Diedro como feature de 4 corpos é o padrão que funciona (a favor de nó, contra C).
* Validação cristalográfica exige diedros consultáveis: Ramachandran (1963, phi/psi), rotâmeros Dunbrack (Shapovalov & Dunbrack, Structure 2011, chi), MolProbity (Chen et al., Acta Cryst. 2010) — QE7/QE8 precisam de `angle{classe=diedro}` por resíduo.
* CCD guarda coords ideais 3D, não torções: `ideal_deg` do diedro deriva das coords ideais (documentar `ideal_fonte=CCD-3D`); alternativa `ideal_fonte=Dunbrack-mode` p/ chi. Medido sempre do xyz da instância.

## Consequências

1. `angle`: `atom_instance -part_of→ angle` (3 ou 4 arestas), `angle -part_of→ monomer_instance`; `classe` obrigatória.
2. Ponte de H: `distance{classe=ponte_h}` + `angle{classe=ligacao}` D–H···A no mesmo trio (QE2).
3. MVP: diedros só de backbone (phi/psi/omega) + chi; sem out-of-plane.

## Referências

* Halgren, T. A. J Comput Chem 1996–1999 (MMFF94 I–VIII).
* Klicpera, J. et al. NeurIPS 2020 (DimeNet); NeurIPS 2021 (GemNet).
* Ramachandran, G. N. et al. J Mol Biol 1963; Shapovalov & Dunbrack, Structure 2011; Chen et al., Acta Cryst. D 2010 (MolProbity).
* docktorch `core/torsion_tree.py` (genes diedrais, referência de implementação).
