# Return To School | Misc 250

```
APT403 taunts us with a challenge, the goal is to find the shortest path to solve the maze, don't let us down
```

Running netcat against the IP and port, we get back:

```
resolvez le chemain le plus cours
Example:
................................
.AAXXX XX   X XX   X  XXX X   X.
.XAXXX XX X      XXXX       XX .
.XAAAA XXX X XXX      XXXX XXX .
.X  XAXXXX   XXX XXX  XXX      .
.  X AXX   X X  X  X    XXX X X.
. XXXAXX XXXAAAAX   XX X    XXX.
.X   AAX  XAA XAA XXXXXX XX    .
.XX XXAXXXAAXXXXAA XX   X   XX .
.   XXAAAAAXXXXXXAXXX X X XXX  .
.X  XXX XX   X   AXXX XXXXXX  X.
.  XXX  XX XXXXX AXAAAA  XXX XX.
.XXX X  XX XXXXXXAAAXXAXXXX    .
.X      XXX  X   XXXXXA XX X XX.
.X XXX XXXXX   X X    AAXX    X.
.X  XX XX   X XX XXX XXAXXXX X .
.X  XX  X XX XX    X  XAXXXX X .
. X  XX    X  X X X  XXAXXXX X .
.   XXXXXX XX   X  XXX AXX XX  .
.XX   XXX     X XXXX XXAXAAAXX .
.X  XXXXX X XXX XXX    AAAXAAAA.
................................
vous devez répondre (ou equivalent)->EEENEESEENNNNNNENNNEEESEENNNNENENEEESESESEEEENNENNNNEEENNE

a votre tour
...
```

#### Investigation

Followed by a much much bigger maze. If you don't reply within 10 seconds the connection is terminated.

When it comes to these kinds of challenges, I generally assume the solution isn't to actually solve the maze,
but to find some exploit that skips to the end. This time, the answer was to just find the right series of moves to get from the bottom right to the top left.

Solving a maze like this is pretty straight forward, you can use either [A*](https://en.wikipedia.org/wiki/A*_search_algorithm) or [Dijkstra's](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm) to solve it pretty simply.

In essence, you solve a maze much like a chess game. You start at the initial state and understand the available options. You assess these options based on some kind of fitness function. A fitness function is basically just a measure of how good one move is relative to another, and gives you a way to measure success.

Starting at the bottom right of our example, we have 2 moves available [`Left` or `Up`]. An easy fitness function for mazes, is the [Manhattan Distance](https://en.wikipedia.org/wiki/Taxicab_geometry) from the destination.   

#### Setup

Let's create a simple class model in python to model our route.

``` python
class PathNode:

  def __eq__(self, other):
        return other.x == self.x and other.y == self.y

  def __init__(self, x, y, move, last):
        self.x = x
        self.y = y
        self.move = move
        self.last = last

  def manhattan(self, endX, endY):
      return abs(endX - self.x) + abs(endY - self.y)
```

Our class is very simple
- `x` - Our position on the X Axis of the maze
- `y` - Our position on the Y Axis of the maze
- `move` - What move got us here (used for back tracking)
- `last` - What PathNode came before us (essentially our path is a singly linked list)

I've also defined a custom `__eq__` which is pythons equals, since I only care about the X,Y when doing comparison operators later.

Lastly, `manhattan` just allows us to gauge the distance from this node to the end.

Next we need to know what actions are available at each PathNode, I did this through a very simple method that checks the neighboring character for `X` or `.`. We also need to keep within the bounds of the 2D Array.

``` python
def check_neighbour_free(posX, posY):
    return (posY < len(maze) and posX < len(maze[0])) #stay within bounds
    and (maze[posY][posX]!= 'X' and maze[posY][posX]!= '.') #stay away from walls
```

To let the algorithm know legal moves, I defined a simple class called `Direction`

``` python
class Direction:
    def __init__(self, x, y, move):
        self.x = x
        self.y = y
        self.move = move

directions = [Direction(0, -1, "N"),Direction(1, 0, "O"),Direction(0, 1, "S"),Direction(-1, 0, "E")]        
```

You'll notice `East` and `West` are mixed up, and also the directions are in French. That's part of the challenge.


#### Path Finding

We're going to solve this maze in a [Greedy](https://brilliant.org/wiki/greedy-algorithm/#:~:text=A%20greedy%20algorithm%20is%20a,to%20solve%20the%20entire%20problem.&text=However%2C%20in%20many%20problems%2C%20a,not%20produce%20an%20optimal%20solution.) fashion. We don't need to solve the entire maze at once, we just need to make the best decision at each stage of the maze.

Let's take our starting point of the bottom right. In the example we have 2 options, `Up` and `Left`. These moves are all we have, so we pick the best of the options (ie: Manhattan, which gets us closest.). We pick the best, and explore that option.

Both have the same outcome (we move 1 block closer), so let's go `Left`. Now we have 2 options, `Right` or `Left`. We came from the right, so there is no point exploring that way, so we need to keep track of our `seen` nodes in a list somewhere to avoid looping and waste. We add our new `Left` to our pool of options, with the initial `Up` we didn't explore from the start.

Each time we move, we link our current location to where we came from using the `last` variable, this is used for back tracking later.

Now all we have to do is loop until we get to our destination!

``` python
at_end = False
while not at_end:
    if location.x == endX and location.y == endY:
        return location
    else:
        for direction in directions:  #for every available direction
            potential = PathNode(location.x + direction.x, location.y + direction.y, direction.move, location)
            if potential not in seen and check_neighbour_free(potential.x, potential.y): #Is this a new route, and is it free
                possiblePaths.append(potential) #We've a new potential path

        next = sorted(possiblePaths, key=lambda x: x.manhattan(endX, endY))[0] #Pick the most valuable path

        possiblePaths.remove(next)
        seen.append(next)
        location = next

```

#### But how do I find the full path?
Once we get to the end, we just have a `PathNode` of our destination and only the last step before that. We've actually built a linked list along the way, so getting all the way back to the beginning is as simple as following our `.last` variable. The `.move` variable kept track of what direction(`N,E,S,O`) we moved to get here.

We can do this recursively:
``` python
def get_path_from_start(self):
    if self.last:
        return self.last.get_path_from_start() + self.move
    else:
        return ""
```

# Solving

```
SENDING EENENNONNENNNNNEENNONNENNNENEENENNNNNEEESEENEEESEESEESSSSSEENEEEEEEENNONONNENNNEEEEENEEEEESESSESEEESESEESSSSEESSSSSEESESSEENENNNNENEENENNNNONNENNNEENNNENEESSEESEEEENEEEENNOONNNNNNENNNEEEENENNNNOONNNNEENEENENNOOONNNNONNNENEEESEEEEENEEE

Bien joué tu as CYBERTF{M@ze_Mast3r}
```

Very fun challenge
