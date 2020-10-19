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
