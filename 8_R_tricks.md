### Stroke size in R & Illustrator

The thickness scale of lines in R is different from the scale in general operation.

```r
> ggplot2::.pt
[1] 2.845276
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

### How to read input from clipboard in Rstudio(remote)

There will be times when you get some fresh data that is not particularly large that you want to graph through R
directly.

So, you can perform as shown:

```r
## tmp is your clipboard content.
tmp <- "	LC109	LC1173	LC123	LC1501	LC166	LC1671	LC185	LC192	LC193	LC194
10	5845678	5447297	5608682	5730609	5592508	5574256	5848625	5704038	5671094	5743587
15	5481512	5013117	5233426	5347440	5187061	5149794	5468576	5306342	5270447	5372153
20	5190391	4640099	4922449	5032642	4845818	4790548	5165080	4978950	4938863	5072444
25	4935274	4292651	4642388	4750270	4535318	4455697	4897707	4681822	4636813	4806952
30	4699481	3958641	4376873	4484899	4238183	4131744	4649326	4401311	4347687	4557447
35	4475203	3633012	4120187	4229293	3947857	3812923	4412817	4129428	4062698	4317785
40	4258808	3316625	3869099	3977343	3663135	3497795	4183798	3862465	3777706	4084163
45	4046883	3010020	3622986	3730509	3383921	3186169	3959663	3600885	3493644	3854691
50	3838668	2715315	3380938	3488163	3112459	2881001	3738845	3343704	3210306	3628144"

## do read.csv like readin a file.
df <- read.csv(
  textConnection(object = tmp),
  header = T,
  row.names = 1,
  sep = "\t",
  stringsAsFactors = FALSE
)
```