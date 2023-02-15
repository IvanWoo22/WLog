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