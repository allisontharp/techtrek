---
title: Solving an Edge Matching Puzzle with Go
tags:
---
An edge matching puzzle is a type of tiling puzzle that involes matching tiles in such a way that all of their edges match up.  It's easier to see one of these, so here is an example of one (it is not solved):
**insert image here**.

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
	{
		North: "RAnt",
		East:  "RBeetle",
		South: "LBettle",
		West:  "LGrasshopper",
	},
	{
		North: "LDragon",
		East:  "RAnt",
		South: "RGrasshopper",
		West:  "LAnt",
	},
	{
		North: "RAnt",
		East:  "LDragon",
		South: "RGrasshopper",
		West:  "LBettle",
	},
	{
		North: "RBeetle",
		East:  "LAnt",
		South: "RDragon",
		West:  "LGrasshopper",
	},
	{
		North: "LAnt",
		East:  "RBeetle",
		South: "LGrasshopper",
		West:  "LDragon",
	},
	{
		North: "LBeetle",
		East:  "RGrasshopper",
		South: "LDragon",
		West:  "RAnt",
	},
	{
		North: "RGrasshopper",
		East:  "LDragon",
		South: "RAnt",
		West:  "RBeetle",
	},
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
	2.1. If it doesn't fit, rotate up to a max of 3 times
		2.1.1. If it doesn't fit, discard that tile and go back to step 2
	2.2. If none of the tiles fit, go back to the previous tile (the first tile)
		2.2.1. If the tile has not been rotated 3 times, rotate it
		2.2.2. If the tile has been rotated 3 times, replace it with the next available tile

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

## Overall
You can view the completed code on my github [here](https://github.com/allisontharp/EdgeMatchingPuzzleSolver).  It will sove a 3x3 puzzle in about 5 seconds, which isn't too bad.

## Next Steps
- Validate that that every edge descriptor starts with R or L 
- Maybe run a count of descriptors (without the prefix) and see if there are any obvious outliers.  I'm not sure if this is valuable though because it could be possible to have just 1 bee head or whatever that is then never used.
