---
created: 2023-08-30 09:59
updated: 2023-08-30 10:56
---
---
**Links**: [[104 Linux Index]]

---
## GNU - Parallel
- Example command:
	- Only 4 at a time.

```bash
cat img.lst | parallel -j 4 wget {}
```

- Items from piping are put at the end of the command but we might want to put it else where, this is where we use `{}`
	- For example `seq 20 | parallel echo` is same as `seq 20 | parallel echo {}`
- If we want to use the input at some other position we can do something like this:
	- `seq 20 | parallel echo  "what you {} doing"`