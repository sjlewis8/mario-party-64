# Basics

### Dice Roll

This distance each player moves around the game board is determined by the roll of a single dice. When using the normal (unmodified) dice, the program retrieves a two byte random integer from the random number generator, finds the remainder after dividing by 10 and adds 1. This generates a uniform distribution of the integers between 1 and 10. This area of the program starts at 0x046C3C. 

The number of spaces that the player has yet to move is stored in 0x100028. For each space moved, this number is decremented by 1 by code at 0x065868.

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
 
