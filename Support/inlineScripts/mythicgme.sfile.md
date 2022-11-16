---
obsidianUIMode: preview
---

Shortcuts for Mythic Game Master Emulator.  Mythic GME is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic GME at [wordmill games](http://wordmillgames.com/mythic-game-master-emulator.html).

This shortcut-file has a tutorial video available:
- [Using the "mythicgme" shortcut-file to play Mythic GME](https://www.youtube.com/watch?v=NwHwctDp_vM) (runtime 5:00)

Incompatible with __mythicv2.sfile__.  If __mythicv2.sfile__ comes before __mythicgme.sfile__ in the shortcut-list, then __mythicgme.sfile__ will be disabled.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the chaos value and the scene count.

Uses __lists.sfile__ shortcut-file (optional).
Adds and uses lists for pcs, npcs and threads.
This requires that __lists.sfile__ comes before __mythicgme.sfile__ in the shortcut-list.
If the pc, npc and thread lists have items, then the __event__ and __detail__ shortcuts will incorporate them into their results.

This shortcut-file has supplementary shortcut files:
__mythicgme_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
```
^sfile setup$
```
__
```js
// Block loading if mythicv2 is already setup (incompatible shortcut-files)
if (_inlineScripts.inlineScripts.sfileIndices["mythicv2"])
{
	print(
		"The mythicgme shortcut-file is disabled as it is incompatible with " +
		"the mythicv2 shortcut-file.");
	return true;
}

const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Setup the state
confirmObjectPath("_inlineScripts.state.sessionState.mythicgme.chaos", 5);
confirmObjectPath("_inlineScripts.state.sessionState.mythicgme.scene", 1);

// Setup the lists: pcs, npcs and threads
confirmObjectPath(
	"_inlineScripts.state.sessionState.lists.pcs", { type: "basic", content: [] });
confirmObjectPath(
	"_inlineScripts.state.sessionState.lists.npcs", { type: "basic", content: [] });
confirmObjectPath(
	"_inlineScripts.state.sessionState.lists.threads",{ type: "basic", content: [] });

// Event callback - state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.mythicgme",
	function()
	{
		expand("mythicgme reset noconfirm");
	});
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
// Event callback removal
delete _inlineScripts.state?.listeners?.onReset?.mythicgme;

// State removal
delete _inlineScripts.state?.sessionState?.mythicgme;
```
__
Shuts down this shortcut-file


__
```
^mythic(?:gme)? reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Mythic GME</b> system"))
{
	return null;
}

// Reset
return expand("mythicgme reset noconfirm");
```
__
mythicgme reset - Resets mythic state to defaults and displays scene heading.
***


__
```
^mythic(?:gme)? reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Reset state
confirmObjectPath("_inlineScripts.state.sessionState.mythicgme");
_inlineScripts.state.sessionState.mythicgme.chaos = 5;
_inlineScripts.state.sessionState.mythicgme.scene = 1;

// Recreate lists: pcs, npcs, threads
confirmObjectPath("_inlineScripts.state.sessionState.lists");
_inlineScripts.state.sessionState.lists.pcs =
	{ type: "basic", content: [] };
_inlineScripts.state.sessionState.lists.npcs =
	{ type: "basic", content: [] };
_inlineScripts.state.sessionState.lists.threads =
	{ type: "basic", content: [] };

// Return a heading for scene 1
return "***\n\n\n### SCENE " +
	_inlineScripts.state.sessionState.mythicgme.scene + "\n- Setup:\n    - ";
```
__
hidden - No-confirm reset


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
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
```
__
Helper scripts


__
```
^fate ?(|-4|-3|-2|-1|0|1|2|3|4|5|6)$
```
__
```js
// Parameter defaults to 0
$1 = Number($1) || 0;

// Data
const ODDS = ["impossible","no way","very unlikely","unlikely","50/50","somewhat likely","likely","very likely","near sure thing","sure thing","has to be"];
const FATE_CHART = [
[ [ 10, 50, 91],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82],[  0,  0, 81],[  0,  0, 81],[  0,-20, 77] ],
[ [ 15, 75, 96],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82],[  0,  0, 81] ],
[ [ 16, 85, 97],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82] ],
[ [ 18, 90, 99],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91],[  7, 35, 88],[  4, 20, 85],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82] ],
[ [ 19, 95,100],[ 16, 85, 97],[ 15, 75, 96],[ 13, 65, 94],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83] ],
[ [ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90],[  5, 25, 86],[  4, 20, 85] ],
[ [ 20,100,  0],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86] ],
[ [ 21,105,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 15, 75, 96],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90] ],
[ [ 23,115,  0],[ 20,100,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 80, 97],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91] ],
[ [ 25,125,  0],[ 22,110,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97],[ 13, 65, 94],[ 11, 55, 92] ],
[ [ 26,145,  0],[ 26,130,  0],[ 20,100,  0],[ 20,100,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97] ] ];

// Determine the fate range from the "odds" parameter and the current chaos level
const fateRange =
	FATE_CHART[$1+4][9 - _inlineScripts.state.sessionState.mythicgme.chaos];

// Roll on the fate range to determine fate result
const r = roll(100);
let result =
	(r <= fateRange[0]) ? "EXTEREME YES" :
	(r <= fateRange[1]) ? "YES" :
	(r <= fateRange[2]) ? "NO" :
	"EXTREME NO";

// If the roll triggers a random event, add it
let eventOutput = "";
if (Math.trunc(r/10) == r % 10 &&
    r % 10 < _inlineScripts.state.sessionState.mythicgme.chaos)
{
	eventOutput = "\nevent - " + expand("event")[1];
}

