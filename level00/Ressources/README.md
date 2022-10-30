Find a file owned by user flag00:
```
level00@SnowCrash:~$ find / -user flag00 2>/dev/null
/usr/sbin/john
```

Open the file:
```
level00@SnowCrash:~$ cat /usr/sbin/john
cdiiddwpgswtgt
```

Try to login into flag00:
```
level00@SnowCrash:~$ su flag00
Password: cdiiddwpgswtgt
su: Authentification failure
```

The password seems to be encrypted.

Try to decrypt it on `dcode.fd`. Decrypted password is:
```
nottoohardhere
```