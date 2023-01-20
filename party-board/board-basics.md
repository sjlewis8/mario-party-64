# Basics

### Dice Roll

This distance each player moves around the game board is determined by the roll of a single dice. When using the normal (unmodified) dice, the program retrieves a two byte random integer from the random number generator, finds the remainder after dividing by 10 and adds 1. This generates a uniform distribution of the integers between 1 and 10. This area of the program starts at 0x046C3C. 

The number of spaces that the player has yet to move is stored in 0x100028. For each space moved, this number is decremented by 1 by code at 0x065868.

### Board Layout

The spaces on the party board are all numbered based on what branch of the board they are on and then how far into the branch they are. New branches typically start after a fork in the path. 

Data for the spaces on the party board begins at 0x12C5E0. Each space on the board has a block of memory that is 9 words long layed out as follows:

<table>
  <tr>
    <td> 1
    <td> Space visual
  <tr>
    <td> 2
    <td> ?
  <tr>
    <td> 3
    <td> X position (32 bit float)
  <tr>
    <td> 4
    <td> Z position (32 bit float)
  <tr>
    <td> 5
    <td> Y position (32 bit float)
  <tr>
    <td> 6
    <td rowspan="3"> Temporary - used in calculating movement
  <tr>
    <td> 7
  <tr>
    <td> 8
  <tr>
    <td> 9
    <td> Event list
 </table>

The second byte in the `Space visual` word determines the appearance of the space (eg blue space, star space, etc.). Changing this does not change the space's behavior when a player lands on it. These values are:

|Value|Appearance|
-|-
0x1|Blue space
0x2|Red space
0x4|Green space
0x5|Chance time
0x6|Item minigame
0x7|Bank space
0x8|Fork in the path (invisible?)
0x9|Battle minigame
0xC|Bowser
0xD|Item shop
0xE|Star
0xF|Baby Bowser
      
The last byte contains the address of an event that triggers special behaviour by the game (for example moving the player to a another location).
      
      
### Position and Walking

Each position value has two position values, the first is the current position on the board and the last is the players next tile they will move to. This is stored in
|Player|Location
-|-
1 |0x0FD2D0
2 |0x0FD304
3 |0x0FD338
4 |0x0FD36C

Player position is stored as two words, the first word is the branch the tile is located on and the second word is the tile number on the branch. For example,

<table>
  <tr>
    <td colspan = 2> current position
    <td colspan = 2> next position
  <tr>
    <td> branch
    <td> tile
    <td> branch
    <td> tile
  <tr>
    <td> 0002
    <td> 0003
    <td> 0002
    <td> 0004
</table>
 
