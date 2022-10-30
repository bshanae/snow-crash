Let's check home directory:
``` shell
ls -la
-rwsr-sr-x 1 flag09  level09 7640 Mar  5  2016 level09
----r--r-- 1 flag09  level09   26 Mar  5  2016 token
```

We have a binary and text file.

The text file contains some strange symbols:
``` shell
cat -e token
f4kmm6p|=M-^B^?pM-^BnM-^CM-^BDBM-^CDu{^?M-^LM-^I$
```

The binary takes one argument (stirng).
Let's play with it to see what it does.

``` shell
./level09 hello
hfnos

./level09 abc
ace

./level09 aaa
abc

./level09 000
013

./level09 001
013
```

This algorithm seems to work like this:
``` c
encode(char *str)
{
	for (int i = 1; i < strlen(str))
	{
		str[i] += i;
	}
}
```

So, to decode we need to following:
``` c
decode(char *str)
{
	for (int i = 1; i < strlen(str))
	{
		str[i] -= i;
	}
}
```

We can write code that open token file and decodes it content.
This way we get the token.