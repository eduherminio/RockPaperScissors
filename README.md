# Rock, Paper, Scissors: A mathematical approach

## Introduction

I'm sure all of you have played 'Rock, Paper, Scissors', a simple hand game that is said to [have been around for nearly 2000 years](https://en.wikipedia.org/wiki/Rock_paper_scissors#Origins); but has any of you acted as a referee in that game?

Probably not, and the purpose of this first problem in Tuenti Challenge 10 is exactly that: to programmatically create a referee for it. Let's have a look at how I did it.

## Getting analytical

First things first, we need numbers to be able to justify the title of this article. Let's associate each possibility with a number:

- 1: Rock
- 2: Paper
- 3: Scissors

If we combine those numbers and the rules of the game, we get:

- Paper (2) covers Rock (1)
- Scissors (3) cut[s](https://ell.stackexchange.com/a/32288) Paper (2)
- Rock (1) smashes Scissors (3)

What do we have here?

[Reader] _"That's.. that's.. a circular linked list!"_

Well, indeed, but we're not going down that path today. However, the key fact to observe has to do with exactly that: **all combinations follow the same pattern** (big number beats small number) **but the last one** (where small number beats big number).

And that's a fact that remains true even if we shift the possibilities (keeping the same order) and use different, consecutive numbers:

- Paper (227)
- Scissors (228)
- Rock (229)

Which would leave us with

- Scissors (228) beats Paper (227)
- Rock (229) smashes Scissors (228)
- Paper (227) covers Rock (229)

## Spotting the difference

Our base conclusion is that **all combinations follow the same pattern except for one**.

In order to create an algorithm that can solve our problem, the first step now is **being able to identify that odd combination**, the one that doesn't follow the general rule.

Let's put together our pairs of numbers:

- 2 beats 1, 228 beats 227
- 3 beats 2, 229 beats 228
- 1 beats 3, 227 beats 229

Now it may be easier to spot that **the combination that behaves differently is the one whose numbers aren't consecutive**.

## Putting everything together

Let's summarize what we've got:

- **If both numbers are the same, no one wins**
- **If both numbers are consecutive, the bigger one wins**
- **If both numbers aren't consecutive, the smaller one wins**

Let's try to put those three conditions together in code (I'm using C# 8 syntax here):

```C#
int player1 = 229, player2 = 227;
var players = new []{ player1, player2 };

var winnerNumber = Math.Abs(player2 - player1)
    switch
    {
        0 => -1,
        1 => players.Max(),
        _ => players.Min()
    };
```

We can use the result of `Math.Abs(player2 - player1)` to check if the numbers are equal, consecutive or something else; and act accordingly.

[Reader] _"Hey, but hold on a second, those are numbers, not strings!"_

Correct.

We already know that it doesn't really matter which numbers we choose, providing they follow a given order. That allows us to get advantage of `enum`s, which are based on integers, in the following way:

```C#
using System;
using System.Linq;

namespace RockPaperScissors
{
    public static class Program
    {
        private enum Item { R, P, S }

        public static void Main()
        {
            const string str1 = "R";
            const string str2 = "P";

            var player1 = (int)Enum.Parse(typeof(Item), str1);
            var player2 = (int)Enum.Parse(typeof(Item), str2);
            var players = new[] { player1, player2 };

            var result = Math.Abs(player2 - player1)
              switch
              {
                  0 => "-",
                  1 => ((Item)players.Max()).ToString(),
                  _ => ((Item)players.Min()).ToString()
              };

            Console.WriteLine($"Winner: {result}");
        }
    }
}
```

We can create an `enum Item` with elements that match the strings we'll be getting as inputs. Note that we could give those elements any consecutive values (i.e., 227, 228 and 229, as we did in our example), or just let the language automatically do it.

That allows us to use `Enum.Parse()` to get the correct `Item` from each input, and cast them to get our numbers.

We feed our algorithm with those numbers, which chooses `"-"` as output if it declares the game a draw, or the string representation of whoever `Item` has won (`R`, `S` or `P`).

---

You can find [here](https://github.com/eduherminio/RockPaperScissors/blob/master/RockPaperScissors/Program.cs) the complete code I used for the challenge, which includes reading the data from an input file and writing the results to an output one.

Now a small test for you, avid reader:

Can you find a similar, mathematical approach for [Rock, Paper, Scissors, Lizard, Spock](https://en.wikipedia.org/wiki/File:Rock_paper_scissors_lizard_spock.png)?
