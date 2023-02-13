A fast way to length distribution of `FASTQ` files

```shell
pigz -dc INPUT.fq.gz |
  awk 'NR%4 == 2 {lengths[length($0)]++} END{for(l in lengths){print l, lengths[l]}}'
```