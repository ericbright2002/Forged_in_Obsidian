---
obsidianUIMode: preview
---

An extension to __mythicv2.sfile__ that provides graphical ui versions of shortcuts.


__
```
^sfile setup$
```
__
```js
// Block loading if mythicgme is already setup (incompatible shortcut-files)
if (_inlineScripts.inlineScripts.sfileIndices["mythicgme"])
{
	return true;
}
```
__
Sets up this shortcut-file


__
__
```js
// Reset the details if the current shortcut is user-triggered (i.e. a new action)
function clearDetailsIfUserTriggered()
{
	if (expansionInfo.isUserTriggered)
	{
		_inlineScripts.mythicv2.details = [];
	}
}
```
__
Helper scripts


__
```
^ui mythicv2 details$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Get the current showDetails flag state
const currentState = _inlineScripts.state.sessionState.mythicv2.showDetails;

// Choose a new  showDetails flag value
const pick = popups.pick(
	"Set 'details' mode",
	[ "Disable", "Enable" ], (currentState ? 1 : 0), 2);
if (pick === null) { return null; }

return expand("mythicv2 details " + (pick ? "y" : "n"));
```
__
ui mythicv2 details - Asks the user whether to enable or disable 'details' mode.
Displays whether 'details' mode is enabled.


__
```
^ui fate$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
const odds =
[
	"Impossible", "No way", "Very unlikely",
	"Unlikely", "50 / 50", "likely",
	"Very likely", "Sure thing", "Has to be"
];

// Choose what odds to use in the fate check
const pick = popups.pick("Choose the odds", odds, 4, 9);
if (pick === null) { return null; }

// Choose whether the pc benefits from a "yes" or a "no"
const pick2 =
	popups.pick("What answer is best for the pc(s)?", [ "YES", "NO" ], 0, 2);
if (pick2 === null) { return null; }

return expand( "fate " + (pick - 4) + " " + (pick2 ? "n" : "y") );
```
__
ui fate - Asks the user to choose the odds for this fate check.
Asks the user what answer is best for the pc(s).
Displays the answer to a yes/no question, possibly with a random event attached.


__
```
^ui scene$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Choose a chaos adjustment
const pick = popups.pick(
	"How was the previous scene?", [ "More controlled", "More chaotic" ], 1, 2);
if (pick === null) { return null; }

return expand("scene " + (pick * 2 - 1));
```
__
ui scene - Asks the user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.


__
```
^ui disposition$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Choose the descriptor modifier value
const choices =
[
	"-3 (ALL descriptors make the npc LESS ACTIVE)", "-2", "-1",
	"0", "+1", "+2",
	"+3 (ALL descriptors make the npc MORE ACTIVE)"
];
const pick = popups.pick("Enter the total descriptor modifier for the npc.\n\nConsider the npc's three descriptors in the current situation.\n+1 for descriptors that make the npc MORE active.\n-1 for descriptors that make the npc LESS active.\n0 for descriptors that don't affect npc activity.", choices, 3, 7);
if (pick === null) { return null; }

// Choose the base disposition
const choices2 =
[
	"Random","2","3","4","5","6","7","8","9",
	"10","11","12","13","14","15","16","17",
	"18","19","20"
];
const pick2 = popups.pick("Enter the base disposition for the npc for this scene.\n\nIf the npc has a base dispositon for this scene, select it.\nIf the npc does NOT yet have a base disposition for this scene, select 'Random'.", choices2, 0, 20);
if (pick2 === null) { return null; }

// Base disposition is random, use the shortcut that rolls it
if (!pick2)
{
	return expand("disposition " + (pick - 3));
}

// Base disposition is not random, use the shortcut that takes it as a parameter
else
{
	return expand("disposition " + (pick - 3) + " " + choices2[pick2]);
}
```
__
ui disposition - Asks the user for the total descriptor modifier for an npc (+1 for descriptors that make the npc more active in the scene, -1 for descriptors that make the npc less active).
If the npc already has a disposition for this scene, select it.  If NOT, select "random" to choose one.
Rolls and shows a disposition for the npc for the current scene.


__
```
^ui action$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Choose the disposition modifier
const dispositionMods =
[
	"-2 (Passive)", "0 (Moderate)", "+2 (Active)",
	"+4 (Aggressive)"
];
const pick =
	popups.pick("Enter the npc's current disposition modifier.", dispositionMods, 1,
	4);
if (pick === null) { return null; }

return expand("action " + (pick * 2 - 2));
```
__
ui action - Asks the user for the npc's disposition modifier.
Rolls and shows an action for the npc to take.
