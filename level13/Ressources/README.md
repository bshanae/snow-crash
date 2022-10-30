``` shell
ls -l
-rwsr-sr-x 1 flag13 level13 7303 Aug 30  2015 level13
```

Let's run the binary:
``` shell
./level13
UID 2013 started us but we we expect 4242
```

It expects a specific UID. We can't change our UID.

Let's disassemble te binary to see what it does:
```
   0x0804858c <+0>: push   ebp
   0x0804858d <+1>: mov    ebp,esp
   0x0804858f <+3>: and    esp,0xfffffff0
   0x08048592 <+6>: sub    esp,0x10
   0x08048595 <+9>: call   0x8048380 <getuid@plt>
   0x0804859a <+14>:  cmp    eax,0x1092
   0x0804859f <+19>:  je     0x80485cb <main+63>
   0x080485a1 <+21>:  call   0x8048380 <getuid@plt>
   0x080485a6 <+26>:  mov    edx,0x80486c8
   0x080485ab <+31>:  mov    DWORD PTR [esp+0x8],0x1092
   0x080485b3 <+39>:  mov    DWORD PTR [esp+0x4],eax
   0x080485b7 <+43>:  mov    DWORD PTR [esp],edx
   0x080485ba <+46>:  call   0x8048360 <printf@plt>
   0x080485bf <+51>:  mov    DWORD PTR [esp],0x1
   0x080485c6 <+58>:  call   0x80483a0 <exit@plt>
   0x080485cb <+63>:  mov    DWORD PTR [esp],0x80486ef
   0x080485d2 <+70>:  call   0x8048474 <ft_des>
   0x080485d7 <+75>:  mov    edx,0x8048709
   0x080485dc <+80>:  mov    DWORD PTR [esp+0x4],eax
   0x080485e0 <+84>:  mov    DWORD PTR [esp],edx
   0x080485e3 <+87>:  call   0x8048360 <printf@plt>
   0x080485e8 <+92>:  leave  
   0x080485e9 <+93>:  ret  
```

We see a check for value 4242 (1092 in hex):
```
0x0804859a <+14>:  cmp    eax,0x1092
```

If check was successful, program call some `ft_des` with string `boe]!ai0FB@.:|L6l@A?>qJ}I`.

This string looks like an token encoded with DES.
However, if we wan't to decode it we need a secret key.
I couldn't find it in binary...

Let's try another way.
We can simply debug the binary and change `eax` to pass the UID check.

*I won't show the GDB workflow here, but it works out :)*

