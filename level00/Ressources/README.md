To find this flag we're first going to check which files we can currently access.

`find / -user flag00 2>/dev/null`

Looking through the list we notice a file that we decide to inspect.

`cat /usr/sbin/john`

In it there's a string that we're going to try to decode.

`cdiiddwpgswtgt` using https://www.dcode.fr/caesar-cipher we find that it's rot15 Caesar -> `nottoohardhere`

We then try to log in using this password `su flag00`.
And get the flag with `getflag`.