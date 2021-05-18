# Show us your ID | Misc 200

`Analysis of a PDF file. Beware of rabbit holes.`

# Investigation
My first port of call with a PDF file, is always to open and look for redacted text or attachments. This PDF was just a picture of Nyan cat and nothing obvious.

![Nyan](screenshots/nyan-1_1.jpg)

Next, I ran basically every PDF tool from [Kali linux](https://tools.kali.org/tools-listing), along side `zsteg` on a quest for hidden data. Nothing.

 # The solve
 I decided to open the PDF in Atom, and do some `cmd + f`ing around. I first searched `dctf` since the flag begins with that. Nothing

 Fresh from solving [ReadMe](ReadMe.md), I also searched the hex for that:  `64637466`. I got a hit

 ```
 http://ns.adobe.com/xap/1.0/<?xpacket begin="" id="646374667b3362306261347d"
 ```

`646374667b3362306261347d` converts to the flag `dctf{3b0ba4}` which was correct.

Very sneaky, hidden in plain sight. Of course no tool would find it, it's an ID of a packet.
