In home directory we see two executables.
One is a binary, second is a PHP file.

Let's inspect the PHP file:
```php
#!/usr/bin/php
<?php
function y($m)
{
  $m = preg_replace("/\./", " x ", $m);
  $m = preg_replace("/@/", " y", $m);

  return $m;
}

function x($y, $z)
{
  $a = file_get_contents($y);

  $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);
  $a = preg_replace("/\[/", "(", $a);
  $a = preg_replace("/\]/", ")", $a);

  return $a;
}

$r = x($argv[1], $argv[2]);
print $r;
?>

```

`$a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a);`
Firstly, the `/e` modifier in `preg_replace` call is a possibility for exploit.
This modifier allows to execute code pasted into the first argument string.

We see that any string inside `[x ...]` will be got pasted into `y("...")` and that string is getting executed.
So we can paste anything in `y("...")`.

To execute code inside string we should use complex variable parsing inside strings.
In PHP manuals we find example `"{${getName()}}"`.
Here function `getName` gets executed, retuned string is used as variable name.

We can try to paste something like this `{${exec_shell("getflag")}}`.
This won't work, because double quotes are pasted incorrectly.

We can try another way: `{${``getflag``}}`.
This way the string will be passed correctly.

Firstly, `getflag` will be executed.
Secondly, returned string will be parsed as variable name.
The variable won't be found, but the returned string will be displayed inside a warning.

**Resources**
http://www.madirish.net/402
https://www.php.net/manual/en/language.types.string.php#language.types.string.parsing.complex
https://www.php.net/manual/en/language.operators.execution.php