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
