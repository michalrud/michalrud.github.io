---
title: "BASH: Finding all hard links to a file in a given directory tree"
---

Question: How to find all hard links to one file in a given file and do something with them (count, replace with symbolic links, delete or whatever)? We need to use a `find` command. But there are some problems with it:

 * We can use `-links +1` to find all files, which link count value is bigger than 1 (knowing, that every file in \*nix is just a hard link to a given *inode*, we look for files that have more than one hard link). But that would give us files that have links **outside** the given directory tree as well.
 * We can use a `while` loop to check all files found using a `-links +1` approach using `find` with a `-samefile` switch, but that would be *really* slow when given a big directory tree.

Solution? We have to find every regular file (`-type f`) that have more than one hard link (`-links +1`). Cool, but `find` will print out those files in a rather random order. We need them sorted - so every link to a given inode would be next to each other. How to do that? `find` allows printing results not only in a simple way using `-print`, but gives us a more sophisticated way with `-printf`. Knowing that we can use `-printf "%i %p\n"` to print inode in front of the file path, and then use `sort` to achieve success.

## Example code looks like that:
```bash
#!/bin/bash
find $1 -type f -links +1 -printf &quot;%i %p\n&quot; | sort | while read a
do
    # $a == currently checked line
    inode=`echo $a | cut -d' ' -f1`
    path=`echo $a | cut -d' ' -f2`
done
```

Now, if current `$inode` differs from the one found in previous loop iteration, then we can be sure that we won't find another link to that file. So, we have to add a little modification - for example, a global variable with last inode, and everything is cool again..

## Why while and not for?

Difference is simple, but important. In the case of that loop:

```bash
for i in *
do
    # do something
done
```

whole `*` lands in the command line. It may seem unimportant, but when there are a lot of files in `*` it may be a problem - length of a command line is limited after all. Let's look at the `while`:

```bash
find $1 -print | while read variable
do
    # do something
done
```

Looks more complicated, but it's better. Here `find` result is not passed as a command line argument, but uses a pipeline to send it to `while`. Knowing, that pipeline length is practically unlimited, we are avoiding the `for` problem.

## See also:

 * [Manual page for `find`](http://manpages.debian.net/cgi-bin/man.cgi?query=FIND&sektion=1&apropos=0&manpath=Debian+6.0+squeeze)
 * [Manual page for `cut`](http://manpages.debian.net/cgi-bin/man.cgi?query=cut&apropos=0&sektion=1&manpath=Debian+6.0+squeeze&format=html)
 * [Manual page for `sort`](http://manpages.debian.net/cgi-bin/man.cgi?query=sort&apropos=0&sektion=1&manpath=Debian+6.0+squeeze&format=html)