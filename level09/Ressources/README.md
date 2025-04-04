We start by listing the files in the current directory:
```bash
ls
```
Output:
```bash
level09 token
```

When executing level09 we realize that it converts each char to itself + it's position:
```bash
./level09 aaaa
abcd
```

We're going to write a program that **reverses** this process:
```bash
nano /tmp/reverse.c
```
```c
#include <stdio.h>

int main(int argc, char **argv) {
  if (argc != 2) {
    printf("Usage: ./reverse [string_to_reverse]\n");
    return (1);
  }

  for (int i = 0; argv[1][i]; i++) {
    argv[1][i] -= i;
  }

  printf("Reversed string: %s\n", argv[1]);
  return (0);
}
```

Let's now run our program:
```bash
cd /tmp
gcc -std=c99 reverse.c
./a.out $(cat /home/user/level09/token)
```

This way we get a **password** that we use to log in flag09 an get our next flag !