// Return the fate result, along with the odds and possible random event
return expFormat("Fate check (" + ODDS[$1+4] + "):\n" + result + eventOutput);
```
__
fate {odds: -4 TO 6 ("impossible" TO "has to be"), default: 0 ("50/50")} - Makes a fate check based on {odds}.


__
```
^fate ?\s?(.*)$
```
__
```js
// Data
const INPUT_ODDS =
{
	"impossible": -4, "no way": -3, "very unlikely": -2, "unlikely": -1, "50/50": 0, "somewhat likely": 1, "likely": 2, "very likely": 3, "near sure thing": 4, "sure thing": 5, "has to be": 6
};

// Turn the odds parameter from text to number, then call the other fate shortcut
return expand("fate " + (INPUT_ODDS[$1.trim().toLowerCase()] ?? 0));
```
__
fate {odds: text, default: "50/50"} - Makes a fate check based on {odds}: a specific text, such as "impossible", "sure thing", etc.

__
```
^scene get?$
```
__
```js
return expFormat(
	"Currently in scene " + _inlineScripts.state.sessionState.mythicgme.scene + ".");
```
__
scene get - Shows the current scene.


__
```
^scene (1|-1)$
```
__
```js
// Adjust the chaos by the parameter
let result =
	($1 === "1") ? expUnformat(expand("chaos++")) :
	($1 === "-1") ? expUnformat(expand("chaos--")) :
	"Chaos is unchanged at " + expUnformat(expand("chaos"));

// Prefix the scene heading with a bunch of space
result += "\n\n\n***\n\n\n";

// Advance the scene index
_inlineScripts.state.sessionState.mythicgme.scene++;

// Add the scene header
result += "### SCENE " + _inlineScripts.state.sessionState.mythicgme.scene;

// Roll scene check for scene control
let chk = roll(10);
if (chk <= _inlineScripts.state.sessionState.mythicgme.chaos)
{
	// Odd roll?  Modify the scene in some way
	if (chk % 2)
	{
		result += "\n- Scene altered";
	}
	// Even Roll?  Replace the scene with a random event
	else
	{
		result += "\n- Scene interrupted:\n    - event - " + expand("event")[1];
	}
}

// Return the scene header
return result + "\n- setup:\n    - ";
```
__
scene {chaosAdjust: -1 OR 1} - Shifts the chaos value by {chaosAdjust}, then increments the current scene and runs a scene check.
***


__
```
^event$
```
__
```js
// Data
const FOCUS_TABLE = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];

// Roll on the table
let result = aPickWeight(FOCUS_TABLE);

// If the table result has an associated list, add a random pick from the list
let focus = result[2] ? expand("lists pick " + result[2]) : "";
focus = (focus.length < 2) ? "" : (" (" + focus[1] + ")");

// Add a roll from the meaning tables
let meaning = expand("meaning")[1];

// Return the random event result
return expFormat([ "Event:\n", result[0] + focus + " - " + meaning, "" ]);
```
__
event - Makes an event check.


__
```
^meaning$
```
__
```js
// Data
const ACTION_TABLE = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
const SUBJECT_TABLE = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];

// Roll on the two meaning tables
let result = aPick(ACTION_TABLE) + " _(of)_ " + aPick(SUBJECT_TABLE);

// Return the meaning roll result
return expFormat([ "Meaning:\n", result, "" ]);
```
__
meaning - Rolls on the meaning tables.
***


__
```
^chaos$
```
__
```js
// Return the current chaos value, wrapped in a nice message
return expFormat(
	[ "Chaos is __", _inlineScripts.state.sessionState.mythicgme.chaos, "__." ]);
```
__
chaos - Shows the current chaos value.


__
```
^chaos--$
```
__
```js
// Lower the chaos value
_inlineScripts.state.sessionState.mythicgme.chaos--;

// Floor the chaos value (with expansion notifying user of the flooring)
if (_inlineScripts.state.sessionState.mythicgme.chaos < 1)
{
	_inlineScripts.state.sessionState.mythicgme.chaos = 1;
	return expFormat("Chaos remains at __1__ (hit minimum).");
}

return expFormat(
	"Chaos lowered to __" + _inlineScripts.state.sessionState.mythicgme.chaos +
	"__.");
```
__
chaos-- - Decreases the chaos value by 1 (minimum of 1).


__
```
^chaos\+\+$
```
__
```js
// Raise the chaos value
_inlineScripts.state.sessionState.mythicgme.chaos++;

// Ceiling the chaos value (with expansion notifying user of the ceiling)
if (_inlineScripts.state.sessionState.mythicgme.chaos > 9)
{
	_inlineScripts.state.sessionState.mythicgme.chaos = 9;
	return expFormat("Chaos remains at __9__ (hit maximum).");
}

return expFormat(
	"Chaos raised to __" + _inlineScripts.state.sessionState.mythicgme.chaos + "__.");
```
__
chaos++ - Increases the chaos value by 1 (maximum of 9).


__
```
^chaos=([1-9])$
```
__
```js
// Explicity set the chaos to the parameter
_inlineScripts.state.sessionState.mythicgme.chaos = $1;

return expFormat("Chaos set to __" + $1 + "__.");
```
__
chaos={value: 1 TO 9} - Sets the chaos value to {value}.


__
```
^lists? pick (|[_a-zA-Z][_a-zA-Z0-9]*)(| [1-9][0-9]*)$
```
__
```js
return [];
```
__
hidden - A placeholder shortcut in case the shortcut-file lists.sfile isn't available.
