# IAG for three Papaver species
To figure out what genome structural changes occured to shape the present-day karyotypes of three Papaver species. We developed a novel bottom-up workflow IAG (inferring ancestor genome) to reconstruct pre- and post-WGD-1 ancestors of three Papaver genomes. 

IAG framework was specifically designed for three Papaver species, and is not able to correctly reconstruct the pre-WGD-2 ancestor. We have updated IAG to IAGS (https://github.com/xjtu-omics/IAGS) under a wide range of evolutionary scenarios by involving block matching optimization (BMO) and endpoint matching optimization (EMO).  

## Dependencies
python 3.6.4
```Bash
conda create -n environment python=3.6.4
```
```Bash
conda install packge=version
```
packges  | version|
--------- | --------|
numpy  | 1.16.4 |
pandas  | 0.20.3 |
networkx | 2.1 |

[Gurobi 9.0.2](https://www.gurobi.com)

## Introductions

### inferringAncestorGenomeStructure
Containing the source code of three models for inferring ancestor genome structures which are focusing on the block connection, including:

* GMP(genome median problem): Given some ordinary genome(with no WGD, one copy), finding the ancestor genome by minimizing the genomic distance.

* GGAP(guided genome aliquoting problem): Given a duplicated genome(with a WGD or WGT, two or three copies) and a ordinary genome, finding pre-duplicated ancestor genome by minimizing the genomic distance.

* MO(matcing optimaztion): Given two multi-duplicated genome, finding the block matcing relation by minimaizing the genomic distance.

We built three integer programming model to solve above problems. All above optimization instances were solved with the GUROBI solver.

### inferringGeneContent
Containing the source code for inferring ancestor genome gene order. Each block contains multi-copies in sub current genomes and with some different gene indels and micro rearrangements. This step is intend to infer the possible ancestral gene order in ancestors. We used topological sorting with a greedy strategy. When the topological sorting removed nodes, we recorded their out-degree edges weight and used this weight as decision condition. When there is not node without in-degree edge (the number of in-degree is 0), we removed the node with maximal decision weight to break cycle and continue sorting to get the final order.

### inputFiles

* block files which is the block order and direction of three species. P. rhoeas(PR), P. somniferum(PS) and P. setigerum(PT).

* multi-synteny files which is the block gene content in multi-copies with the pPG ID (putative protogene, ortholog gene groups) and gene name.

### outputFiles

* 1_PT_pre: contains the pre-WGD-2 ancestor, using P. somniferum (PS) as outgroup genome and P. setigerum (PT) as duplicated genome. First, we used 4:2 MO to find which two block in PT corresponding to which one block in PS and then using GGAP (WGD) to infer. The result file is PT_preWGD_ancestor.block and matching relation is in matching_PT_PS.txt.
* 2_PS_post: contains the post-WGD-1 ancestor, using doubled P. rhoeas (PR), P. somniferum (PS), and pre-WGD-2 ancestor (PT_preWGD_ancestor.block). First, we used 2:2 MO to find the block matching relation in three genome and then using GMP to infer. The result file is PS_postWGD_ancestor.block (PS_postWGD_ancestor.block.withbar) and matcing relation is in matching_PRdouble_PS.txt.

* 3_PS_pre: contains the pre-WGD-1 ancestor, using P. rhoeas (PR) and post-WGD-1 ancestor (PS_postWGD_ancestor.block). We used GGAP (WGD) to inder. The result file is PS_preWGD_ancestor.block.

* 4_block_gene_content: contains the inferred ancestor block gene content. 
```Bash
*_synteny_ps.txt: ancestor's block gene order. The number is pPG ID.
```
```Bash
*_synteny.name_ps.txt: ancestor's block gene order with gene name.
```
```Bash
*_synteny.genename_ps.txt: all gene name represent by same pPG ID.
```
han corresponds to PS_preWGD_ancestor.block and dan corresponds to PS_postWGD_ancestor.block.withbar.

### pipline.py
The python version workflow for inferring Papaver ancestor genomes.

## Citation
Yang, X., Gao, S., Guo, L. et al. Three chromosome-scale Papaver genomes reveal punctuated patchwork evolution of the morphinan and noscapine biosynthesis pathway. Nat Commun 12, 6030 (2021). https://doi.org/10.1038/s41467-021-26330-8
