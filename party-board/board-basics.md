# Basics

### Dice Roll

This distance each player moves around the game board is determined by the roll of a single dice. When using the normal (unmodified) dice, the program retrieves a two byte random integer from the random number generator, finds the remainder after dividing by 10 and adds 1. This generates a uniform distribution of the integers between 1 and 10. This area of the program starts at 0x046C3C. 

The number of spaces that the player has yet to move is stored in 0x100028. For each space moved, this number is decremented by 1 by code at 0x065868.
