# BBomb Phase 2

```
Can you help me find my lost key so I can read my string?

```

Continuing on from [Phase 1] (BBombPhase1.md), I took a look at the disassembly in R2

```
lea rdi, str._nCan_you_help_me_find_my_lost_key_so_I_can_read_my_string_    ; 0x2960 ; "\nCan you help me find my lost key so I can read my string?"                 |
|             0x0000123e      e85df9ffff     call sym.imp.puts       ;[1]                                                                                                                                         |
|             0x00001243      c745d8010000.  mov dword [rbp - 0x28], 1                                                                                                                                            |
|             0x0000124a      488d054a1700.  lea rax, str.Dk52m6WZw_s6w0dIZh_2m5a    ; 0x299b ; "Dk52m6WZw@s6w0dIZh@2m5a"                                                                                         |
|             0x00001251      488945e0       mov qword [rbp - 0x20], rax                                                                                                                                          |
|             0x00001255      be01000000     mov esi, 1                                                                                                                                                           |
|             0x0000125a      bf29000000     mov edi, 0x29           ; ')'                                                                                                                                        |
|             0x0000125f      e8dcf9ffff     call sym.imp.calloc     ;[2]                                                                                                                                         |
|             0x00001264      488945e8       mov qword [rbp - 0x18], rax                                                                                                                                          |
|             0x00001268      488b55e8       mov rdx, qword [rbp - 0x18]                                                                                                                                          |
|             0x0000126c      488b45c8       mov rax, qword [rbp - 0x38]                                                                                                                                          |
|             0x00001270      4889d1         mov rcx, rdx                                                                                                                                                         |
|             0x00001273      488d15571600.  lea rdx, [0x000028d1]    ; "%s"                                                                                                                                      |
|             0x0000127a      4889c6         mov rsi, rax                                                                                                                                                         |
|             0x0000127d      bf02000000     mov edi, 2                                                                                                                                                           |
|             0x00001282      b800000000     mov eax, 0                                                                                                                                                           |
|             0x00001287      e8fefcffff     call sym.getInput       ;[3]             
```

You can pull out `Dk52m6WZw@s6w0dIZh@2m5a` which seems important. It doesn't look like any cipher I've seen, the output looks random. I put it through `Magic` in Cyber Chef which will try guess by running a tonne of operations together, showing you the output and Entropy.

With `intensive mode` enabled, I reviewed the output and found:

```

XOR({'option':'Hex','string':'5'},'Standard',false)
An07h3R_rEv3r5aL_mE7h0d

```

which was indeed the flag.
