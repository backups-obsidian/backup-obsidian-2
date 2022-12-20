---
created: 2022-12-16 22:09
updated: 2022-12-19 20:04
---
---
**Links**: [[104 Linux Index]]

---
## Bash strict mode
### [[Linux - Bash strict mode - set commands]]
### [[Linux - Bash strict mode - understanding IFS]]
### [[Linux - Bash strict mode - issues and solutions]]

## Summary
```bash
set -euo pipefail
IFS=$'\n\t' # mind the quotes and the dollar
# you may choose to not change the IFS.
```

- Running the scripts in debug mode: `bash -x script.sh`
	- If you want to use debug mode in scripts then use `set -x`

- Output of the following script in norma and debug mode
	- ![[attachments/Pasted image 20221219194622.png]]
	- It substitutes the variables for easy debugging.
	- `++` means subshell command.

```bash
test=$(pwd)

# I use the below to output in debug mode. echo creates too much noise.
: ======== $test =========
echo $test
```

## References
- [Bash Strict Mode (redsymbol.net)](http://redsymbol.net/articles/unofficial-bash-strict-mode/)
- [wick/bash-strict-mode.md at master · tests-always-included/wick (github.com)](https://github.com/tests-always-included/wick/blob/master/doc/bash-strict-mode.md)
- [bash "strict mode": -euo pipefail (beginner - intermediate) anthony explains #201 - YouTube](https://www.youtube.com/watch?v=9fSkygQ-ZjI)
- [why I use the colon (:) command (intermediate) anthony explains #505 - YouTube](https://www.youtube.com/watch?v=onkNf1AKSgg)
	- Print hack in debug mode.