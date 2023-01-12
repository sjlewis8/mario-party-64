# 11 - Day at the Races

### Introduction
This is a 4v4 minigame from Mario Party 2. Each player selects, in random order, from four characters [Bob-omb, Boo, Thwomp, and Whomp] who run a foot/ghost-race down a track. The winner is the player who selected the character who finishes first.

### Mechanics
The outcome of the race is determined once all memory has been generated for the minigame (ie. one frame before the screen loads). After that point, there is no changing the outcome of the race. 

The running track is divided into 7 segments. The lines (milestones) dividing the first four segments line up with the numbered signs at the back side of the track (signs “4”,”3”,”2”, and “1”). Lines 5 and 6 are somewhere between sign “1” and the finish line, they are very close to each other. Line 7 is the finish line. Each character’s progress through the race is held in one byte:

|Memory|Description|
-|-
0x10AC40 | Bob-omb’s progress
0x10AC41 | Boo’s progress
0x10AC42 | Thwomp’s progress
0x10AC43 | Whomp’s progress

The game also tracks who is in first, second, etc.:
|Memory|Description|
-|-
0x10AE5B|Who’s in first
0x10AC5F|Who’s in second
0x10AC63|Who’s in third
0x10AC67|Who’s in last

Each character has 3 movement modes: walking, running, and tripped. Characters start the race walking but their movement mode can change as they cross a milestone (include the unmarked ones between “1” and the finish line:
- a walking player can either continue walking, start running, or trip.
- a tripped player will immediately recover and either begin running or continue walking
- once a player begins running, it can trip but will never slow to walking again

### Race outcome
After running the game 357 times (waiting 2 frames longer each iteration during the rules explanation) the number of wins were:
|Character|# wins|proportion
-|-|-|
Bob-omb|98|0.27
Boo|116|0.33
Thwomp|62|0.17
Whomp|81|0.23

If this were a fair race, each racer would have been expected to win about 89 times. This is a significant difference (chi-squared test p-value =
0.0004487).

0x0C99B4-6 holds a random number (freezing it keeps the race outcome and progress from changing).

### Race Performance
When the first player passes a milestone, the program looks up a table of program addresses (one for each milestone). This address directs the program to a table of halfwords containing either 0 or 1s. 0000 means there is a change in the player’s state and 0001 means there is no change in the player’s state. If this is zero, the game grabs a number from the random number generator (at 0x10AEC0) and places the value at 10AEC0. This random number is used to do a look up from a  table of 20 addresses that direct the program to memory locations that change the player's movement mode.

### Player Order
Since some characters have a much higher chance of winning than others, whichever play gets to select their character first has a significant advantage.

Player character selection is stored in 0x10AC30-33 (30 = Bob-omb, 31 = Boo, 32 = Thwomp, 33 = Whomp). Each character’s respective byte will change from FF (unselected) to 00-03 (00 = picked first, 01 = picked second, etc.) 1D1414 stores which player had just picked (03 = Player 1, 05 -  Player 2, 07 = Player 3, 09 = Player 4). The order that players select their character is stored at 0x10AC2C-2F (10AC2C = First pick, etc.)

Player pick order seems to work as follows:

The half-words from 0x1D1558-64 each contain a random number between 0-3. These random numbers are used to determine the final pick order which is then stored in 0x10AC2C with 1 byte for each player. This is formatted as P1P2P3P4 so for example P1 first, P2 second, etc.. = 00010203

Player pick position is chosen in descending order, from Player 4 to Player1. The program starts by searching 0x1D1558-64 for the number 0. The offset of the first zero found from 0x1D1558 is player 4’s pick position. The next player's position is then chosen. Once all the zeros are found, the program then begins looking for 1’s, then 2’s, etc. until the program starts looking for 4, in which case the program exits the loop.

For example, say 0x1D1558-64 contains  
0x1D1558: 00000002  
0x1D155C: 00000000  
0x1D1560: 00000000  
0x1D1564: 00000001  

The first zero is found at 0x1D155C (the second half-word starting from 0x1D1558), so P4 picks second => 0x10AC2C = 00000001  
The second zero is found at 0x1D1560, so P3 picks third => 0x10AC2C = 00000201  
No more zeroes are found so now we are looking for 1's.  
The first one is found at 0x1D1564, so P2 picks fourth => 0x10AC2C = 00030201  
No more ones are found so now we are looking for 2's.
The first two is found at 0x1D1558, so P1 picks first => 10AC2C = 00030201

With this approach, P4 has a significantly higher chance of picking first. P4 will pick first as long as 0x1D1558 is less than or equal to the other 3 words. 

We can determine the probability that Player 4 chooses first. If we let $X_1$ = the value of 0x1D1558, $X_2$ = the value of 0x1D155C, $X_3$ = the value of 0x1D1560, $X_4$ = the value of 0x1D1564 then 

$$ \begin{aligned}
P(\text{Player 4 picks first}) &= \sum_{i=1}^{4}{P(X_1 \leq X_2,X_3,X_4 \mid X_1 = i)P(X_1=i)} \\
&= (1)^3(\tfrac{1}{4}) + (\tfrac{3}{4})^3(\tfrac{1}{4}) + (\tfrac{1}{2})^3(\tfrac{1}{4}) + (\tfrac{1}{4})^4 \\
&= \frac{100}{256}
\end{aligned} $$

and similarly  

$$\begin{align*}
P(\text{Player 4 picks second}) &= \sum_{i=1}^{4}{P(X_2 \leq X_1 \leq X_3,X_4 \mid X_1 = i)P(X_1=i)} +  \sum_{i=1}^{4}{P(X_3 \leq X_1 \leq X_2,X_4 \mid X_1 = i)P(X_1=i)} \\ 
& \qquad \qquad +  \sum_{i=1}^{4}{P(X_4 \leq X_1 \leq X_2,X_3\mid X_1 = i)P(X_1=i)} \\
&= 3\sum_{i=1}^{4}{P(X_2 \leq X_1 \leq X_3,X_4 \mid X_1 = i)P(X_1=i)} \\
&= 3\left((\tfrac{1}{4})(\tfrac{3}{4})^2(\tfrac{1}{4}) + (\tfrac{1}{2})(\tfrac{1}{2})^2(\tfrac{1}{4}) + (\tfrac{3}{4})(\tfrac{1}{4})^2(\tfrac{1}{4})  \right) \\
&= \frac{60}{256}
\end{align*}$$

Completing the calculations we get
|P4 order|Probability|Percent|
-|-|-
1st| $\tfrac{100}{256}$ |39%
2nd| $\tfrac{60}{256}$ |23%
3rd| $\tfrac{60}{256}$ |23%
4th| $\tfrac{36}{256}$ |14%

Putting this all together, we can calculate the chance that Player 4 will win this mini-game, assuming they pick the best character available to them.

$$\begin{aligned}
P(\text{Player 4 wins} &= P(\text{P4 chooses first})P(\text{Boo Wins}) + P(\text{P4 chooses second})P(\text{Bob-omb Wins})  \\
& \qquad \qquad + P(\text{P4 chooses third})P(\text{Whomp wins}) + P(\text{P4 chooses last})P(\text{Thwomp wins}) \\
&= (\tfrac{100}{256})(\tfrac{116}{357}) + (\tfrac{60}{256})(\tfrac{98}{357}) + (\tfrac{60}{256})(\tfrac{81}{357}) + (\tfrac{36}{256})(\tfrac{62}{256}) \\
& \approx 0.27
\end{aligned}$$

So overall, Player 4 has a slight edge over their competitors, but this advantage is not very large considering that they have an almost 40% chance of picking first. This method of picking player order is used for many different mini-games, not just this one so in games where there is a bigger advantage to picking first, the fourth player may have a more significant effect.
