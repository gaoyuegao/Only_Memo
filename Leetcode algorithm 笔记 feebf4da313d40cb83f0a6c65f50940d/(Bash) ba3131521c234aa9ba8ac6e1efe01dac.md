# (Bash)

## Read from the file words.txt and output the word frequency list to stdout.

```python
cat words.txt | tr -s ' ' '\n' | sort | uniq -c | sort -r | awk '{ print $2, $1 }'
```

In Bash, we use \ to escape next one trailing character;
^ is used to denote the beginning of a line
$ is used to denote the end of a line
{M} is used to denote to match exactly M times of the previous occurence/regex
(...) is used to group pattern/regex together
Back to this problem: it requires us to match two patterns, for better readability, I used -e and separate the two patterns into two regexes, the first one matches this case: xxx-xxx-xxxx and the second one matches this case: (xxx) xxx-xxxx

Please vote this post up if you find it helpful for your understanding!

```python
grep -e '\(^[0-9]\{3\}-[0-9]\{3\}-[0-9]\{4\}$\)' -e '\(^([0-9]\{3\})[ ]\{1\}[0-9]\{3\}-\([0-9]\{4\}\)$\)' file.txt
```

`head` and pipe with `tail` will be slow for a huge file. I would suggest `sed` like this:

```
sed 'NUMq;d' file

```

Where `NUM` is the number of the line you want to print; so, for example, `sed '10q;d' file` to print the 10th line of `file`.

Explanation:

`NUMq` will quit immediately when the line number is `NUM`.

`d` will delete the line instead of printing it; this is inhibited on the last line because the `q`causes the rest of the script to be skipped when quitting.

If you have `NUM` in a variable, you will want to use double quotes instead of single:

```
sed "${NUM}q;d" file
```

Print 10th line in file

```bash
# Read from the file file.txt and output the tenth line to stdout.
# Solution 1
cnt=0
while read line && [ $cnt -le 10 ]; do
  let 'cnt = cnt + 1'
  if [ $cnt -eq 10 ]; then
    echo $line
    exit 0
  fi
done < file.txt

# Solution 2
awk 'FNR == 10 {print }'  file.txt
# OR
awk 'NR == 10' file.txt

# Solution 3
sed -n 10p file.txt

# Solution 4
tail -n+10 file.txt|head -1
```