---
title: Solving an Edge Matching Puzzle with Go
tags:
  - Go
categories:
  - Personal Projects
date: 2021-03-26 14:00:00
---

An edge matching puzzle is a type of tiling puzzle that involes matching tiles in such a way that all of their edges match up.  It's easier to see one of these, so here is an example of one (it is not solved):
![](https://raw.githubusercontent.com/allisontharp/EdgeMatchingPuzzleSolver/main/images/ExamplePuzzle.png)

These puzzles have an incredible amount of possible tile combinations: (4^x)(x!) where x = number of tiles.  For a 3x3 puzzle, that comes out to over _95 billion_ possible combinations.  

I'm in the process of learning Go and a puzzle like this is in a board game that a group of friends and I are currently playing.  I thought it would be fun to try to use this problem as a way to start learning Go.  My goal with this isn't to write the most efficient way to solve the puzzle (either mathematically or programmatically).  My goal is to just _use_ Go to start getting a better feel and hopefully also solve our puzzle.

## Create the Project
I created a new repo and then ran `go mod init edgematchingpuzzlesolver`, which created my go.mod file.  I manually created a main.go file.

## Setup Data
The first thing I want to be able to do is store the information about my 9 tiles (and perhaps also display them).  My requirements for how the tile data is stored are: 
- Need to be able to easily store the data because it will be a manual effort to describe each tile.  Setting each edge's half-image to a specific number is too hard for the user to then have to use to describe each tile and could easily result in errors.
- Need to be able to rotate the tile programmatically.
- Would be cool if you could read in a file (json?) that has the tile specifications on it.

### Using R and L
I like the idea of prefixing each tile edge with R or L, then having some descriptor after that.  In our example, R will denote the head pieces and L will denote the backend pieces.  My struct looks like this:
```go
type Tile struct {
	North string
	East  string
	South string
	West  string
}

var tiles = []Tile{
	{
		North: "RBeetle",
		East:  "LDragon",
		South: "RDragon",
		West:  "RGrasshopper",
	},
	{
		North: "LBeetle",
		East:  "RGrasshopper",
		South: "LAnt",
		West:  "RDragon",
	},
	...
}
```

## Rotate Tile
I will need the ability to rotate a tile.  I came up with a few ways to do this, but the easiest way I found was this:
```go
func rotateTile(tile Tile) Tile {
	newTile := Tile{
		North: tile.West,
		East:  tile.North,
		South: tile.East,
		West:  tile.South,
	}
	return newTile
}
```
I will need to be careful to not lose track of what the original state of the tile was (for when I start a new iteration), but that is a problem for later.

## Check If Two Edges match
Next, I need a way to test if two edges match.  In order to do this, I created a new struct and a couple of new functions:

```go
type TileSide struct {
	Direction   string
	Description string
}

func splitTileSide(side string) TileSide {
	tileSide := TileSide{
		Direction:   side[0:1],
		Description: side[1:],
	}
	return tileSide
}

func checkForMatch(currentTileSide string, testTileSide string) bool {
	current := splitTileSide(currentTileSide)
	test := splitTileSide(testTileSide)

	fmt.Println(currentTileSide, testTileSide)

	// could do this with ORs and probably other ways, but again, this is easiest for now.
	if current.Description == test.Description {
		if current.Direction == "R" && test.Direction == "L" {
			return true
		} else if current.Direction == "L" && test.Direction == "R" {
			return true
		}
	}

	return false
}
```

## Overall Process
The above steps are the helper pieces for the overall process.  The rest is mostly just orchestration.  The flow for how I will solve is:
1. Place a tile in the top left corner.  This tile doesn't need to match on anything because nothing is there.
2. Take the next available tile and try to place it in the spot to the right of the first tile.  
	- If it doesn't fit, rotate up to a max of 3 times
		- If it doesn't fit, discard that tile and go back to step 2
	- If none of the tiles fit, go back to the previous tile (the first tile)
		- If the tile has not been rotated 3 times, rotate it
		- If the tile has been rotated 3 times, replace it with the next available tile

The steps above are repeated for every position.  The only difference is that different tile positions require different checks (some tiles only have 1 edge to match, some tiles have 2).  In order to figure that out, I wrote a function that allows me to pass in the currently placed tiles as well as the position I'm trying to pass in.  It will output which tile(s) the to-be-placed tile needs to match on and which side(s) it uses.

``` go
type sideToMatch struct {
	tileToMatch              int
	sideToMatchOnTile        string // side to match on the current tile
	sideToMatchOnMatchedTile string // side to match on the tile you are matching to
}

func getSidesToMatch(position int, width int) []sideToMatch {
	x, y := getTileCoordinates(position, width)
	out := []sideToMatch{}
	// tiles on left edge don't need to match anything in their row
	if x > 0 { // tile is not on left edge
		out = append(out, sideToMatch{
			tileToMatch:              position - 1,
			sideToMatchOnTile:        "West",
			sideToMatchOnMatchedTile: "East",
		})
	}

	// tiles on the top don't need to match anything above them
	if y > 0 { // tile is not on top row
		out = append(out, sideToMatch{
			tileToMatch:              position - width,
			sideToMatchOnTile:        "North",
			sideToMatchOnMatchedTile: "South",
		})
	}

	return out
}
```

## Keeping Track
The information that needs to be stored is obviously the placed tiles and their positions.  For this, I just used `[] Tile`.  The position in this array corresponds with where it is in the puzzle.  It is important that you save the entire tile and that it is rotated correctly in this array, as it is what is used to find matches on when you place a new tile.

Next, I needed to store the number of rotations per tile placed.  For this, I just used an array of ints.  I could have (should have?) just allowed this to be a property on the tile itself.  In the next version, this is probably what I would do.

Finally, I needed to store which tiles were still possible tiles for each location.  In this case, I used an array of array of ints (`[][]int`).  So, for instance `[[2,3,4,5,6],[4,5,6]]` would mean that tiles 4, 5, or 6 could be placed in position 1.  This is important because if I am back tracking to a position (say I tried position 3 and nothing worked, then you need to go back to position 2 and try a new tile), you don't want to try tiles you have already tried.  However, if it is _not_ a retry, then the list of possible tiles needs to be any in the pool.  Even if there were IDs already in this array for that position, they need to be overwritten to include every possible ID.

## Overall
You can view the completed code on my github [here](https://github.com/allisontharp/EdgeMatchingPuzzleSolver).  It will sove a 3x3 puzzle in about 5 seconds and around 15k attempts, which isn't too bad.  Once I realized there were 95 billion possible combinations, I thought it would take hours to find a solution.  However, since I'm not trying every path and am able to eliminate large groups of combinations at a time, it goes really fast.

I spent a lot of time on this.  Not because of Go is new to me and not because the logic of it is necessarily hard.  I had an edge mislabeled, which caused it to never find a solution.  I thought it was a bug in my code (I had reviewed the edge descriptions sevral times, but what can you do), and this is a slightly hard thing to try to track (because it takes thousands of attempts).  This is ultimately what lead me to make my own print function, where I can pass in the print level.  If I just want an overview of the output, I can put level 0.  If I need to see lots of details, I put in 4 (and then anything in between is in between).  It will print level 0 until it gets to the last 10 rows, then it will start printing more details (if you asked it to).  Printing takes a lot of time, and the console won't remember it all, so there's no use printing details until you are close to the end of the run.

## Next Steps
Should I choose to take this to the next level, the following would be near the top of the next things I would work on: 
- Make the command line arguments better by using flags (so you aren't just blinding putting numbers in that have to be in a certain order)
- Allow for other sizes (not just 3x3)
	- This would be trivial once the flags were set, just allow the size to be passed in as a param.  If it's not passed, you could assume a square and calculate based on the number of tiles
- Allow an option to let it keep running and find multiple solutions
	- It's obvious there are at least 4 solutions to ever puzzle.  However, it's also possible there could be other solutions (I found two for the puzzle above).  It would be cool to tell it to run for X attempts and see how many solutions it comes up with.  I think this would also be trivial (basically just make what I already have be a function, then iterate over that) and would be pretty cool.

It could also be cool to make this have a front end.  Maybe you place a tile in and each position is subscribed to the changes of the sides it is touching, which tells it to check again and change if needed.  This would add a lot of complexity, but could be neat.

_If you have any questions or suggestions, feel free to [contact me](/contact/)._
