A fast way...

to length distribution of `FASTQ` files:

```shell
pigz -dc INPUT.fq.gz |
  awk 'NR%4 == 2 {lengths[length($0)]++} END{for(l in lengths){print l, lengths[l]}}'

```

to density of a column:

```shell
awk '{h[sprintf("%.2f",$6)]++}END{for (i in h){print i,h[i]}}' infile |
  sort -nrk 1,1

```

to extract some terms in `gff` files

```shell
pigz -dc gencode.v40.annotation.gff3.gz |
  awk 'BEGIN{FS="\t"}{split($9,a,";");
    if($3~"gene") print a[1]"\t"a[4]}' | sed 's/ID=//' |
  sed 's/gene_name=//' \
    >gencode.v40_gene_annotation_table.txt
```