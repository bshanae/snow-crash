Let look at file `/etc/passwd` where all linux passwords are store.

There is are a password for flag01:
```
flag01:42hDRfypTqqnw:3001:3001::/home/flag/flag01:/bin/bash
```

Let's try to decode it with 'dcode.fr'.
This doesn't work.

Let's John The Ripper:
```
echo 42hDRfypTqqnw > pwd
john --show pwd
```

We get this:
```
?:abcdefg

1 password hash cracked, 0 left
```

So, the result password is `abcdefg`.