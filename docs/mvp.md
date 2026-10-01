# MVP GA-009

* 1 família (ex. kinases), ~30 PDBs apo/holo, raios-X < 2.5 Å. Cada PDB expande para seus assemblies anotados.
* Nós: os 16 do modelo; `atom_instance` = pesados + H resolvido; `angle` só peptídica + ponte de H.
* Arestas: `instance_of` + `part_of` todas; `distance` só R0–R5. Sem vizinhança persistida.
* Fora: R6–R7, CATH, NMR completo (só medoide), similaridade vetorial.
* Entregas: P1 = ER + dicionário; P2 = mapeamento grafo + 8 QEs Cypher; Projeto = carga + relatório + redundância/temporalidade.
* Ingestão futura: Gemmi/Biopython (REMARK 350 → assemblies) + RDKit → bulk. Nada de código neste passo docs-only.
