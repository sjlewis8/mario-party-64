# Boo Coin Stealing Event

### Introduction

This is a board event found on all party boards. When a player passes this space, they are given an option to pay to have either coins or a star stolen from an opponent. If the player chooses to have coins stolen from another player, then that player is told to mash the A-button to reduce the number of coins that are taken.

### Implementation

The number of coins stolen are determined by two factors, the current turn number and the number of times the victim has pressed the A button. 

The event starts by determining the maximum number of coins that can be stolen from the player. This is based solely on the current turn number as follows (found in memory at 0x110EFC):
|Turn Number Range|Number of Coins Stolen|
-|-
1-5	 |0x0F
6-F	 |0x19
10-19|0x1B
1A-23|0x1E
24-2D|0x20
2E-32|0x23

This number of coins is then reduced by the number of times the A-button was pressed as follows (found in memory at 0x110EE8):
|Button presses|Coins reduced|
-|-|
1-A		|1
B-B		|4
C-14	|2
15-15	|4
16-1E	|3
1F-28	|4
29-2C	|5
2D-2E	|2
33-38	|6
39-3D	|5
3E-46	|7
47-47	|8
48-FF	|9

In both cases, these tables are stored in memory as a sequence of triplets of bytes. The first byte is lower boundary, the second byte is the upper boundary, and the third byte is the result (max coins, or number of coins reduced). The program simply scans through these two tables until the proper range is found and the value retrieved.
