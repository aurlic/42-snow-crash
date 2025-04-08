We will solve this level by directly interfering with `getflag`'s behaviour.
We decompile the binary using Ghidra and notice the following:
- A `ptrace(PTRACE_TRACEME, ...)` check is used to detect debuggers.
- If debugging is detected, the program prints:
  ```
  You should not reverse this
  ```
- The program checks `getuid()` and prints a flag only if the UID matches one of several hardcoded values.
- The UID of `flag14` (which we confirmed using `id flag14`) is `3014` (in hex: `0x0bc6`).

Now let's run the binary in GDB:
```bash
gdb /bin/getflag
```
Inside GDB:
```gdb
(gdb) run
```
Output:
```bash
You should not reverse this
```
The binary exits because it detects GDB through `ptrace`.

We catch the `ptrace` syscall and modify its return value:
```gdb
(gdb) catch syscall ptrace
Catchpoint 1 (syscall 'ptrace' [26])

(gdb) commands 1
Type commands for breakpoint(s) 1, one per line.
End with a line saying just "end".
>set ($eax) = 0
>continue
>end
```
This will fake the return value of `ptrace` to bypass anti-debugging.

We now restart execution and place a **breakpoint before the UID** is checked:
```gdb
(gdb) start
(gdb) b *0x08048b0a
Breakpoint 3 at 0x8048b0a
```

Then we step until we hit the breakpoint:
```gdb
(gdb) n
```
At this point, we modify the UID to match `flag14`:
```gdb
(gdb) set $eax = 3014
(gdb) n
```

Output:
```bash
Check flag.Here is your token : 7QiHafiNa3HVozsaXkawuYrTstxbpABHD8CPnHJ
```
We successfully fake the UID and got this way finish our last level !