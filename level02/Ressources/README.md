In our home directory we'll see a `.pcap` file.

We need to transfer it on host machine.
This can be done with `scp`:
```
cd ~/Desktop
scp -P 4242 level02@[your vm ip]:~/level02.pcap .
```

Set read permission on the file:
```
chmod +r level02.pcap
```

Now let's open Wireshark.
Drag the in there.
Now click to `Analyze/Follow/TCP Stream`.

Here we see password field. The value is `ft_wandr...NDRel.L0L`. 
Let's inspect it with hexdump mode. We get this:
 66 | 74 | 5f | 77 | 61 | 6e | 64 | 72 | 7f | 7f | 7f | 4e | 44 | 52 | 65 | 6c | 7f | 4c | 30 | 4c
--- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | --
  f |  t |  _ |  w |  a |  n |  d |  r |  . |  . |  . |  N |  D |  R |  e |  l |  . |  L |  O |  L

`7f` is ASCII is `DEL`. So, if we apply these deletes, we' ll get `ft_waNDReLOL`.