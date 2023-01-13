# 26 - Toad Bandstand

### Introduction

This is a 2v2 minigame in Mario Party 2. Teams of two players try to hit their instruments in time with the music. The most accurate team wins.

### Scoring

Players get a window of 5 frames before each note and 3 frames after a note to register a successful hit. The game tracks successful hits in 0x10DA42 and 0x10DA44. A miss is registered if a player either misses a note or plays their instrument at the wrong time. Misses are counted in 0x10D870.

Each team's final score is simply the number of misses. This is stored in 0x10DAAC (the first two bytes are the left team's deductions, second two bytes is the right team's deductions). Whichever team has the fewest deductions wins.
