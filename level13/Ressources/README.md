We start by listing the files in the current directory:
```bash
ls -la
```
Output:
```bash
total 16
dr-xr-x---+ 1 level13 level13  120 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level13 level13  220 Apr  3  2012 .bash_logout
-r-x------  1 level13 level13 3518 Aug 30  2015 .bashrc
-r-sr-x---  1 flag13 level13  552 Mar  5  2016 level13
-r-x------  1 level13 level13  675 Apr  3  2012 .profile
```

We notice the `level13` binary is owned by `flag13` and has the SUID bit set. This means we can execute it with `flag13`'s privileges.

Trying to run it directly:
```bash
./level13
```
Output:
```bash
UID 2013 started us but we we expect 4242
```

The binary checks if the current UID is 4242 (`0x1092` in hex). Since we don't have this UID, it exits.

Let's inspect the binary using `gdb`:
```bash
gdb ./level13
```
Inside GDB:
```gdb
b main
r
```
We break at the beginning of `main`. Then disassemble it:
```gdb
disas main
```
We observe this portion:
```asm
0x08048595 <+9>:    call   0x8048380 <getuid@plt>
0x0804859a <+14>:   cmp    $0x1092,%eax
0x0804859f <+19>:   je     0x80485cb <main+63>
```
This is where the binary checks the UID. We can **bypass this check** by forcing the jump to always be taken.*

We patch the instruction at address `0x0804859a` (the `cmp`) to `nop` out the comparison and force the jump to be always taken:

```gdb
set {unsigned char}0x0804859a = 0xeb
```
This replaces `cmp` with a `jmp` (unconditional jump).

Then continue the program:
```gdb
c
```

And get our next flag !