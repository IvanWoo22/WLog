### Linux find the largest file in directory recursively using find

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

### Find all kind of bio-type in GFF3 files.

```shell
grep -o 'biotype=[^;]*;' Arabidopsis_thaliana.TAIR10.58.gff3 \
	| cut -d '=' -f 2 | cut -d ';' -f 1 \
	| sort | uniq
```