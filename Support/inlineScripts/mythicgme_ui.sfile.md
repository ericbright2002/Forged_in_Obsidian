---
obsidianUIMode: preview
---

An extension to __mythicgme.sfile__ that provides graphical ui versions of shortcuts.


__
```
^sfile setup$
```
__
```js
// Block loading if mythicv2 is already setup (incompatible shortcut-files)
if (_inlineScripts.inlineScripts.sfileIndices["mythicv2"])
{
	return true;
}
```
__
Sets up this shortcut-file


__
```
^ui fate$
```
__
```js
// Data
const odds =
[
	"Impossible", "No way", "Very unlikely",
	"Unlikely", "50 / 50", "Somewhat likely",
	"Likely", "Very likely", "Near sure thing",
	"Sure thing", "Has to be"
];

// Choose the odds to roll fate with
const pick = popups.pick("Choose the odds", odds, 4, 11);
if (pick === null) { return null; }

// Roll fate with the chosen odds
return expand("fate " + (pick - 4));
```
__
ui fate - Asks the user to choose the odds for this fate check.
Displays the answer to a yes/no question, possibly with a random event attached.


__
```
^ui scene$
```
__
```js
// Choose chaos adjustment for the scene advancement
const pick =
	popups.pick("How was the previous scene?", [ "More controlled", "More chaotic" ],
	1, 2);
if (pick === null) { return null; }

// Run the scene with the chosen chaos adjustments
return expand("scene " + (pick * 2 - 1));
```
__
ui scene - Asks the user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.
