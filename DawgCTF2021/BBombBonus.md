# BBomb Bonus

```
But wait... there's more!
```

Continuing on from [Phase 2] (BBombPhase2.md). This one I found by accident :D

I decided that if the last two phases involved manipulating strings stored in the binary, it might be worth it to extract all strings and to review.

This can be done in R2, by right clicking and choosing "Search strings in whole bin" and hitting enter on an empty search query.

```

| 0x27b7 6 5 AUATL                                                                   
| 0x2809 16 14 \b[]A\\A]A^A_Ðf.                                                    
| 0x2860 6 5 ERROR                                               
| 0x2866 21 20 Pr3e7Y_57E4ltHy_Fl4g               
| 0x287b 18 17 Phase %d answer:                         
| 0x2890 54 53 Truncating input to maximum allowed answer size                          
| 0x28c6 7 6 %*[^\n]                                        
| 0x28d8 113 112 \nStarting off easy... reversing (things) is

```

Scrolling down, `Pr3e7Y_57E4ltHy_Fl4g` stands out. Wrapping that in `DawgCTF{}` and bingo, that's the right answer 
