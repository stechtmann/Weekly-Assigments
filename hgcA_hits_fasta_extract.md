## Pipeline for HgcA identification from SCG. You just have to modify the input files for the MetaG_T.faa

1. Merging sequences together

```{BASH}
cat *proteins.faa > SCG.faa
```
2. Make sure the combined fasta is a one line fasta
```{BASH}
awk '!/^>/ { printf "%s", $0; n = "\n" } /^>/ { print n $0; n = "" } END { printf "%s", n }' SCG.faa > SCG_one_line.faa
```
3. Run HMM on SCG data
```{BASH}
hmmsearch -E 1e-50 --tblout scg-hgcA-hits.out hgcA.hmm SCG_one_line.faa
```
4. Process output into a CSV
- Make sure you're in the directory with the output and your output ends in .out
```{BASH}
bash ~/data/HgcAB/hmmscan_rough_parse.sh scg-hgcA-hits-out.csv
```
5. extract the names of the the hits from parsed output
```{BASH}
cut -d , -f 1 scg-hgcA-hits-out.csv > scg-names.txt
```
6. Make fasta of hits

```{BASH}
for hit in $(cat scg-names.txt); do grep -A 1 $hit SCG_one_line.faa ; done>scg-hits.fasta
```

7. Extract sequences for other sequences on the contig
```{BASH}
cut -d '_' -f 1:2 scg-names.txt > scg-contigs.txt
for hit in $(cat scg-contigs.txt); do grep -A 1 $hit SCG_one_line.faa ; done>scg-contig_his.fasta
```

## Pipeline for HgcB identification

1. Extract hit from hmm output from MetaG_T HgcA
```{BASH}
bash ~/data/HgcA/hmmscan_rough_parse2.sh MetaG_T-hgcA-hits.out MetaG_T-hgcA-hits-out.csv
```

2. extract the names of the the MetaG/MetaT HgcA hits from parsed output
```{BASH}
cut -d , -f 1 MetaG_T-hgcA-hits-out.csv > MetaG_T-names.txt
```
3. Extract all sequences on HgcA containing contigs
```{BASH}
for hit in $(cat MetaG_T-names.txt); do grep $hit ~/data/HgcAB/Maddy_gff/MetaG_T.gff|cut -d$'\t' -f 1 ; done > Contig_ID_of_HgcA_contigs.txt
for hit in $(cat MetaG_T-names.txt); do echo $hit; grep -c $hit ~/data/HgcAB/Maddy_gff/MetaG_T.gff; done > Count_of_genes_on_HgcA_contigs.txt
for hit in $(cat Contig_ID_of_HgcA_contigs.txt); do grep $hit ~/data/HgcAB/Maddy_gff/MetaG_T.gff|cut -d$'\t' -f 9| cut -d ';' -f 2 | cut -d '=' -f 2 ; done > MetaG_T_gene_ID_HgcA-contigs_hits.txt
for hit in $(cat MetaG_T_gene_ID_HgcA-contigs_hits.txt); do grep -A 1 $hit MetaG_T_one_line.faa ; done>MetaG_T-contig_hits.fasta
```
2. Search HgcA containing contigs for HgcB
```{BASH}
hmmsearch -E 1e-50 --tblout MetaG_T-hgcB-hits.out hgcB.hmm MetaG_T-contig_hits.fasta
```
4. Process output into a CSV
- Make sure you're in the directory with the output and your output ends in .out
```{BASH}
bash ~/data/HgcA/hmmscan_rough_parse2.sh MetaG_T-hgcB-hits.out MetaG_T-hgcB-hits-out.csv
```
5. extract the names of the the hits from parsed output
```{BASH}
cut -d , -f 1 MetaG_T-hgcB-hits-out.csv > MetaG_T-HgcB-names.txt
```
3. Extract HgcB hits from contigs
```{BASH}
for hit in $(cat MetaG_T-HgcB-names.txt); do grep -A 1 $hit MetaG_T-contig_hits.fasta ; done>MetaG_T-HgcB-hits.fasta
```


## Other CODE


1. Save file as a CSV and move to server

1. extract the names of the the hits
```{BASH}
cut -d , -f 1 hgcA_hits_IMG_faa_data.csv > names.txt
```

3. Make sure the combined fasta is a one line fasta
```{BASH}
awk '!/^>/ { printf "%s", $0; n = "\n" } /^>/ { print n $0; n = "" } END { printf "%s", n }' combined.fasta > combined_one_line.fasta
```
4. Make fasta of hits

```{BASH}
for hit in $(cat names.txt); do grep -A 1 $hit combined_one_line.fasta ; done>hits.fasta
```
