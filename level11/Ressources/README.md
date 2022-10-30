``` shell
ls -l
-rwsr-sr-x 1 flag11 level11 668 Mar  5  2016 level11.lua
```

Let's inspect given LUA script.
It's a server, that ask for password, hashes it with `sha1sum` and analyzes in some way:
``` lua
#!/usr/bin/env lua
local socket = require("socket")
local server = assert(socket.bind("127.0.0.1", 5151))

function hash(pass)
  prog = io.popen("echo "..pass.." | sha1sum", "r")
  data = prog:read("*all")
  prog:close()

  data = string.sub(data, 1, 40)

  return data
end


while 1 do
  local client = server:accept()
  client:send("Password: ")
  client:settimeout(60)
  local l, err = client:receive()
  if not err then
      print("trying " .. l)
      local h = hash(l)

      if h ~= "f05d1d066fb246efe0c6f7d095f909a7a0cf34a0" then
          client:send("Erf nope..\n");
      else
          client:send("Gz you dumb*\n")
      end

  end

  client:close()
end
```

We are not intereseted in the password analysis.
We will try to exploit system call to `sha1sum`:
``` lua
prog = io.popen("echo "..pass.." | sha1sum", "r")
```

We see that we can pass any string into this call.
So lets pass a string with commands that save token in some file:
`$(echo $(getflag) > /var/tmp/level11.txt; chmod 777 /var/tmp/level11.txt)`