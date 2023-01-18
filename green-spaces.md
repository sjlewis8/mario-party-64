# Green Space Resolution

When a player lands on a green space, they are not immediately allocated to a red or blue team until right before minigame selection. Is the choice between the red and blue team a random process or determined by a set of criteria?

Prior to the minigame select wheel showing up, all players on a green space are assigned to either the red or blue team. This determines what type of minigame is chosen [ie. 4v4, 3v1, 2v2]. The game first collects the number of players on blue spaces, green spaces, and red spaces. Then a random number between 0-100 is chosen. The probabilities of each type of minigame are hardcoded and the value of the random number is used to determine the type (eg. For 1 player on green space and 3 players on red space: if the random number < 70 then the green player turns red, otherwise they turn blue).

The hardcoded probabilities are as follows:

<table>
<tr bgcolor="green">
<td valign="top">
<table>
  <tr>
    <td colspan=2><b>If ONE player on a green space
  <tr>
    <td valign="top">If 3 players on red spaces:</td>
    <td>70% chance turns red (4v4) <br> 30% chance turns blue (3v1)
  <tr>
    <td valign="top">If 1 blue and 2 red:
    <td>50% chance turns red (3v1) <br> 50% chance turns blue (2v2)
  <tr>
    <td valign="top">If 2 blue and 1 red:
    <td>60% chance turns red (2v2) <br> 40% chance turns blue (3v1)
  <tr>
    <td valign="top">If 3 blue spaces:	</td>
    <td>20% chance turns red (3v1) <br> 80% chance turns blue (4v4)
</table>
<td valign="top">
<table>
  <tr>
    <td colspan=2><b> If TWO players on green spaces
  <tr>
	  <td valign="top"> If 1 blue and one red:		
    <td> 20% chance both turn red (3v1) <br> 60% chance one red and one blue (2v2) <br> 20% chance both turn blue (3v1)
  <tr>
	  <td valign="top"> If 2 blue spaces:			
    <td> 15% chance both turn red (2v2) <br> 15% chance one red and one blue (3v1) <br> 70% chance both turn blue (4v4)
  <tr>
	  <td valign="top"> If 2 red spaces:
    <td> 70% chance both turn red (4v4) <br> 15% one red and one blue (3v1) <br> 15% both turn blue (2v2)
</table>
</table>

<table>
<td valign="top">
  <table>
    <tr>
      <td colspan=2 width = 434><b> If THREE players on green spaces
    <tr>
      <td valign="top"> If 1 red space:			
      <td> 60% chance all turn red (4v4) <br> 10% chance 2 turn red (3v1) <br> 20% chance 1 turns red (2v2) <br> 10% chance all turn blue (3v1)
    <tr>
      <td valign="top"> If 1 blue space:
      <td> 10% all turn red (3v1) <br> 20% 2 turn red (2v2) <br> 10% 1 turn red (3v1) <br> 60% all turn blue (4v4)
  </table>
   
<td valign="top">
  <table>
    <tr>
      <td valign="top"><b> If FOUR players on green space
    <tr>
      <td> 25% all turn red (4v4) <br> 12% chance 3 red and 1 blue (3v1) <br> 25% two red and two blue (2v2) <br> 15% 1 red and 3 blue (3v1) <br> 25% all turn blue (4v4)
  </table>
</table>

The team slots that need to be filled are stored as an array, with 0 indicating the blue team and 1 indicating the red team. For example, if there are 3 players on green spaces and one needs to turn blue and two need to turn red, you would have an array that looks like 0000 0001 0001.

Next, an array of the numbers 0 through the number of players on green spaces is generated (ie. If 4 players on green spaces → 00 01 02 03). These numbers are then shuffled using a random number generator (00 01 02 03 → 01 03 00 02).

Finally, each player is cycled through. If they are on a green space, then the first unused number in the array of shuffled numbers is taken and used as the index into the array of team assignments. 

For example: If there are 4 players on green spaces playing a 2v2 game then we have  
&emsp; required team assignments: 0000 0000 0001 0001  
&emsp; shuffled list of numbers: 02 00 01 03

&emsp; p1 → team assignment[ shuffled number list [0] ] → team assignment[02] → 0001 → red  
&emsp;	p2 → team assignment[ shuffled number list [1] ] → team assignment[00] → 0000 → blue  
&emsp;	p3 → team assignment[ shuffled number list [2] ] → team assignment[01] → 0000 → blue  
&emsp;	p4 → team assignment[ shuffled number list [3] ] → team assignment[03] → 0001 → red  
