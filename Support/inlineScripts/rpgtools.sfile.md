---
obsidianUIMode: preview
---

Shortcuts to help in playing tabletop rpgs, either group or solo.


__
__
```js
// Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Pick an item from array a.
function aPick(a) { return a[roll(a.length)-1]; }

// Pick an item from array a, weighted by element wIndex of the item.  If theRoll is
// passed, use that as the roll.
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (let i = 0; i < a.length; i++)
	{
		if (a[i][wIndex] >= theRoll)
		{
			return a[i];
		}
	}
	return a.last();
}
```
__
Helper scripts


__
```
^tbl twist$
```
__
```js
// Data
let target = ["ENVIRONMENT","ALLY","ENEMY","UNKNOWN","SOMETHING","INFORMATION"];
let circumstance = ["MISSING / GONE / LOST / FORGOTTEN","EXPLOSIVE / DANGEROUS / SENSITIVE","BLOCKED / HIDDEN / TRAPPED","ARRIVED / RECENT / NEW","DEAD / DESTROYED / UNRECOVERABLE","BROKEN / DAMAGED / INJURED","FLOODED / OVERFLOWED / EXCEEDED","SABOTAGED / CAPTURED / TAKEN","CORRUPTED / TWISTED / INSANE","FALSE / FAKE / LYING / TREASON","MISTAKEN / ERROR / FALSE / WRONG","LEAKED / DISCOVERED / KNOWN","ERRATIC / UNRELIABLE / FAILING / INSUFFICIENT","DISRUPTED / MODIFIED / ARTIFICIAL","SURPRISING / ALTERED / UNEXPECTED","YOUNGER / OLDER / NEWER / OBSOLETE","SICK / HAZARDOUS / POISON / TOXIC","PREPARED / ARMED / EXPECTING","UNPREPARED / UNARMED / UNEXPECTED","DELAYED / TIMED / WAIT"];

// Roll from the data and return the result.  The final empty string allows
// formatting to be removed by the caller.
return expFormat([ "Twist:\n", aPick(target), " - ", aPick(circumstance), "" ]);

```
__
tbl twist - Rolls a plot twist from random tables.  <a href='https://jvhouse.xyz/plot-twist-situations'>Source</a>.
