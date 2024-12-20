# Tricks

## Perl split

`split` followed by `//` will work fine.

```perl5
my @tmp = split /\./, $ARGV[0];
print(join("\t", @tmp));
```

While, `split` followed by `""` won't work.

```perl5
my @tmp = split "\.", $ARGV[0];
print(join("\t", @tmp));
```

## Stroke size in R & Illustrator

The thickness scale of lines in R is different from the scale in general operation.

```r
ggplot2::.pt
# [1] 2.845276
```

Therefore, when using R for visualization, you need to refer to the following comparison table.

| Size in R | Point in Illustrator |
|:---------:|:--------------------:|
|     1     |       2.141959       |
| 0.9337247 |          2           |
| 0.7002935 |         1.5          |
|    0.5    |       1.07098        |
| 0.4668623 |          1           |
| 0.2334312 |         0.5          |
| 0.1634018 |         0.35         |
| 0.1167156 |         0.25         |

---

---

# Tips

## Find the largest file

The procedure to find the largest files including directories in Linux is as follows:

- Open the terminal application
- Login as root user using the `sudo -i` command
- Type `du -a /dir/ | sort -n -r | head -n 20`
- du will estimate file space usage
- sort will sort out the output of du command
- head will only show top 20 largest file in /dir/
- Also check the `du` command to get the largest files in directory

Run the command:

```shell
sudo du -a * | sort -rn | head -n 20
```

## Find all kind of bio-type in `GFF3`

```shell
grep -o 'biotype=[^;]*;' Arabidopsis_thaliana.TAIR10.58.gff3 \
	| cut -d '=' -f 2 | cut -d ';' -f 1 \
	| sort | uniq
```

## Read input from clipboard in `Rstudio`

There will be times when you get some fresh data that is not particularly large that you want to graph through R
directly.

So, you can perform as shown:

```r
## tmp is your clipboard content.
tmp <- "	LC109	LC1173	LC123	LC1501	LC166	LC1671	LC185	LC192	LC193	LC194
10	5845678	5447297	5608682	5730609	5592508	5574256	5848625	5704038	5671094	5743587
15	5481512	5013117	5233426	5347440	5187061	5149794	5468576	5306342	5270447	5372153
20	5190391	4640099	4922449	5032642	4845818	4790548	5165080	4978950	4938863	5072444"

## do read.csv like readin a file.
df <- read.csv(
  textConnection(object = tmp),
  header = T,
  row.names = 1,
  sep = "\t",
  stringsAsFactors = FALSE
)
```

## Length distribution of `FASTQ`

```shell
pigz -dc INPUT.fq.gz |
  awk 'NR%4 == 2 {lengths[length($0)]++} END{for(l in lengths){print l, lengths[l]}}'

```

## Calculate density of a column

```shell
awk '{h[sprintf("%.2f",$6)]++}END{for (i in h){print i,h[i]}}' infile |
  sort -nrk 1,1

```

## Extract some terms in `GFF3`

```shell
pigz -dc gencode.v40.annotation.gff3.gz |
  awk 'BEGIN{FS="\t"}{split($9,a,";");
    if($3~"gene") print a[1]"\t"a[4]}' | sed 's/ID=//' |
  sed 's/gene_name=//' \
    >gencode.v40_gene_annotation_table.txt
```

## Substitute a file by a dictionary

```shell
awk 'NR==FNR{a[$1]=$2;next} {for(i in a) gsub(i, a[i])}1' \
	dictionary.tsv target.tsv \
	>substituted.tsv
```