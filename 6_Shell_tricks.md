### Linux find largest file in directory recursively using find

The procedure to find largest files including directories in Linux is as follows:

- Open the terminal application
- Login as root user using the `sudo -i` command
- Type `du -a /dir/ | sort -n -r | head -n 20`
- du will estimate file space usage
- sort will sort out the output of du command
- head will only show top 20 largest file in /dir/
- Also check the ncdu command to get largest files in directory

Run the command:
```shell
sudo du -a * | sort -rn | head -n 20
```