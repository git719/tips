# Hobbies

## Chess
Great Chess Openings to master.

- For White
  - King's Gambit (Muzio Gambit)
  - Danish Gambit
  - Fried Liver Attack
  - Cochrane Gambit
  - Scotch Gambit
  - Halloween Gambit
  - Spicy Gambits: The Monkey's Bum
  - Caro-Kann Defense, Hillbilly Attack
  - The Ponziani Opening

- For Black
  - Latvian Gambit

**Convert portable naming notation (PGN) from chess.com**:
```
pgnconv() {
    cat $1 | sed  "s/ \[%timestamp null\]//g" | sed "s/ [0-9]*\.\.\.//g" > ${1%.*}_2.pgn
}
```


## Baseball
Very useful reference for baseball related hacking:
<https://github.com/baseballhackday/data-and-resources/wiki/Resources-and-ideas>


## Poker
Games cheat-sheet patterns:

- Cash game
```
  $40 buy-in
  4  green/25c =   1.00
  8  blue/50c  =   4.00
  10 white/$1  =  10.00
  5  red/$5    =  25.00
  Total        = $40.00
```

- Tournament game
```
  Each player gets $2000 in chips:
  4 green/$25   =   100
  8 blue/$50    =   400
  5 white/$100  =   500  (or 5 black/$100)
  2 purple/$500 =  1000  (or 1 yellow/$1000)
  Total         =  2000
  BLIND SCHEDULE
  LEVEL      SMALL     BIG
  Level 1      25       50
  Level 2      50      100
  Level 3     100      200
  Level 4     200      400
  Level 5     300      600
  Level 6     500    1,000
  Level 7   1,000    2,000
  Level 8   2,000    4,000
  Level 9   4,000    8,000
  Level 10  5,000   10,000
  SAMPLE PAYOUTS
  $40 buy-in
    6 Players
      Total = 240  1st=200  2nd=40
      Additional buy-ins are split between 1st & 2nd 4:1 ratio
    8 Players
      Total = 320  1st=200  2nd=80  3rd=40
      Additional buy-ins are split between 1st & 2nd 3:2 ratio
  $20 buy-in
    6 Players
      Total = 120  1st=100  2nd=20
      Additional buy-ins are split between 1st & 2nd 4:1 ratio
    8 Players
      Total = 160  1st=100  2nd=40  3rd=20
      Additional buy-ins are split between 1st & 2nd 3:2 ratio
```
