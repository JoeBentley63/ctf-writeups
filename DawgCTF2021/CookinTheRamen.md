# Cookin the Ramen

```
Apparently we made cookin the books too hard, here's some ramen to boil as a warmup: .--- ...- ...- . ....- ...- ... ..--- .. .-. .-- --. -.-. .-- -.- -.-- -. -... ..--- ..-. -.-. ...- ...-- ..- --. .--- ... ..- .. --.. -.-. .... -- ...- -.- . ..- -- - . -. ...- -. ..-. --- -.-- --.. - .-.. .--- --.. --. --. ...-- ... -.-. -.- ..... .--- ..- --- -. -.- -..- -.- --.. -.- ...- ..- .-- - -.. .--- -... .... ..-. --. --.. -.- -..- .. --.. .-- ...- ... -- ...-- --.- --. ..-. ... .-- --- .--. .--- .....
```

# Research
Obviously, this is morse code. I went straight to [Cyber Chef](https://gchq.github.io/CyberChef) which is a great tool for pen testing and a valuable ally for CTF's

Putting it through `From Morse Code` gave us back

```
JVVE4VS2IRWGCWKYNB2FCV3UGJSUIZCHMVKEUMTENVNFOYZTLJZGG3SCK5JUONKXKZKVUWTDJBHFGZKXIZWVSM3QGFSWOPJ5
```

Which obviously isn't the flag.

There isn't any immediately obvious characteristics about this cipher text to me

- It does have obvious punctuation, or clear word breaks (we are looking for a flag, so no big deal)
- There is no key or similar

Whenever I'm faced with an unknown cipher text, I try out `Magic` in Cyber Chef which will try guess by running a tonne of operations together, showing you the output and Entropy.

# Result

Turns out the text was:

- Base32
- Base64
- Base58

Reversing the ops gives us
`DawgCTF{0k@y_r3al_b@by's_f1r5t}`
