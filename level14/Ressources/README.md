``` shell
ls -l
```

Nothing files. No open sockets. No processes. No mails. Nothing...

Last thing we can try is to exploit `getflag`...

Firstly lets's disassemble it.
We see following lines:
```
 ...
 89 04 24                               mov    %eax,(%esp)
 8048be9:       e8 d2 f8 ff ff          call   80484c0 <fwrite@plt>
 8048bee:       e9 3c 02 00 00          jmp    8048e2f <main+0x4e9>
 8048bf3:       a1 60 b0 04 08          mov    0x804b060,%eax
 8048bf8:       89 c3                   mov    %eax,%ebx
 8048bfa:       c7 04 24 b2 90 04 08    movl   $0x80490b2,(%esp)
 8048c01:       e8 fe f9 ff ff          call   8048604 <ft_des>
 8048c06:       89 5c 24 04             mov    %ebx,0x4(%esp)
 8048c0a:       89 04 24                mov    %eax,(%esp)
 8048c0d:       e8 1e f9 ff ff          call   8048530 <fputs@plt>
 8048c12:       e9 18 02 00 00          jmp    8048e2f <main+0x4e9>
 8048c17:       a1 60 b0 04 08          mov    0x804b060,%eax
 8048c1c:       89 c3                   mov    %eax,%ebx
 8048c1e:       c7 04 24 cc 90 04 08    movl   $0x80490cc,(%esp)
 8048c25:       e8 da f9 ff ff          call   8048604 <ft_des>
 8048c2a:       89 5c 24 04             mov    %ebx,0x4(%esp)
 8048c2e:       89 04 24                mov    %eax,(%esp)
 8048c31:       e8 fa f8 ff ff          call   8048530 <fputs@plt>
 8048c36:       e9 f4 01 00 00          jmp    8048e2f <main+0x4e9>
 8048c3b:       a1 60 b0 04 08          mov    0x804b060,%eax
 ...
```

Maybe (encoded) passwords are again stored inside the binary?
Let's see:
``` shell
strings /bin/getflag
...
I`fA>_88eEd:=`85h0D8HE>,D
7`4Ci4=^d=J,?>i;6,7d416,7
<>B16\AD<C6,G_<1>^7ci>l4B
B8b:6,3fj7:,;bh>D@>8i:6@D
?4d@:,C>8C60G>8:h:Gb4?l,A
G8H.6,=4k5J0<cd/D@>>B:>:4
H8B8h_20B4J43><8>\ED<;j@3
78H:J4<4<9i_I4k0J^5>B1j`9
bci`mC{)jxkn<"uD~6%g7FK`7
Dc6m~;}f8Cj#xFkel;#&ycfbK
74H9D^3ed7k05445J0E4e;Da4
70hCi,E44Df[A4B/J@3f<=:`D
8_Dw"4#?+3i]q&;p6 gtw88EC
boe]!ai0FB@.:|L6l@A?>qJ}I
...
```

Yes! So we can debug the binary again to try get the password.
Let's try to do it with `gdb`:
``` shell
gdb getflag
...
(gdb) run
Starting program: /bin/getflag 
You should not reverse this
...
```

How do they know... Let's inspect:
```
   0x08048989 <+67>: call   0x8048540 <ptrace@plt>
   0x0804898e <+72>: test   eax,eax
   0x08048990 <+74>: jns    0x80489a8 <main+98>
   0x08048992 <+76>: mov    DWORD PTR [esp],0x8048fa8
   0x08048999 <+83>: call   0x80484e0 <puts@plt>
```

They use `ptrace` to check if the process is being debugged... ;(

However, they is an exploit for it!
https://gist.github.com/poxyran/71a993d292eee10e95b4ff87066ea8f2

After applying this exploit we can freely debug the binary.
We need to change `eax` after `getuid` was called:
```
   0x08048afd <+439>:   call   0x80484b0 <getuid@plt>
   0x08048b02 <+444>:   mov    DWORD PTR [esp+0x18],eax
   0x08048b06 <+448>:   mov    eax,DWORD PTR [esp+0x18]
```