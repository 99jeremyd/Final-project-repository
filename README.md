# Final-project-repository
## Download the XP_001619085.2 protein from the NCBI database
``` ncbi-acc-download -F fasta -m protein XP_001619085.2 ``` 
## BLAST against potential homologs
```blastp -db ~/data/blast/allprotein.fas -query XP_001619085.2.fa -outfmt 0 -max_hsps 1 > XP_001619085.2.blastp.typical.out```
## Find potential homologs with an e-score lower than 1e-11
``` awk '{if ($6<0.00000000001)print $1 }' XP_001619085.2.blastp.detail.out > XP_001619085.2.blastp.detail.filtered.out ```
## Filtering using seqkit and grep outputting a fasta file
``` seqkit grep --pattern-file XP_001619085.2.blastp.detail.filtered.out ~/data/blast/allprotein.fas > XP_001619085.2.blastp.detail.filtered.fas ```
## Produce an alignment of multiple sequences with their percent identities
```muscle -in XP_001619085.2.blastp.detail.filtered.fas -out XP_001619085.2.blastp.detail.filtered.aligned.fas ```
## Removes all highly gapped sequences where gapped residues > 50%
``` t_coffee -other_pg seq_reformat -in XP_001619085.2.blastp.detail.filtered.aligned.fas -action +rm_gap 50 -out allhomologs.aligned.r50.fa ```
## A Rudimentary tree produced in Iqtree 
``` iqtree -s allhomologs.aligned.r50.fa -nt 2 ```
## Rerooted via the midpoint
``` gotree reroot midpoint -i XP_001619085.2.blastp.detail.filtered.aligned.fas.treefile -o XP_001619085.2.blastp.detail.filtered.aligned.fas.midpoint.treefile ```






## This command tells us to combine the treefiles two files into one, alternative trees file.
``` cat XP_001619085.2.r50.ufboot.treefile XP_001619085.2.r50.ufboot.unrooted.treefile.rearrange > XP_001619085.r50.alternativetrees ```
## This command produces our second optimal tree with the  -z flag signifying to use this file. The -au flag tells iqtree to run an approximately unbiased topological test. the -zb flag tells iqtree to run X amount of bootstrap replicas for the approximately unbiased test, here it is 10,000. The -te flag specifies which file iqtree will use to set model parameters.
``` iqtree -s XP_001619085.2.blastp.detail.filtered.aligned.fas -z XP_001619085.r50.alternativetrees -au -zb 10000 --prefix DSCAM_altTrees -m LG+F+R5 -nt 2 -te XP_001619085.2.r50.ufboot.treefile ```
