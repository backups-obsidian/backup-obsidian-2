---
created: 2022-12-18 23:58
updated: 2022-12-18 23:59
---
---
**Links**:
- [[104 Linux Index]]
- [[Linux - Bash strict mode]]

---
## Understanding IFS
- The IFS variable - which stands for *Internal Field Separator* - **controls what Bash calls word splitting**.
	- When set to a string, each character in the string is considered by Bash to separate words.

```bash
IFS=$' '
items="a b c"
for x in $items; do
	echo "$x"
done

# Output:
# a
# b
# c

IFS=$'\n'
for y in $items; do
	echo "$y"
done

# Output:
# a b c
```

- For the first loop, IFS is a space, meaning that words are separated by a space character.
- For the second loop, words are separated by a _newline_, which means bash considers the whole value of items as a single word. 
- If *IFS is more than one character*, splitting will be done on _any_ of those characters.

- Changing IFS 

```bash
names=(
  "Aaron Maxwell"
  "Wayne Gretzky"
  "David Beckham"
  "Anderson da Silva"
)

echo "With default IFS value..."
for name in ${names[@]}; do
  echo "$name"
done

echo ""
echo "With strict-mode IFS value..."
IFS=$'\n\t'
for name in ${names[@]}; do
  echo "$name"
done

# Output:
# With default IFS value...
# Aaron
# Maxwell
# Wayne
# Gretzky
# David
# Beckham
# Anderson
# da
# Silva
#
# With strict-mode IFS value...
# Aaron Maxwell
# Wayne Gretzky
# David Beckham
# Anderson da Silva
```

> [!question]+ Why change the default IFS value?
> - If you invoke `myscript.sh notes todo-list 'My Resume.doc'`, then with the default IFS value, the third argument will be mis-parsed as two separate files - named "My" and "Resume.doc". When actually it's a file that has a space in it, named "My Resume.doc".
> - *Setting IFS to `$'\n\t'` means that word splitting will happen only on newlines and tab characters*. This very often produces useful splitting behaviour. 
> - By default, bash sets this to `$' \n\t'` - space, newline, tab - which is too eager. This means words are split on the basis of empty space rather than being split on newline or tab.
