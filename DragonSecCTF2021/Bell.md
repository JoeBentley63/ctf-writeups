# Bell | Rev 100

```Blaise's friends like triangles too!```

# Investigation
This CTF I tried out [Ghidra](https://ghidra-sre.org/), a reverse engineering tool built by the NSA. It comes packaged with a frankly magical decompiler, which turns machine code into very readable C. Coming from Radare2 and GDB, this was a game changer.

I opened the file in Ghidra and took a look at the decompiled code:
(with some renamed variables)
```
undefined8 process(int param_1)

{
  long lVar1;
  long triangle_number;
  long in_FS_OFFSET;
  int i;
  long expected_number;
  bool flag_condition;

  lVar1 = *(long *)(in_FS_OFFSET + 0x28);
  flag_condition = true;
  i = 1;
  while (i <= param_1) {
    triangle_number = triangle(param_1,i,i);
    __isoc99_scanf();
    if (triangle_number != expected_number) {
      flag_condition = false;
    }
    i = i + 1;
  }
  if (flag_condition) {
    system("cat flag.txt");
  }
  else {
    puts("Better luck next time.");
  }
  if (lVar1 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}
```

```
long triangle(int a,int b)

{
  long lVar1;
  long lVar2;

  if (a < b) {
    lVar1 = 0;
  }
  else {
    if ((a == 1) && (b == 1)) {
      lVar1 = 1;
    }
    else {
      if (b == 1) {
        lVar1 = triangle(a + -1,a + -1,a + -1);
      }
      else {
        lVar2 = triangle(a,b + -1,b + -1);
        lVar1 = triangle(a + -1,b + -1,b + -1);
        lVar1 = lVar1 + lVar2;
      }
    }
  }
  return lVar1;
}
```

# The Breakdown
The application would send you a number, this corresponds to the number of replies you need to specify.

Each number you enter would be compared against this triangle method, and if we kept getting it correct, then we get the flag.

# Solving

I took the triangle C code and built a small app locally to pass in 1 -> 12 along side the number the server sent me (12), and output the reply. By sending that after the prompt, we get the flag

```
nc dctf-chall-bell.westeurope.azurecontainer.io 5311
12
678570
794545
931667
1094076
1286789
1515903
1788850
2114719
2504665
2972432
3535027
4213597
dctf{f1rst_step_t0wards_b3ll_l4bs}
```
