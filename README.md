# Final-project-repository
## Download the XP_001619085.2 protein from the NCBI database
``` ncbi-acc-download -F fasta -m protein XP_001619085.2 ``` 
## BLAST against potential homologs
```blastp -db ~/data/blast/allprotein.fas -query XP_001619085.2.fa -outfmt 0 -max_hsps 1 > XP_001619085.2.blastp.typical.out ```
## Find potential homologs with an e-score lower than 1e-11
``` awk '{if ($6<0.00000000001)print $1 }' XP_001619085.2.blastp.detail.out > XP_001619085.2.blastp.detail.filtered.out ```
## Filtering using seqkit and grep outputting a fasta file
``` seqkit grep --pattern-file XP_001619085.2.blastp.detail.filtered.out ~/data/blast/allprotein.fas > XP_001619085.2.blastp.detail.filtered.fas ```
## Produce an alignment of multiple sequences with their percent identities
```muscle -in XP_001619085.2.blastp.detail.filtered.fas -out XP_001619085.2.blastp.detail.filtered.aligned.fas ```
## Removes all highly gapped sequences where gapped residues > 50%
``` t_coffee -other_pg seq_reformat -in XP_001619085.2.blastp.detail.filtered.aligned.fas -action +rm_gap 50 -out allhomologs.aligned.r50.fa ```
## A Rudimentary tree produced in Iqtree with bootstrap support of 1000 replicates 
``` iqtree -s XP_001619085.2.blastp.detail.filtered.aligned.fas -bb 1000 -nt 2 --prefix XP_001619085.2.r50.ufboot ```
## Rerooted tree via the midpoint using Gotree
```gotree reroot midpoint -i XP_001619085.2.r50.ufboot.treefile -o XP_001619085.2.r50.ufboot.midpoint.treefile ```
## Reconciled tree produced in notung
```java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -b XP_001619085.2.r50.ufboot.midpoint.treefile.reconciled --reconcile --speciestag prefix --savepng --treestats --events --homologtabletabs --phylogenomics python ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g XP_001619085.2.r50.ufboot.midpoint.treefile.reconciled --include.species ```
## Put tree in newick format to begin formation of alternative tree
```java -jar ~/tools/Notung-3.0-beta/Notung-3.0-beta.jar -g XP_001619085.2.r50.ufboot.midpoint.treefile.reconciled   -s species.tre --reconcile --speciestag prefix  --treeoutput newick --nolosses ```
## Unroot tree for alternative tree
```gotree unroot -i XP_001619085.2.r50.ufboot.midpoint.treefile.rearrange.0.reconciled.reconciled -o XP_001619085.2.r50.ufboot.unrooted.treefile.rearrange```
## This command tells us to combine the treefiles two files into one, alternative trees file.
``` cat XP_001619085.2.r50.ufboot.treefile XP_001619085.2.r50.ufboot.unrooted.treefile.rearrange > XP_001619085.r50.alternativetrees ```
## This command produces our second optimal tree with the  -z flag signifying to use this file. The -au flag tells iqtree to run an approximately unbiased topological test. the -zb flag tells iqtree to run X amount of bootstrap replicas for the approximately unbiased test, here it is 10,000. The -te flag specifies which file iqtree will use to set model parameters.
``` iqtree -s XP_001619085.2.blastp.detail.filtered.aligned.fas -z XP_001619085.r50.alternativetrees -au -zb 10000 --prefix DSCAM_altTrees -m LG+F+R5 -nt 2 -te XP_001619085.2.r50.ufboot.treefile ```
## IPRSCAN of unaligned protein sequence to obtain domains found in the sequence
```iprscan5   --email jeremy.diaz@stonybrook.edu  --multifasta --useSeqId --sequence   XP_001619085.2.blastp.detail.filtered.fas```
## Concatonate all .tsv files created from IPRSCAN to a single file
```cat *.tsv.txt > XP_001619085.2.domains.all.tsv```
## Filtering for only domains defined by the Pfam database
```grep Pfam XP_001619085.2.domains.all.tsv > XP_001619085.2.domains.pfam.tsv```
##  Rearrangement of IPRSCAN output to use Evolview to map domains next to their corresponding protein sequence 
```awk 'BEGIN{FS="\t"} {print $1"\t"$3"\t"$7"@"$8"@"$5}' XP_001619085.2.domains.pfam.tsv | datamash -sW --group=1,2 collapse 3 | sed 's/,/\t/g' | sed 's/@/,/g' > XP_001619085.2.domains.pfam.evol.tsv```

