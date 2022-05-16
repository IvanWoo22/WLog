# split
`split` followed by `//` will work fine.
```perl5
my @tmp = split /\./, $ARGV[0];
print(join("\t",@tmp));
```
While, `split` followed by `""` won't work.
```perl5
my @tmp = split "\.", $ARGV[0];
print(join("\t",@tmp));
```