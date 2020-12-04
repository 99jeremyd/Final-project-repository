# Final-project-repository
## This command tells us to combine the treefiles two files into one, alternative trees file.
``` cat XP_001619085.2.r50.ufboot.treefile XP_001619085.2.r50.ufboot.unrooted.treefile.rearrange > XP_001619085.r50.alternativetrees ```
## This command produces our second optimal tree with the  -z flag signifying to use this file. The -au flag tells iqtree to run an approximately unbiased topological test. the -zb flag tells iqtree to run X amount of bootstrap replicas for the approximately unbiased test, here it is 10,000. The -te flag specifies which file iqtree will use to set model parameters.
``` iqtree -s XP_001619085.2.blastp.detail.filtered.aligned.fas -z XP_001619085.r50.alternativetrees -au -zb 10000 --prefix DSCAM_altTrees -m LG+F+R5 -nt 2 -te XP_001619085.2.r50.ufboot.treefile ```
