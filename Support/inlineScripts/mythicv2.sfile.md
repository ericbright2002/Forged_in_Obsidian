---
obsidianUIMode: preview
---

Shortcuts for Mythic Variations 2.  Mythic GME, along with it's "Variations 2" supplement, is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic Variations 2 at [wordmill games](http://wordmillgames.com/mythic-variations-2.html).

This shortcut-file has a tutorial video available:
- [Using the "mythicv2" shortcut-file to play Mythic Variations 2](https://www.youtube.com/watch?v=fvrxAg22lYg) (runtime 7:27)

Incompatible with __mythicgme.sfile__.  If __mythicgme.sfile__ comes before __mythicv2.sfile__ in the shortcut-list, then __mythicv2.sfile__ will be disabled.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the chaos value, the scene count, and "Details" mode.

Uses __lists.sfile__ shortcut-file (optional).
Adds and uses lists for pcs, npcs and threads.
This requires that __lists.sfile__ comes before __mythicv2.sfile__ in the shortcut-list.
If the pc, npc and thread lists have items, then the __event__ and __detail__ shortcuts will incorporate them into their results.

This shortcut-file has supplementary shortcut files:
__mythicv2_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
```
^sfile setup$
```
__
```js
// Block loading if mythicgme is already setup (incompatible shortcut-files)
if (_inlineScripts.inlineScripts.sfileIndices["mythicgme"])
{
	print(
		"The mythicv2 shortcut-file is disabled as it is incompatible with " +
		"the mythicgme shortcut-file.");
	return true;
}

const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Setup the state
confirmObjectPath("_inlineScripts.state.sessionState.mythicv2.chaos", 4);
confirmObjectPath("_inlineScripts.state.sessionState.mythicv2.scene", 1);

// Setup the lists: pcs, npcs, threads
confirmObjectPath(
	"_inlineScripts.state.sessionState.lists.pcs", { type: "basic", content: [] });
confirmObjectPath(
	"_inlineScripts.state.sessionState.lists.npcs",	{ type: "basic", content: [] });
confirmObjectPath(
	"_inlineScripts.state.sessionState.lists.threads",{ type: "basic", content: [] });

// Setup the session-specific state
confirmObjectPath("_inlineScripts.mythicv2.details", []);

// Event callback - state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.mythicv2",
	function()
	{
		expand("mythicv2 reset noconfirm");
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
delete _inlineScripts.state?.listeners?.onReset?.mythicv2;

// State removal
delete _inlineScripts.state?.sessionState?.mythicv2;
```
__
Shuts down this shortcut-file


__
```
^mythic(?:v2)? reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Mythic Version 2</b> system"))
{
	return null;
}

// Reset
return expand("mythicv2 reset noconfirm");
```
__
mythicv2 reset - Resets mythic state to defaults and displays scene heading.


__
```
^mythic(?:v2)? reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Reset the state
confirmObjectPath("_inlineScripts.state.sessionState.mythicv2");
_inlineScripts.state.sessionState.mythicv2.chaos = 4;
_inlineScripts.state.sessionState.mythicv2.scene = 1;

// Reset the lists: pcs, npcs, threads
confirmObjectPath("_inlineScripts.state.sessionState.lists");
_inlineScripts.state.sessionState.lists.pcs = { type: "basic", content: [] };
_inlineScripts.state.sessionState.lists.npcs = { type: "basic", content: [] };
_inlineScripts.state.sessionState.lists.threads = { type: "basic", content: [] };

// Make sure the session-specific state is still setup
confirmObjectPath("_inlineScripts.mythicv2.details", []);

// Return a heading for scene 1
return "***\n\n\n### SCENE " +
	_inlineScripts.state.sessionState.mythicv2.scene + "\n- Setup:\n    - ";
```
__
hidden - No-confirm reset


__
```
^mythicv2 details ?(|[y|n])$
```
__
```js
// If the parameter is set, update the flag
if ($1)
{
	_inlineScripts.state.sessionState.mythicv2.showDetails = ($1 === "y");
}

// Return the current flag state
return expFormat(
	"Mythic details are " + (_inlineScripts.state.sessionState.mythicv2.showDetails ?
	"__enabled__" : "__disabled__") + ".");
```
__
mythicv2 details {state: y OR n, default: ""} - If {state} is given, assigns it to the mythicv2 "details" mode.  Otherwise, displays the current "details" mode.
***

__
__
```js
// Make a roll from 1 to max.  Store the result as a detail with the name parameter
function roll(name, max)
{
	return addDetails(name, Math.trunc(Math.random() * max + 1));
}

// Pick an item from array a.  Store the result as a detail with the name parameter
function aPick(name, a) { return a[roll(name, a.length)-1]; }

// Pick an item from array a, weighted by element wIndex of the item.  If theRoll is
// passed, use that as the roll.  Store the result as a detail with the name parameter
function aPickWeight(name, a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll("",a.last()[wIndex]);
	addDetails(name, theRoll);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}

// Takes a variable # of arguments.  Arguments are grouped into pairs.  The first
// item of the pair is a roll's name.  The secondis the roll's value.
function addDetails()
{
	for (let i = 0; i < arguments.length; i+= 2)
	{
		if (!arguments[i]) { continue; }
		_inlineScripts.mythicv2.details.push(arguments[i] + "=" + arguments[i+1]);
	}
	return arguments[arguments.length - 1];
}

// Return a string representing the detail data gathered for the current action up to
// this point.  Each data point is a roll.  If the flag is off, this returns "".
function getDetails()
{
	if (!_inlineScripts.state.sessionState.mythicv2.showDetails ||
	    !_inlineScripts.mythicv2.details.length)
	{
		return "";
	}
	return "\n- _" + _inlineScripts.mythicv2.details.join(" ") + "_";
}

// Reset the details if the current shortcut is user-triggered (i.e. a new action)
function clearDetailsIfUserTriggered()
{
	if (expansionInfo.isUserTriggered)
	{
		_inlineScripts.mythicv2.details = [];
	}
}

// The chaos adjustment modifier is used in a few ways.  This lets it's calculation
// be simple to trigger and use.
function getChaosAdjust(multiplier)
{
	const chaos = Number(_inlineScripts.state.sessionState.mythicv2.chaos);
	const result =
		( (chaos === 3) ? 2 : (chaos === 6) ? -2 : 0 ) * (multiplier || 1);
	return addDetails("chaosAdjust", result);
}
```
__
Helper scripts


__
```
^fate ?(|-4|-3|-2|-1|0|1|2|3|4) ?(|y|n)$
```
__
```js
// The parameter defaults to 0
$1 = Number($1) || 0;

// Start a new details list
clearDetailsIfUserTriggered();

// Data
const ODDS = ["impossible","no way","very unlikely","unlikely","50/50","likely","very likely","sure thing","has to be"];

// Get the wanted flag from the parameter
let wanted = ($2==="n" ? false : true);

// Make the critical rolls
let fateRoll1 = roll("fateRoll1", 10);
let fateRoll2 = roll("fateRoll2", 10);
let chaosRoll = roll("chaosRoll", 10);

// calculate the final roll result
let result =
	fateRoll1 + fateRoll2 + addDetails("oddsAdjust", ($1) * 2) +
	getChaosAdjust(wanted ? 1 : -1);
result = result > 10 ? "YES" : "NO";

// The chaos roll triggered.  Is it an extreme result, random event or both?
let isChaotic = (chaosRoll <= _inlineScripts.state.sessionState.mythicv2.chaos);
let isExtreme = isChaotic && !!(fateRoll1 % 2) && !!(fateRoll2 % 2);
let isEvent = isChaotic && !(fateRoll1 % 2) && !(fateRoll2 % 2);
if (isChaotic && fateRoll1 === fateRoll2) isExtreme = isEvent = true;

// If extreme, add such to the result
result = (isExtreme ? "EXTREME " : "") + result;

// If random event, roll it and record the result
let evtText = isEvent ? ( "\nevent - " + expand("event")[1] ) : "";

// Return the roll result, along with any random event result
return expFormat(
	"Fate check (" + ODDS[$1 + 4] + "):\n" + result + evtText + getDetails());
```
__
fate {odds: -4 TO 4 ("impossible" TO "has to be"), default: 0 ("50/50")} {wanted: y OR n, default: y} - Makes a fate check based on {odds}.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.


__
```
^fate ?(.*)$
```
__
```js
// Parameter is case-insensitive
$1 = $1.trim().toLowerCase();

// Start a new details list
clearDetailsIfUserTriggered();

// Data
const INPUT_ODDS =
{
	"impossible": -4, "no way": -3, "very unlikely": -2, "unlikely": -1, "50/50": 0, "likely": 1, "very likely": 2, "sure thing": 3, "has to be": 4
};

// Pull the wanted flag from the parameter
let wanted = " y";
if ($1.endsWith(" y"))
{
	$1 = $1.slice(0,-2);
}
else if ($1.endsWith(" n"))
{
	wanted = " n";
	$1 = $1.slice(0,-2);
}

// Work out the input odds number by comparing the parameter to the data table
const inputOdds = INPUT_ODDS[$1.trim()] || 0;

// Rely on the other "fate" shortcut to finish the job
return expand("fate " + inputOdds + wanted);
```
__
fate {odds: text, default: "50/50"} {wanted: y OR n, default: y} - Makes a fate check based on {odds}: a specific text such as "impossible", "sure thing", etc.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.


__
```
^detail$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let outcomes = [ ["ANGER",4],["SADNESS",5],["FEAR",6],["THREAD NEGATIVE",7,"threads"],["PC NEGATIVE",8,"pcs"],["FOCUS NPC",9,"npcs"],["NPC POSITIVE",10,"npcs"],["FOCUS PC",11,"pcs"],["NPC NEGATIVE",12,"npcs"],["FOCUS THREAD",13,"threads"],["PC POSITIVE",14,"pcs"],["THREAD POSITIVE",15,"threads"],["COURAGE",16],["HAPPINESS",17],["CALM",99] ];

// Make the detail roll
let result = roll("roll1", 10) + roll("roll2", 10) + getChaosAdjust();

// Pick from the weighted data table based on the detail roll
result = aPickWeight("", outcomes, 1, result);

// If the result references one of the lists, roll on the list and record the result
let focus = result[2] ? expand("lists pick " + result[2]) : "";
focus = (focus.length < 2) ? "" : (" (" + focus[1] + ")");

// Return the detail roll, along with the item of the list, if applicable
return expFormat("Detail:\n" + result[0] + focus + getDetails());
```
__
detail - Makes a detail check.


__
```
^event$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let outcomes = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35,null,true],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];

// Get an item from the weighted data table
let result = aPickWeight("eventRoll", outcomes);

// If the result references one of the lists, roll on the list and record the result
let focus = result[2] ? expand("lists pick " + result[2]) : "";
focus = (focus.length < 2) ? "" : (" (" + focus[1] + ")");

// Make and record a meaning roll
let meaning = expand("meaning " + (result[3] ? "description" : "action"))[1];

// Return the event roll result, along with the item of the list, if applicable
return expFormat(
	[ "Event:\n", result[0] + focus + " - " + meaning, getDetails(), "" ]);
```
__
event - Makes an event check.
***


__
```
^meaning(?:| action)$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let value1 = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
let value2 = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];

// Roll on the meaning tables
let result = aPick("actionRoll1", value1) + " _(of)_ " + aPick("actionRoll2", value2);

// Return the roll result
return expFormat([ "Meaning (action):\n", result, getDetails(), "" ]);
```
__
meaning action - Rolls on the action meaning tables.
	- Alternative: __meaning__


__
```
^meaning description$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let value1 = ["ABNORMALLY","ADVENTUROUSLY","AGGRESSIVELY","ANGRILY","ANXIOUSLY","AWKWARDLY","BEAUTIFULLY","BLEAKLY","BOLDLY","BRAVELY","BUSILY","CALMLY","CAREFULLY","CARELESSLY","CAUTIOUSLY","CEASELESSLY","CHEERFULLY","COMBATIVELY","COOLLY","CRAZILY","CURIOUSLY","DAINTILY","DANGEROUSLY","DEFIANTLY","DELIBERATELY","DELIGHTFULLY","DIMLY","EFFICIENTLY","ENERGETICALLY","ENORMOUSLY","ENTHUSIASTICALLY","EXCITEDLY","FEARFULLY","FEROCIOUSLY","FIERCELY","FOOLISHLY","FORTUNATELY","FRANTICALLY","FREELY","FRIGHTENINGLY","FULLY","GENEROUSLY","GENTLY","GLADLY","GRACEFULLY","GRATEFULLY","HAPPILY","HASTILY","HEALTHILY","HELPFULLY","HELPLESSLY","HOPELESSLY","INNOCENTLY","INTENSELY","INTERESTINGLY","IRRITATINGLY","JOVIALLY","JOYFULLY","JUDGEMENTALLY","KINDLY","KOOKILY","LAZILY","LIGHTLY","LOOSELY","LOUDLY","LOVINGLY","LOYALLY","MAJESTICALLY","MEANINGFULLY","MECHANICALLY","MISERABLY","MOCKINGLY","MYSTERIOUSLY","NATURALLY","NEATLY","NICELY","ODDLY","OFFENSIVELY","OFFICIALLY","PARTIALLY","PEACEFULLY","PERFECTLY","PLAYFULLY","POLITELY","POSITIVELY","POWERFULLY","QUAINTLY","QUARRELSOMELY","QUIETLY","ROUGHLY","RUDELY","RUTHLESSLY","SLOWLY","SOFTLY","SWIFTLY","THREATENINGLY","VERY","VIOLENTLY","WILDLY","YIELDINGLY"];
let value2 = ["ABANDONED","ABNORMAL","AMUSING","ANCIENT","AROMATIC","AVERAGE","BEAUTIFUL","BIZARRE","CLASSY","CLEAN","COLD","COLORFUL","CREEPY","CUTE","DAMAGED","DARK","DEFEATED","DELICATE","DELIGHTFUL","DIRTY","DISAGREEABLE","DISGUSTING","DRAB","DRY","DULL","EMPTY","ENORMOUS","EXOTIC","FADED","FAMILIAR","FANCY","FAT","FEEBLE","FEMININE","FESTIVE","FLAWLESS","FRESH","FULL","GLORIOUS","GOOD","GRACEFUL","HARD","HARSH","HEALTHY","HEAVY","HISTORICAL","HORRIBLE","IMPORTANT","INTERESTING","JUVENILE","LACKING","LAME","LARGE","LAVISH","LEAN","LESS","LETHAL","LONELY","LOVELY","MACABRE","MAGNIFICENT","MASCULINE","MATURE","MESSY","MIGHTY","MILITARY","MODERN","EXTRAVAGANT","MUNDANE","MYSTERIOUS","NATURAL","NONDESCRIPT","ODD","PALE","PETITE","POOR","POWERFUL","QUAINT","RARE","REASSURING","REMARKABLE","ROTTEN","ROUGH","RUINED","RUSTIC","SCARY","SIMPLE","SMALL","SMELLY","SMOOTH","SOFT","STRONG","TRANQUIL","UGLY","VALUABLE","WARLIKE","WARM","WATERY","WEAK","YOUNG"];

// Roll on the meaning tables
let result =
	aPick("descriptorRoll1", value1) + " " + aPick("descriptorRoll2", value2);

// Return the roll result
return expFormat([ "Meaning (description):\n", result, getDetails(), "" ]);
```
__
meaning description - Rolls on the description meaning tables.
***


__
```
^scene get$
```
__
```js
return expFormat(
	"The current scene is " + _inlineScripts.state.sessionState.mythicv2.scene + ".");
```
__
scene get - Shows the current scene.


__
```
^scene (1|-1)$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Adjust the chaos by the parameter
let result =
	($1 === "1") ? expUnformat(expand("chaos++")) :
	($1 === "-1") ? expUnformat(expand("chaos--")) :
	"Chaos is unchanged at " + expUnformat(expand("chaos"));

// Prefix the scene heading with a bunch of space
result += "\n\n\n***\n\n\n";

// Advance the scene index
_inlineScripts.state.sessionState.mythicv2.scene++;

// Add the scene header
result += "### SCENE " + _inlineScripts.state.sessionState.mythicv2.scene;

// Roll scene check for scene control
let chk = roll("sceneCheck", 10);
if (chk <= _inlineScripts.state.sessionState.mythicv2.chaos)
{
	// Odd roll?  Modify the scene in some way
	if (chk % 2)
	{
		result += "\n- Scene modified";
	}
	// Even Roll?  Replace the scene with a random event
	else
	{
		result += "\n- Scene replaced:\n" + "    - event - " + expand("event")[1];
	}
}

// Return the scene header
return result + getDetails() + "\n- setup:\n    - ";
```
__
scene {chaos adjust: -1 OR 1} - Shifts the chaos value by {chaosAdjust}, then increments the current scene and run a scene check.


__
```
^chaos$
```
__
```js
// Return the current chaos value, wrapped in a nice message
return expFormat(
	[ "Chaos is __", _inlineScripts.state.sessionState.mythicv2.chaos, "__." ]);
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
_inlineScripts.state.sessionState.mythicv2.chaos--;

// Floor the chaos value (with expansion notifying user of the flooring)
if (_inlineScripts.state.sessionState.mythicv2.chaos < 3)
{
	_inlineScripts.state.sessionState.mythicv2.chaos = 3;
	return expFormat("Chaos remains at __3__ (hit minimum).");
}

return expFormat(
	"Chaos lowered to __" + _inlineScripts.state.sessionState.mythicv2.chaos + "__.");
```
__
chaos-- - Decreases the chaos value by 1 (minimum of 3).


__
```
^chaos\+\+$
```
__
```js
// Raise the chaos value
_inlineScripts.state.sessionState.mythicv2.chaos++;

// Ceiling the chaos value (with expansion notifying user of the ceiling)
if (_inlineScripts.state.sessionState.mythicv2.chaos > 6)
{
	_inlineScripts.state.sessionState.mythicv2.chaos = 6;
	return expFormat("Chaos remains at __6__ (hit maximum).");
}

return expFormat(
	"Chaos raised to __" + _inlineScripts.state.sessionState.mythicv2.chaos + "__.");
```
__
chaos++ - Increases the chaos value by 1 (maximum of 6).


__
```
^chaos=([3-6])$
```
__
```js
// Explicity set the chaos to the parameter
_inlineScripts.state.sessionState.mythicv2.chaos = $1;

return expFormat("Chaos set to __" + $1 + "__.");
```
__
chaos={value: 3 TO 6} - Sets the chaos value to {value}, an integer from 3 to 6.
***


__
```
^descriptors?$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Make all rolls necessary for an NPC descriptor
return expFormat(
	"Descriptors:\n- personality:\n    - " + expand("meaning description")[1] +
	"\n- activity:\n    - " + expand("meaning action")[1] + getDetails());
```
__
descriptors - Generates a personality and activity descriptor for an NPC.


__
```
^disposition (-?[0-3])$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let outcomes =
	[ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],
	  ["AGGRESSIVE (+4)",99] ];

// Make the base roll (separated from the descriptor adjust as it's used on its own)
let base = roll("roll1", 10) + roll("roll2", 10);

// Add the descriptor adjust to the base
let result = base + addDetails("descriptorAdjust", Number($1) * 2);

// Pick the outcome from the weighted data table based on the resulting roll
result = aPickWeight("", outcomes, 1, result)[0];

return expFormat("Disposition:\n. " + result + "\n. BASE = " + base + getDetails());
```
__
disposition {descriptor count: -3 TO 3} - Rolls for an NPC's disposition, modified by {descriptor count}, which represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.


__
```
^disposition (-?[0-3]) ([2-9]|1[0-9]|20)$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let outcomes = [
	["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],
	["AGGRESSIVE (+4)",99] ];

// In this version, of "disposition", the "base" is passed in.
let result =
		addDetails("base", Number($2)) +
		addDetails("descriptorAdjust", Number($1) * 2);
result = aPickWeight("", outcomes, 1, result);

return expFormat("Disposition:\n. " + result[0] + "\n. BASE = " + $2 + getDetails());
```
__
disposition {descriptorCount: -3 TO 3} {base: 2 TO 20} - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.  {descriptor count} represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.


__
```
^action (-2|0|2|4)$
```
__
```js
// Start a new details list
clearDetailsIfUserTriggered();

// Data
let outcomes1 = [ ["NEW ACTION BASED ON THEME",3],["CONTINUE CURRENT ACTION",5],["CONTINUE CURRENT ACTION, +2 DISPOSITION",6],["CONTINUE CURRENT ACTION, -2 DISPOSITION",7],[false,8,0],[false,9,-4],[false,10,4] ];
let outcomes2 = [ ["TALK / EXPOSITION",6],["NEUTRAL, DISCONNECTED ACTION",8],["ACTION BENEFITS THE PC",10],["GIVE SOMETHING TO THE PC",11],["TRY TO END ENCOUNTER",12],["CHANGE THEME",13],["CHANGE \"%1\" DESCRIPTOR. IT IS ACTIVATED & DECIDES NEXT ACTION.",14,true],["ACT OUT OF SELF INTEREST",17],["TAKE SOMETHING",18],["HURT THE PC",99] ];
let descriptors = ["IDENTITY","PERSONALITY","ACTIVITY"];

// Roll on outcomes1 table
result = aPickWeight("table1Roll", outcomes1);

// If roll result is to be passed to outcomes2 table, roll on that
if (!result[0])
{
	// Make the formulated roll necessary for the outcomes2 table
	result =
		roll("table2Roll1", 10) + roll("table2Roll2", 10) +
		addDetails("dispositionAdjust", Number($1)) +
		addDetails("tableAdjust", result[2]);

	// Pull the rolled result from outcomes2 table
	result = aPickWeight("", outcomes2, 1, result);

	// If the outcomes2 roll results in "CHANGE \"%1\" DESCRIPTOR.", pick a
	// descriptor and add it to the output text.
	if (result[2])
	{
		result[0] = result[0].replace("%1", aPick("descriptorRoll", descriptors));
	}

	// Append a standard text for outcomes2 rolls
	result[0] += "\n+2/0/-2 DISPOSITION TO MATCH ACTION.";
}

return expFormat("Action:\n" + result[0] + getDetails());
```
__
action {dispositionAdjust: -2 OR 0 OR 2 OR 4} - Makes an NPC behavior check, modified by {dispositionAdjust}: the modifier of the NPC's disposition.


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
