# Very Secure Website | Web 200

`Some students have built their most secure website ever. Can you spot their mistake?`


# Investigation

We get a login screen and a basic website, with the following:

`This is a very secure website, so we will also include the source code. Nobody will ever break it. It is the best.`

```
<?php
    if (isset($_GET['username']) and isset($_GET['password'])) {
        if (hash("tiger128,4", $_GET['username']) != "51c3f5f5d8a8830bc5d8b7ebcb5717df") {
            echo "Invalid username";
        }
        else if (hash("tiger128,4", $_GET['password']) == "0e132798983807237937411964085731") {
            $flag = fopen("flag.txt", "r") or die("Cannot open file");
            echo fread($flag, filesize("flag.txt"));
            fclose($flag);
        }
        else {
            echo "Try harder";
        }
    }
    else {
        echo "Invalid parameters";
    }
?>
```

I know from previous CTF's, that PHP is notorious for type juggling, and `==` is a dead giveaway. Both sides of the comparison look OK though...


# Experiments
[Doing some reading](https://www.programmersought.com/article/50963179676/), PHP sees any string starting with `0e` to mean scientific notation (`0 * 10 ^`), and has very poor comparison logic in this case.

Here are some examples I tried

```
'0e509367213418206700842008763514' == '0e481036490867661113260034900752'
true

'0e509367213418206700842008763514' == 'AAA'
false

'0e509367213418206700842008763514' == '0e0000000'
true
```

This is legit crazy to see. So if a hashing function (like MD5 or Tiger128,4) results starts with `0e`, we just bypass the check. Insane.


# The solve
Thankfully, [hash lookup tables exist](https://md5hashing.net/hash/tiger128,4) which revealed `51c3f5f5d8a8830bc5d8b7ebcb5717df` means `guest`. [Cieran](https://github.com/cieran) dug out `00e05651056780370631793326323796` from the site, which results in `479763000`.


Entering both those, and we get `dctf{It's_magic._I_ain't_gotta_explain_shit.}`
