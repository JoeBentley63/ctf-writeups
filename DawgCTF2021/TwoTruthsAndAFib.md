# Two Truths and  a Fib
This was an simple enough challenge, you are given a netcat command which when ran,
gave the following output:

```
Welcome to two truths and a fib! You'll be given three numbers:
one of them will be a fibonacci number and two of them will not.
It's your job to tell which is which and send back the fibonacci.
Example:
12, 8, 4
Which number is a fib?
>> 8
Correct!


[100333703513099, 317811, 69734566644710]
>>
```

If you didn't enter the correct answer in ~5 seconds, you'd get back an error, and your connection would terminate.

```
Oof, too slow!
```

## Research
So this was in the `MISC` category, so I figured we'd have to just write automation to solve the questions. Answer enough, and maybe we'd get the flag.

Netcat is just an interactive shell to read and write network connections over UDP/TCP, so I started reading up on sockets in python. If I can create a socket and read/write, this should be easy enough

#### Sockets
[This Page](https://pequalsnp-team.github.io/cheatsheet/socket-basics-py-js-rb) was very useful for learning what to do, along side [this gist](https://gist.github.com/leonjza/f35a7252babdf77c8421)

I threw together some simple python to establish the connection and read/write

Connection:
```
self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
self.socket.connect((ip, port))
```

Read:
```
def read(self, length):
    return self.socket.recv(length)
```

Write:
```
def write(self, data):
    self.socket.send(data)
```

Python is such a great language for getting stuff like this prototyped, since in all it took ~5 minutes to automate the NC connection


#### Isolating the possible inputs
Once I had a connection, I had to parse out just the possible response. They appeared at the bottom in square brackets, comma seperated. Easy enough to pull out using a regex

```
output = response.decode("utf-8")
options = re.search(r"\[(.*?)\]", output).group(1).split(",")
```

[I really like this site](https://regexr.com/) for testing regex, it's very interactive and comes with tips on the side.


#### How to determine what is in the fibonacci sequence
Once I had the possible replies, we've to figure out which is correct. I don't have a background in maths, so I didn't know off hand any shortcuts to determine this.
Thankfully, smarter people have outlined a very simple [equation](https://math.stackexchange.com/questions/9999/checking-if-a-number-is-a-fibonacci-or-not)

Essentially:
```
(5 x (N Squared) + 4) or (5 x (N Squared) − 4) is a perfect square (no remainders once your square root).

```

That's very easy to codify

```
def is_fib(number):
	calculation = 5 * (number * number)
	return math.sqrt(calculation - 4).is_integer() or math.sqrt(calculation + 4).is_integer()
```


## All together

```
while True:
	output = nc.read_until(b"]\n>>").decode("utf-8")
	options = re.search(r"\[(.*?)\]", output).group(1).split(",")

	for potentialFib in options:
		if is_fib(int(potentialFib)):
			print (str(count) + " ~ sending " + potentialFib)
			nc.write(str.encode(potentialFib + "\n"))

```

After 100 correct replies, you get the flag

```Correct!

Congrats! Here's your flag: DawgCTF{jU$T_l1k3_w3lc0me_w33k}"
```
