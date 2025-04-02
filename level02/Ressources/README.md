We start this level with a simple `ls` command in the current folder and notice a file `level02.pcap`.

We copy it to our system using SCP:

`scp -P 4242 level02@192.168.56.101:/home/user/level02/level02.pcap ~/Downloads/`

And now let's analize it using WireShark.

We first open the file in the software, then follow the TCP Stream by doing `Analyze > Follow > TCP Stream`. 
We then start reading the info and find something pretty interesting:

```
Password: ft_wandr...NDRel.L0L
```

We try to put use this as our flag02 password but it doesn't work:

```
level02@SnowCrash:~$ su flag02
Password: 
su: Authentication failure
```

When showing the data as `Hex Dump` instead of `ASCII`, we see that the dots in the middle of the password are equal to the value of `DEL` (7f) and not to an actual dot:

```
    000000D6  00 0d 0a 50 61 73 73 77  6f 72 64 3a 20            ...Passw ord: 
000000B9  66                                                 f
000000BA  74                                                 t
000000BB  5f                                                 _
000000BC  77                                                 w
000000BD  61                                                 a
000000BE  6e                                                 n
000000BF  64                                                 d
000000C0  72                                                 r
000000C1  7f                                                 .
000000C2  7f                                                 .
000000C3  7f                                                 .
000000C4  4e                                                 N
000000C5  44                                                 D
000000C6  52                                                 R
000000C7  65                                                 e
000000C8  6c                                                 l
000000C9  7f                                                 .
000000CA  4c                                                 L
000000CB  30                                                 0
000000CC  4c                                                 L
```

Following this idea, the password would be `ft_waNDReL0L`, we try it and get our next flag !