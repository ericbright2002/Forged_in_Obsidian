---
obsidianUIMode: preview
---

Shortcuts for Adventure Crafter.  Adventure Crafter is a system to create coherent story beats through randomization and tables.  It was designed by Tana Pigeon.  You can find more info about Adventure Crafter at [wordmill games](http://wordmillgames.com/the-adventure-crafter.html).

This shortcut-file has a tutorial video available:
- [Using the "adventurecrafter" shortcut-file to play with AdventureCrafter](https://www.youtube.com/watch?v=-z922-50K-o) (runtime 7:29)

Uses __lists.sfile__ shortcut-file (required).
This requires that __lists.sfile__ comes before __adventurecrafter.sfile__ in the shortcut-list.
Adds and uses lists for plotlines and characters.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the theme order and final theme switch state.

Uses __mythicv2.sfile__ or __mythicgme.sfile__ shortcut file (optional).
If __mythicv2.sfile__ or __mythicgme.sfile__ is available, then the plotlines and characters lists will be created to reference the __mythicv2.sfile__ or __mythicgme.sfile__ lists.
This requires that __mythicv2.sfile__ or __mythicgme.sfile__ comes before __adventurecrafter.sfile__ in the shortcut-list.


__
__
```js
// Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Pick an item from array a.
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
^adventurecrafter reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Adventure Crafter</b> system"))
{
	return null;
}

// Reset
expand("adventurecrafter reset noconfirm");

return expFormat("Adventure crafter reset.");
```
__
adventurecrafter reset - Resets adventurecrafter state to defaults.
***


__
```
^adventurecrafter reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Reset themes
confirmObjectPath("_inlineScripts.state.sessionState.adventurecrafter");
_inlineScripts.state.sessionState.adventurecrafter.themeSlots = [];

// Check lists state
confirmObjectPath("_inlineScripts.state.sessionState.lists");

// Setup plotlines list (built upon threads list, if it's there)
if (!_inlineScripts.state.sessionState.lists.threads)
{
	_inlineScripts.state.sessionState.lists.plotlines =
		{ type: "basic", content: [] };
	delete _inlineScripts.state.sessionState.lists.plotline_dupes;
}
else
{
	_inlineScripts.state.sessionState.lists.plotline_dupes =
		{ type: "basic", content: [] };
	_inlineScripts.state.sessionState.lists.plotlines =
		{ type: "combo", content: ["threads", "plotline_dupes"] };
}

// Setup characters list (built upon pcs & npcs lists, if they are there)
if (!_inlineScripts.state.sessionState.lists.pcs ||
	!_inlineScripts.state.sessionState.lists.npcs)
{
	_inlineScripts.state.sessionState.lists.characters =
		{ type: "basic", content: [] };
	delete _inlineScripts.state.sessionState.lists.character_dupes;
}
else
{
	_inlineScripts.state.sessionState.lists.character_dupes =
		{ type: "basic", content: [] };
	_inlineScripts.state.sessionState.lists.characters =
		{ type: "combo", content: ["pcs", "npcs", "character_dupes"] };
}
```
__
hidden - No-confirm reset


__
```
^turning point$
```
__
```js
// Early-out if theme-slots aren't filled with chosen themes
if (_inlineScripts.state.sessionState.adventurecrafter.themeSlots.length < 5)
{
	return expFormat("Turning point not generated.  Not all theme slots filled.");
}

// Create 5 plot-points for this turning point. Hold "none" plot-points for the end.
let result = "Turning point:\n";
let nones = [];
debugger;
for (let i = 0; i < 5; i++)
{
	// Get a polot point
	const plotPoint = expand("plot point");

	if (!plotPoint[0])
	{
		return expFormat(
			"Turning point not generated.  Plot point error: " + plotPoint.join(""));
	}

	// "normal" plot-points have a length of 4 or more
	else if (plotPoint.length >= 4)
	{
		result += plotPoint[1] + "\n    " + plotPoint[3] + "\n";
	}

	// "none" plot-points have a length of less than 4
	else
	{
		// Only allow 3 "none" plotpoints
		if (nones.length < 3)
		{
			nones.push(plotPoint[1]);
		}
		// If already have 3 "none", just try again
		else
		{
			i--;
		}
	}
}

// Add "none" plotpoints to the end of the list, then remove extra "\n" at the end.
for (const none of nones)
{
	result += "" + none + "\n";
}
result = result.slice(0, -1);

return expFormat(result);
```
__
turning point - Gets a list of five plot points to represent a major milestone in a plotline.


__
```
^plot point$
```
__
```js
// Early-out if theme-slots aren't filled with chosen themes
if (_inlineScripts.state.sessionState.adventurecrafter.themeSlots.length < 5)
{
	return expFormat(
		[ "", "Plot point not generated.  Not all theme slots filled." ]);
}

// Pick the theme to use for this turning point, if error then early out
const themePick = expand("themes pick");
if (!themePick[0])
{
	return expFormat(
		[ "", "Plot point not generated.  Theme error: " + themePick.join("") ]);
}

// Setup plot-point output as array to allow splitting it up in turning-point
let result = [ "Plot point:\n" ];
const plotPoint = aPickWeight(_inlineScripts.adventurecrafter.plot, themePick[3]);

// Handle a normal plot-point
if (!plotPoint[7])
{
	result.push("- " + plotPoint[0] + "    _(" + themePick[1] + ")_");
	result.push("\n    ");
	result.push("- " + plotPoint[6]);
}

// Handle a special plot-point
else
{
	// Handle "none" plot-point
	if (plotPoint[7] === 1)
	{
		// Having just this element signifies this as a "none" plotpoint.
		result.push("- " + plotPoint[0]);
	}

	// Handle "meta" plot-point
	else if (plotPoint[7] === 2)
	{
		const metaPoint = aPickWeight(_inlineScripts.adventurecrafter.plot_meta);
		result.push("- " + metaPoint[0] + "    _(META)_");
		result.push("\n    ");
		result.push("- " + metaPoint[2]);
	}
}

// Add an extra element to hold the expFormat suffix.  Lets the suffix be removable.
result.push("");

return expFormat(result);
```
__
plot point - Gets a single plot point, generated from the plot points table.
***


__
```
^themes? pick$
```
__
```js
// Early-out if theme-slots aren't filled with chosen themes
if (_inlineScripts.state.sessionState.adventurecrafter.themeSlots.length < 5)
{
	return expFormat([ "", "Theme not picked.  Not all theme slots filled." ]);
}

// Pick which theme slot to use
let pick = roll(10);
if      (pick <  5) { pick = 0; }
else if (pick <  8) { pick = 1; }
else if (pick < 10) { pick = 2; }
else // if pick === 10
{
	// Alternate between theme[3] and theme[4].  Start with theme[3].
	let state = _inlineScripts.state.sessionState.adventurecrafter;
	pick = state.priorPickWas3 ? 4 : 3;
	state.priorPickWas3 = !state.priorPickWas3;
}

// Get the theme at the picked theme slot
pick = _inlineScripts.state.sessionState.adventurecrafter.themeSlots[pick];

// Return the picked theme
return expFormat([
	"Theme __", _inlineScripts.adventurecrafter.themes[pick], "__ picked _(",
	(pick+1), ")_." ]);
```
__
themes pick - Picks a random theme from the chosen themes, as per the Adventure Crafter rules.


__
```
^themes?$
```
__
```js
const themeNames = _inlineScripts.adventurecrafter.themes;
const themeSlots = _inlineScripts.state.sessionState.adventurecrafter.themeSlots;

// Begin an expansion listing the theme-slots
let result = [ "Theme slots:\n", "" ];

// Add to expansion - the theme in each theme slot, or empty if theme slot unfilled.
for (let i = 0; i < 5; i++)
{
	result[1] += (i+1) + " - " + (themeNames[ themeSlots[i] ?? 9 ] || "") + "\n";
}

// Remove extra newline from the end of the expansion
result[1] = result[1].slice(0, -1);

return expFormat(result);
```
__
themes - Shows the list of picked themes.


__
```
^themes? add$
```
__
```js
let themeSlots = _inlineScripts.state.sessionState.adventurecrafter.themeSlots;

// Early out if all theme-slots are already filled
if (themeSlots.length >= 5)
{
	return expFormat("Theme not added.  All five theme slots are filled.");
}

// Get theme info
const themeNames = _inlineScripts.adventurecrafter.themes;
const themeDescriptions = _inlineScripts.adventurecrafter.themeDescriptions;

// Setup the picker popup
let themeSlotDisplay = themeSlots.map((v, i) =>
	{
		return (i + 1) + " - " + themeNames[v];
	});
for (let i = themeSlotDisplay.length; i < 5; i++)
{
	themeSlotDisplay.push((i + 1) + " -");
}
themeSlotDisplay = themeSlotDisplay.join("\n");
const popupMessage =
	"Select the next theme.\n\nCurrent theme slots:\n" +
	"<div style='display:inline-block;text-align:left'>" + themeSlotDisplay +
	"</div><br/><br/>";
const popupOptions = themeNames.map((v, i) =>
	 {
		return v.padEnd(16, " ") +
			_inlineScripts.adventurecrafter.themeDescriptions[i];
	 });

// Choose a theme
let pick = popups.pick(popupMessage, popupOptions, 0, "adaptive");
if (pick === null) { return null; }

// Add the picked theme
themeSlots.push(pick);

const result =
	"Theme slot __" + themeSlots.length + "__ set to __" + themeNames[pick] + "__";
return expFormat(result);
```
__
themes add - Chooses a theme for the next unchosen theme-slot.


__
```
^themes? roll$
```
__
```js
let themeSlots = _inlineScripts.state.sessionState.adventurecrafter.themeSlots;

// Early out if all theme-slots are already filled
if (themeSlots.length >= 5)
{
	return expFormat(
		[ "", "Theme not added.  All five theme slots are already filled." ]);
}

// Pick from 1 to 5.  Repeat until the pick is the index of an unslotted theme.
let r;
do
{
	r = roll(5);
}
while (themeSlots.includes(r - 1)
	   // Prevent infinite loops from race conditions (an exceptional error)
       && themeSlots.length < 5);

// Add the picked theme to the theme slots
themeSlots.push(r - 1);


return expFormat(
	[ "Theme slot __" + themeSlots.length + "__ set to __" + 
	_inlineScripts.adventurecrafter.themes[r-1] + "__." ]);
```
__
themes roll - Picks a random theme for the next unchosen theme-slot.


__
```
^themes? fill$
```
__
```js
// Begin expansion of all theme-slots filled
let result = [];

// Repeatedly roll a random theme until all theme slots are filled
do
{
	// Roll the next theme.  If there's an error, early out
	const rollResult = expUnformat(expand("themes roll"));
	if (!rollResult[0])
	{
		result = result.join("\n");
		if (result) { result += "\n\n"; }
		return expFormat(result + rollResult.join(""));
	}
	result.push(rollResult.join(""));
}
while (_inlineScripts.state.sessionState.adventurecrafter.themeSlots.length < 5);

return expFormat(result.join("\n"));
```
__
themes fill - Picks random themes for the remaining unchosen theme-slots.


__
```
^themes? clear$
```
__
```js
_inlineScripts.state.sessionState.adventurecrafter.themeSlots = [];
return expFormat("All theme slots cleared.");
```
__
themes clear - Clears all chosen themes.
***


__
```
^ac chars? gen$
```
__
```js
const identities = [ ["TWO IDENTITIES",33,true],["WARRIOR",34],["HEALER",35],["PROTECTOR",36],["ASSISTANT",37],["DEPENDENT",38],["RULER",39],["ADMINISTRATOR",40],["VICTIM",41],["SCHOLAR",42],["EXPERT",43],["ELITE",44],["INVESTIGATOR",45],["CRIMINAL",46],["SUPPORTER",47],["HELPLESS",48],["MEDIATOR",50],["ENTERTAINER",51],["SOCIALITE",52],["ATHLETE",53],["PERFORMER",54],["REPRESENTATIVE",55],["MERCHANT",56],["TRADER",57],["CREATOR",58],["ARTIST",59],["SERVANT",60],["LABORER",61],["RELIGIOUS",62],["HUNTER",63],["LEADER",64],["FIGHTER",65],["THIEF",67],["RADICAL",68],["EXECUTIVE",69],["THUG",70],["GUARD",71],["GUARDIAN",72],["EXPLORER",73],["HERO",74],["VILLAIN",75],["DECEIVER",76],["ENGINEER",77],["SCOUT",78],["FIXER",79],["WANDERER",80],["SUBVERTER",81],["SOLDIER",82],["SCIENTIST",84],["GATHERER",85],["FOREIGNER",86],["SURVIVOR",87],["GAMBLER",88],["ROGUE",89],["FARMER",90],["KILLER",91],["PROFESSIONAL",92],["DRIVER/PILOT",93],["STUDENT",94],["ORGANIZER",95],["DELIVERER",96],["LACKEY",97],["TEACHER",98],["EXOTIC",100] ];
const descriptors = [ ["TWO DESCRIPTORS",21,true],["UGLY",22],["BEAUTIFUL",23],["FOUL",24],["SWEET",25],["UNUSUAL",26],["COMMON",27],["INTELLIGENT",28],["IGNORANT",29],["EDUCATED",30],["SKILLED",31],["TRAINED",32],["RUDE",33],["POLITE",34],["FANCY",35],["ROUGH",36],["DIRTY",37],["CLEAN",38],["WEALTHY",39],["POOR",40],["SMALL",41],["LARGE",42],["QUIET",43],["LOUD",44],["FAST",45],["SLOW",46],["EXOTIC",47],["UNIFORMED",48],["INTERESTING",49],["COLORFUL",50],["INFORMATIVE",51],["DANGEROUS",52],["INEPT",53],["CLUMSY",54],["CAPABLE",55],["INTRUSIVE",56],["RESPECTFUL",57],["PRIMITIVE",58],["SOPHISTICATED",59],["ELEGANT",60],["ARMED",61],["DIFFERENT",62],["YOUNG",63],["OLD",64],["DIFFICULT",65],["HELPFUL",66],["HARMFUL",67],["DISCIPLINED",68],["ERRATIC",69],["WILD",70],["CRAZY",71],["COMMANDING",72],["MEEK",73],["HUMOROUS",74],["FRIGHTENED",75],["BRAVE",76],["STRONG",77],["WEAK",78],["IMPULSIVE",79],["STRATEGIC",80],["NAIVE",81],["CONFIDENT",82],["SURPRISING",83],["PASSIVE",84],["BOLD",85],["CARELESS",86],["CAUTIOUS",87],["SNEAKY",88],["INTIMIDATING",89],["POWERFUL",90],["POWERLESS",91],["HURT",92],["ROUGH",93],["GENTLE",94],["CARING",95],["PRINCIPLED",96],["ARROGANT",97],["CURIOUS",98],["SUPPORTIVE",99],["HEROIC",100] ];
const specialTraits = [
	[ "THE CHARACTER IS AN INDIVIDUAL", 50, "The Character is an individual, as opposed to an organization or object." ],
	[ "THE CHARACTER IS AN ORGANIZATION", 57, "This Character is not a specific individual, but an organization or community. General members of this organization are considered part of the Character as a community." ],
	[ "THE CHARACTER IS AN OBJECT", 64, "This Character is something other than a typical, living individual or group organization. The Character is an object of some kind that could also be considered a Character unto itself. Examples might include a spaceship that is old and temperamental, or a city teeming with culture." ],
	[ "THE CHARACTER IS CONNECTED TO THIS PLOTLINE", 71, "This Character enters the Adventure somehow connected with the Plotline of this Turning Point." ],
	[ "THE CHARACTER IS NOT CONNECTED TO THIS PLOTLINE", 78, "This Character enters the Adventure not connected to this Turning Point’s Plotline. The Character may become part of the Plotline in the course of this Turning Point, but does not start off that way. Examples include bystanders to the main events of a Turning Point or people outside the events of the Plotline who get drawn into the Adventure." ],
	[ "THE CHARACTER ASSISTS IN RESOLVING THIS PLOTLINE", 85, "This Character is someone who can help resolve the current Plotline in some way, likely serving as an aid to the Player Characters." ],
	[ "THE CHARACTER HINDERS RESOLVING THIS PLOTLINE", 92, "This Character gets in the way of resolving the current Plotline in some way, likely serving as a complication to the Player Characters." ],
	[ "THE CHARACTER IS CONNECTED TO AN EXISTING CHARACTER", 100, "This Character has some relationship to another, existing Character in this Adventure. Roll on the Characters List to see who. A result of New Character is changed to Choose The Most Logical Character. The connection can be anything, from the two Characters are related, they know each other, they were former friends, they both work in the same occupation or belong to the same organization, they look or act similarly, they have similar skills or equipment, etc. The connection can be as close or as distant as you like." ]
];

// Identity trait
let identity = aPickWeight(identities);
if (identity[0][2])
{
	identity = [];
	for (let i = 0; i < 2; i++)
	{
		let subIdentity = [];
		do
		{
			subIdentity = aPickWeight(identities);
		}
		while (subIdentity[2]);
		identity.push(subIdentity[0]);
	}
}
else
{
	identity = [ identity[0] ];
}
identity = "- Identity - " + identity.join(", ") + "\n";

// Descriptor trait
let descriptor = aPickWeight(descriptors);
if (descriptor[2])
{
	descriptor = [];
	for (let i = 0; i < 2; i++)
	{
		let subDescriptor = [];
		do
		{
			subDescriptor = aPickWeight(descriptors);
		}
		while (subDescriptor[2]);
		descriptor.push(subDescriptor[0]);
	}
}
else
{
	descriptor = [ descriptor[0] ];
}
descriptor = "- Descriptor - " + descriptor.join(", ") + "\n";

// Special trait
let special = aPickWeight(specialTraits);
special = "- Special trait - " + special[0] + "\n    - " + special[2];

// Return generated character
return expFormat("Character:\n" + identity + descriptor + special);
```
__
ac chars gen - Generates a new character description, as per the Adventure Crafter rules.
***


__
__
```js
// Get list from the list system, replacing duplicate items with text of dupe count
const getFormattedList = function(
	listName, includeCount /* 0: never, 1: if over 1, 2: always */,
	prefixType /* 0: index, 1: none, 2: period-bullets*/,
	noMarkdown)
{
	// Gather info on entries
	let entryInfo = {};
	const entriesWithDuplicates = expand("lists listraw " + listName);
	for (let i = 0; i < entriesWithDuplicates.length; i++)
	{
		if (entryInfo[entriesWithDuplicates[i]])
		{
			entryInfo[entriesWithDuplicates[i]].count++;
		}
		else
		{
			entryInfo[entriesWithDuplicates[i]] = { index: (i+1), count: 1 };
		}
	}

	// Create output from entries & info
	// (iterate over entriesWithDuplicates to maintain entry order)
	let result = [];
	let isEmpty = true;
	for (const entry of entriesWithDuplicates)
	{
		if (entryInfo[entry])
		{
			const e = entryInfo[entry];
			let countDisplay = " _(x " + e.count + ")_";
			if (noMarkdown)
			{
				countDisplay = countDisplay.replace("_(x", "(x").replace(")_", ")");
			}
			if (includeCount === 0 || (includeCount === 1 && e.count === 1))
			{
				countDisplay = "";
			}
			result.push(
				(prefixType===1 ? "" : prefixType===2 ? ". " : (e.index + " - ")) +
				entry + countDisplay);
			delete entryInfo[entry];
			isEmpty = false;
		}
	}
	if (isEmpty)
	{
		result.push("NONE");
	}

	return result;
};
```
__
Helper scripts


__
```
^ac chars? pick$
```
__
```js
// Roll the character pick (from a table of 25 entries, as seen on pg.121)
const r = roll(25);

// Get the character listed at the roll (or nothing, if no character is at the roll)
let result = expand("lists pick characters " + r)[1];

// If character was picked, return that (otherwise use table defaults: rest of code)
if (result)
{
	return expFormat([ "Character __", result, "__ picked." ]);
}

// Predefine a common message
const NEW_ENTRY_MSG = "Create a new character";

// If pick didn't have a character, get the default value of the table entry.
// ("Create a new character" is just a message, while "Choose most logical character"
//  needs extra logic, which follows this if block)
if (!result)
{
	if (r < 13)
	{
		result = r%4 ? NEW_ENTRY_MSG : "choose";
	}
	else
	{
		result = (r-1)%4 ? "choose" : NEW_ENTRY_MSG;
	}
}

// If a "Choose most logical character" entry was picked, create the message
if (result === "choose")
{
	// If there ARE characters, make the message to list them
	let list = await getFormattedList("characters", 0, 2);
	if (list.length > 1 || list[0] !== "NONE")
	{
		result = "Choose the most logical character:\n" + list.join("\n");
	}
	// If there ARE NO characters, default to "Create a new character" message
	else
	{
		result = NEW_ENTRY_MSG;
	}
}

// Return the picked table entry
return expFormat([ result ]);
```
__
ac chars pick - Picks a random character, as per the Adventure Crafter rules.


__
```
^ac chars?$
```
__
```js
// Return the list of characters
return expFormat(
	"Characters:\n" + (await getFormattedList("characters", 1, 2)).join("\n")
);
```
__
ac chars - Lists all character entries.


__
```
^ac chars? add (.+)$
```
__
```js
// Rely on the "lists" system for functionality and output
return expand("lists add characters " + $1);
```
__
ac chars add {character: text} - Adds {character} to the list of character entries.


__
```
^ac chars? dupe$
```
__
```js
// Get the list of characters
let choices = await getFormattedList("characters", 2, 1, true);

// Early-out if no characters to duplicate
if (choices.length === 1 && choices[0] === "NONE")
{
	return expFormat("No character Duplicated.  There are no characters.");
}

// Choose a character
let pick = popups.pick("Choose a character to duplicate", choices, 0, "adaptive");
if (pick === null) { return null; }

// Duplicate the chosen character
let characters = await getFormattedList("characters", 0, 1);
expand("lists add characters " + characters[pick]);

return expFormat("Character __" + characters[pick] + "__ duplicated.");
```
__
ac chars dupe - Asks the user for a character from the characters list and adds a new entry for that character.


__
```
^ac chars? reduce$
```
__
```js
// Get the list of characters
let choices = await getFormattedList("characters", 2, 1, true);

// Early-out if no characters to reduce
if (choices.length === 1 && choices[0] === "NONE")
{
	return expFormat("No character reduced.  There are no characters.");
}

// Choose a character
let pick = popups.pick("Choose a character to reduce", choices, 0, "adaptive");
if (pick === null) { return null; }

// Reduce the chosen character
let characters = await getFormattedList("characters", 0, 1);
expand("lists remove characters " + characters[pick]);

return expFormat("Character __" + characters[pick] + "__ reduced.");
```
__
ac chars reduce - Asks the user for a character from the characters list and removes one entry for that character.


__
```
^ac chars? rename$
```
__
```js
// Get the list of characters
let choices = await getFormattedList("characters", 2, 1, true);

// Early-out if no characters to rename
if (choices.length === 1 && choices[0] === "NONE")
{
	return expFormat("No character renamed.  There are no characters.");
}

// Choose a character
let pick = popups.pick("Choose a character to rename", choices, 0, "adaptive");
if (pick === null) { return null; }

let characters = await getFormattedList("characters", 0, 1);

// Enter a new name
const replacement =
	popups.input("Enter a new name for the character.", characters[pick]);
if (!replacement) { return null; }

// Rename the chosen character
expand("lists replace characters \"" + characters[pick] + "\" " + replacement);

return expFormat(
	"Character __" + characters[pick] + "__ renamed to __" + replacement + "__.");
```
__
ac chars rename - Asks the user for a character from the characters list and what to change it's text to, then changes the character's text.
***


__
```
^ac plots? pick$
```
__
```js
// Roll the plotline pick (from a table of 25 entries, as seen on pg.121)
const r = roll(25);

// Get the plotline listed at the roll (or nothing, if no plotline is at the roll)
let result = expand("lists pick plotlines " + r)[1];

// If plotline was picked, return that (otherwise use table defaults: rest of code)
if (result)
{
	return expFormat([ "Plotline __", result, "__ picked." ]);
}

// Predefine a common message
const NEW_ENTRY_MSG = "Create a new plotline";

// If pick didn't have a character, get the default value of the table entry.
// ("Create a new character" is just a message, while "Choose most logical character"
//  needs extra logic, which follows this if block)
if (!result)
{
	result = (r+2)%4 ? "choose" : [ NEW_ENTRY_MSG ];
}

// If a "Choose most logical character" entry was picked, create the message
if (result === "choose")
{
	// If there ARE characters, make the message to list them
	let list = await getFormattedList("plotlines", 0, 2);
	if (list.length > 1 || list[0] !== "NONE")
	{
		result = "Choose the most logical plotline:\n" + list.join("\n");
	}
	// If there ARE NO characters, default to "Create a new character" message
	else
	{
		result = NEW_ENTRY_MSG;
	}
}

// Return the picked table entry
return expFormat([ result ]);
```
__
ac plots pick - Picks a random plotline, as per the Adventure Crafter rules.


__
```
^ac plots?$
```
__
```js
// Return the list of plotlines
return expFormat(
	"Plotlines:\n" + (await getFormattedList("plotlines", 1, 2)).join("\n")
);
```
__
ac plots - Lists all plotline entries.


__
```
^ac plots? add (.+)$
```
__
```js
// Rely on the "lists" system for functionality and output
return expand("lists add plotlines " + $1);
```
__
ac plots add {plotline: text} - Adds {plotline} to the list of plotline entries.


__
```
^ac plots? dupe$
```
__
```js
// Get the list of plotlines
let choices = await getFormattedList("plotlines", 2, 1, true);

// Early-out if no plotlines to duplicate
if (choices.length === 1 && choices[0] === "NONE")
{
	return expFormat("No plotline Duplicated.  There are no plotlines.");
}

// Choose a plotline
let pick = popups.pick("Choose a plotline to duplicate", choices, 0, "adaptive");
if (pick === null) { return null; }

// Duplicate the chosen plotline
let plotlines = await getFormattedList("plotlines", 0, 1);
expand("lists add plotlines " + plotlines[pick]);

return expFormat("Plotline __" + plotlines[pick] + "__ duplicated.");
```
__
ac plots dupe - Asks the user for a plotline from the plotlines list and adds a new entry for that plotline.


__
```
^ac plots? reduce ?(|[1-9][0-9]*)$
```
__
```js
// Get the list of plotlines
let choices = await getFormattedList("plotlines", 2, 1, true);

// Early-out if no plotlines to reduce
if (choices.length === 1 && choices[0] === "NONE")
{
	return expFormat("No plotline reduced.  There are no plotlines.");
}

// Choose a plotline
let pick = popups.pick("Choose a plotline to reduce", choices, 0, "adaptive");
if (pick === null) { return null; }

// Reduce the chosen plotline
let plotlines = await getFormattedList("plotlines", 0, 1);
expand("lists remove plotlines " + plotlines[pick]);

return expFormat("Plotline __" + plotlines[pick] + "__ reduced.");
```
__
ac plots reduce - Asks the user for a plotline from the plotlines list and removes one entry for that plotline.


__
```
^ac plots? rename$
```
__
```js
// Get the list of plotlines
let choices = await getFormattedList("plotlines", 2, 1, true);

// Early-out if no plotlines to rename
if (choices.length === 1 && choices[0] === "NONE")
{
	return expFormat("No plotline renamed.  There are no plotlines.");
}

// Choose a plotline
let pick = popups.pick("Choose a plotline to rename", choices, 0, "adaptive");
if (pick === null) { return null; }

let plotlines = await getFormattedList("plotlines", 0, 1);

// Enter a new name
const replacement =
	popups.input("Enter a new name for the plotline.", plotlines[pick]);
if (!replacement) { return null; }

// Rename the chosen plotline
expand("lists replace plotlines \"" + plotlines[pick] + "\" " + replacement);

return expFormat(
	"Plotline __" + plotlines[pick] + "__ renamed to __" + replacement + "__.");
```
__
ac plots rename - Asks the user for a plotline from the plotlines list and what to change it's text to, then changes the plotline's text.


__
```
^sfile shutdown$
```
__
```js
// Event callback removal
delete _inlineScripts.state?.listeners?.onReset?.adventurecrafter;

// State removal
delete _inlineScripts.state?.sessionState?.adventurecrafter;

// Session state removal
delete _inlineScripts.adventurecrafter;
```
__
Shuts down this shortcut-file


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;
confirmObjectPath(
	"_inlineScripts.state.sessionState.adventurecrafter.themeSlots", []);
confirmObjectPath("_inlineScripts.state.sessionState.lists");

if (!_inlineScripts.state.sessionState.lists.plotlines)
{
	if (!_inlineScripts.state.sessionState.lists.threads)
	{
		confirmObjectPath(
			"_inlineScripts.state.sessionState.lists.plotlines",
			{ type: "basic", content: [] });
		delete _inlineScripts.state.sessionState.lists.plotline_dupes;
	}
	else
	{
		confirmObjectPath(
			"_inlineScripts.state.sessionState.lists.plotline_dupes",
			{ type: "basic", content: [] });
		confirmObjectPath(
			"_inlineScripts.state.sessionState.lists.plotlines",
			{ type: "combo", content: ["threads", "plotline_dupes"] });
	}
}

if (!_inlineScripts.state.sessionState.lists.characters)
{
	if (!_inlineScripts.state.sessionState.lists.pcs ||
	    !_inlineScripts.state.sessionState.lists.npcs)
	{
		confirmObjectPath(
			"_inlineScripts.state.sessionState.lists.characters",
			{ type: "basic", content: [] });
		delete _inlineScripts.state.sessionState.lists.character_dupes;
	}
	else
	{
		confirmObjectPath(
			"_inlineScripts.state.sessionState.lists.character_dupes",
			{ type: "basic", content: [] });
		confirmObjectPath(
			"_inlineScripts.state.sessionState.lists.characters",
			{ type: "combo", content: ["pcs", "npcs", "character_dupes"] });
	}
}

confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.adventurecrafter",
	function()
	{
		expand("adventurecrafter reset noconfirm");
	});

confirmObjectPath(
	"_inlineScripts.adventurecrafter.themes",
	[
		"ACTION", "TENSION", "MYSTERY", "SOCIAL", "PERSONAL"
	]);

confirmObjectPath(
	"_inlineScripts.adventurecrafter.themeDescriptions",
	[
		"Direct and physical",
		"Anxiety provoking (for the characters at least)",
		"Questions: raising and answering",
		"Interaction of people and/or factions",
		"Personal situations (for the characters)"
	]);

confirmObjectPath(
	"_inlineScripts.adventurecrafter.plot_meta",
	[ [ "CHARACTER EXITS THE ADVENTURE", 18, "A Character, who is not a Player Character, is removed from the Characters List completely. Cross out all references to that Character on the Characters List. If there are no non-Player Characters, then re-roll for another Meta Plot Point. This change can be reflected in the activity in this Turning Point or not. For instance, you may explain the Character being removed from the Adventure by having that Character die in the Turning Point. Or, you simply remove them from the Characters List and decide that their involvement in the Adventure is over. If, when rolling on the Characters List to determine who this Character is, you roll a Player Character or \"New Character\", then consider it a result of \"Choose The Most Logical Character\"." ],
	[ "CHARACTER RETURNS", 27, "A Character who previously had been removed from the Adventure returns. Write that Character back into the Characters List with a single listing. If there are no Characters to return, then treat this as a \"New Character\" result and use this Plot Point to introduce a new Character into the Turning Point. If there is more than one Character who can return, then choose the most logical Character to return. This change can be reflected in the activity in this Turning Point or not." ],
	[ "CHARACTER STEPS UP", 36, "A Character becomes more important, gaining another slot on the Characters List even if it pushes them past 3 slots. When you roll on the Characters List to see who the Character is, treat a result of \"New Character\" as \"Choose The Most Logical Character\". This change can be reflected in the activity in this Turning Point or not." ],
	[ "CHARACTER STEPS DOWN", 55, "A Character becomes less important, remove them from one slot on the Characters List even if it removes them completely from the List. If this would remove a Player Character completely from the List, or if when rolling for the Character you get a result of \"New Character\", then treat this as a result of \"Choose The Most Logical Character\". If there is no possible Character to choose without removing a Player Character completely from the List, then roll again on the Meta Plot Points Table. This change can be reflected in the activity in this Turning Point or not." ],
	[ "CHARACTER DOWNGRADE", 73, "A Character becomes less important, remove them from two slots on the Characters List even if it removes them completely from the List. If this would remove a Player Character completely from the List, or if when rolling for the Character you get a result of \"New Character\", then treat this as a result of \"Choose The Most Logical Character\". If there is no possible Character to choose without removing a Player Character completely from the List, then roll again on the Meta Plot Points Table. This change can be reflected in the activity in this Turning Point or not." ],
	[ "CHARACTER UPGRADE", 82, "A Character becomes more important, gaining 2 slots on the Characters List even if it pushes them past 3 slots. When you roll on the Characters List to see who the Character is, treat a result of \"New Character\" as \"Choose The Most Logical Character\". This change can be reflected in the activity in this Turning Point or not." ],
	[ "PLOTLINE COMBO", 100, "This Turning Point is about more than one Plotline at the same time. Roll again on the Plotlines List and add that Plotline to this Turning Point along with the original Plotline rolled. If when rolling for an additional Plotline you roll the same Plotline already in use for this Turning Point, then treat the result as a \"Choose The Most Logical Plotline\". If there are no other Plotlines to choose from, then create a new Plotline as the additional Plotline. If a Conclusion is rolled as a Plot Point during this Turning Point, apply it to the Plotline that seems most appropriate. If another Conclusion is rolled, continue to apply them to the additional Plotlines in this Turning Point if you can. It is possible with repeated results of \"Plotline Combo\" to have more than two Plotlines combined in this way." ]
]);

confirmObjectPath(
	"_inlineScripts.adventurecrafter.plot",
	[ [ "CONCLUSION", 8,8,8,8,8, "If this Turning Point is currently a Plotline Development, then it becomes a Plotline Conclusion. Incorporate anything necessary into this Turning Point to end this Plotline and remove it from the Plotlines List. If this Turning Point is a New Plotline or already a Conclusion, then consider this Plot Point a None." ],
	[ "NONE", 24,24,24,24,24, "Leave this Plot Point blank and go on to the next Plot Point, unless it would leave you with fewer than 2 Plot Points in this Turning Point, in which case re-roll.", 1 ],
	[ "INTO THE UNKNOWN", 0,26,26,0,0, "This Turning Point involves Characters entering a situation with unknown factors. To know the unknown, you have to commit to it. For instance, a magic portal where there is no way of knowing what’s on the other side except by walking through it. Or, you discover a machine that is very powerful but you have no idea what it does, except if you turn it on. The only way to discover the unknown is to engage it, when it will be too late if you regret it." ],
	[ "A CHARACTER IS ATTACKED IN A NON-LETHAL WAY", 26,0,0,0,0, "A Character is attacked, but the assailant will not attack to kill." ],
	[ "A NEEDED RESOURCE RUNS OUT", 0,27,0,0,0, "A resource a Character needs has run out. The lack will cause problems. For instance, traveling a dinosaur filled jungle and running out of ammunition." ],
	[ "USEFUL INFORMATION FROM AN UNKNOWN SOURCE", 0,0,28,0,0, "A Character receives useful information from an anonymous source. Perhaps a note is found laying on your doorstep, or an email appears in your inbox with a photo that reveals something to the Character. Whatever the information is, it should impact the Plotline." ],
	[ "IMPENDING DOOM", 0,28,0,0,0, "Something terrible is going to happen, and it is approaching. For instance, an enemy army is advancing to invade and will be at the borders in a week." ],
	[ "OUTCAST", 0,0,0,26,0, "A Character is considered an outcast by other Characters for some reason. Maybe the Character is part of an ethnic group that is disliked in the area, or perhaps the Character is popularly believed to be the perpetrator of a heinous crime." ],
	[ "PERSUASION", 0,0,0,0,26, "A Character tries to persuade another Character to do something. This persuasion can take many forms, from pleading with them to threatening them, for instance." ],
	[ "A MOTIVE FREE CRIME", 0,29,30,0,0, "A crime is committed either in this Turning Point or is learned about in this Turning Point, with no clear reason why the crime was committed. Maybe someone was murdered for no obvious reason, or a building was broken into with nothing stolen." ],
	[ "COLLATERAL DAMAGE", 27,30,0,0,0, "Whatever is going on in this Turning Point, the activity will spill over from the focus of that activity to things around it. This is particularly true for damaging events. For instance, a superhero defeats a villain in a downtown brawl, but doing significant damage to the buildings around them in the process. The collateral damage does not have to be physical. For instance, it could be the legal fallout from a major court decision." ],
	[ "SHADY PLACES", 0,32,0,0,0, "This Turning Point involves a location that is less than legitimate, such as a back alley where drug deals are commonly transacted or a secret gambling hall in a bar." ],
	[ "A CHARACTER IS ATTACKED IN A LETHAL WAY", 29,0,0,0,0, "An assailant is trying to kill a Character." ],
	[ "DO IT, OR ELSE", 0,33,0,0,27, "A Character is being given a task, and is being pressured into completing the task with a threat. For instance, a spy is forcing a diplomat to hand over technology secrets or he will expose the diplomat’s illegal activities and send him to jail. Of course, probably the most common form of this Plot Point is “Do this or I will kill you”." ],
	[ "REMOTE LOCATION", 0,34,0,0,0, "This Turning Point involves a remote location, such as a cave or a cabin in the woods." ],
	[ "AMBUSH", 31,0,0,0,0, "Whatever is happening in this Turning Point involves sudden action at an unexpected time." ],
	[ "SOLD!", 0,0,0,28,0, "This Turning Point involves a sale of some kind. Maybe goods are being sold, or information is being bought. Whatever is happening, goods and money are exchanging hands." ],
	[ "CATASTROPHE", 32,35,0,0,0, "Just about the worst thing that can happen does happen, and it happens spectacularly and with much action. This could be the impregnable fortress that gets sacked, the unstoppable superhero who gets defeated, the unsinkable ship that starts to sink." ],
	[ "GRISLY TONE", 0,36,0,0,0, "Whatever is going on in this Turning Point, the tone of it is grisly, something that causes horror or disgust. For instance, if a note is discovered with a grisly tone it may be smeared in blood or be accompanied by a severed hand." ],
	[ "CHARACTER HAS A CLEVER IDEA", 33,0,0,0,0, "A Character has an idea that has an impact on this Turning Point. For instance, the con man speaks up and just happens to know a secret way through the sewers into the walled city." ],
	[ "SOMETHING IS GETTING AWAY", 34,37,0,0,0, "This Turning Point involves a time limit where, at the end of it, something will get away. For instance, a ship carrying a magic artifact is about to leave the dock and a Character has to fight their way through a pack of armed goons to board the ship before it sets sail." ],
	[ "RETALIATION", 0,39,0,30,28, "Whatever is happening in this Turning Point, it involves an element of retaliation or revenge." ],
	[ "A CHARACTER DISAPPEARS", 0,40,32,0,0, "A Character is nowhere to be found. Whether there is evidence or not as to what happened to the Character is up to you depending on the other Plot Points involved in this Turning Point." ],
	[ "HUNTED", 36,41,0,0,0, "A Character is being hunted by someone or something that is not strictly legitimate. In other words, as opposed to Wanted By The Law, Hunted may mean a hit man is pursuing a Character to fulfill a mafia contract on them, or a ghost may be after a Character. The hunter doesn’t have to be seeking to kill." ],
	[ "A HIGH ENERGY GATHERING", 0,0,0,31,0, "This Turning Point involves a social gathering with a great deal of energy or activity. This could be a busy nightclub, a loud party, or a sporting event, for instance." ],
	[ "A RARE OR UNIQUE SOCIAL GATHERING", 0,0,0,32,0, "This is a social gathering for a specific and rare purpose. Examples would include funerals or a wedding." ],
	[ "BAD DECISION", 0,42,0,0,29, "A decision a Character made has turned out to be a very bad one. This can be a decision made earlier in the Adventure, or it can be something from before the Adventure. This earlier decision may not have seemed like a bad one at the time, but it has turned out to be bad, either for the Character, for others, or both. For instance, maybe a ship’s captain decided to investigate a distress beacon in deep space, only to find it’s a trap laid by pirates." ],
	[ "THIS ISN’T WORKING", 0,0,33,0,0, "Something that is supposed to be working is not for some reason, causing a problem. For instance, a binding spell is failing to hold a demon, or a crime boss is delivering stolen goods through a shipping port that is supposed to be secure but turns out to be swarming with police. Whatever isn’t working is something that was assumed would work." ],
	[ "DISTRACTION", 37,0,0,0,0, "A Character is distracted in this Turning Point in such a way that it impacts events. For instance, before a villain delivers his killing blow he’s distracted by an image of his lost love, giving the hero time to escape." ],
	[ "ILL WILL", 0,0,0,0,31, "A Character harbors ill will toward another Character for some reason. The animosity should be deep seated and color the Character’s reactions when it comes to the unliked Character. The dislike may be reciprocated or not." ],
	[ "AN ORGANIZATION", 0,0,0,34,0, "This Turning Point involves an organization of some kind. This can be an organization already in the Characters List or not. Whatever is happening in this Turning Point, the organization is formally involved in some way. For instance, a crime has been committed and a local guild had knowledge of it and covered it up to protect its own interests." ],
	[ "WANTED BY THE LAW", 0,43,0,0,33, "A Character is wanted for a crime. It doesn’t matter if they actually did the crime, but the law is after them as the main suspect either way." ],
	[ "A RESOURCE DISAPPEARS", 0,0,35,0,0, "An important object or resource is stolen by an unknown thief. The resource should be something either useful to a Character, or it should pertain to the Plotline in question." ],
	[ "IT IS YOUR DUTY", 0,0,0,0,35, "A Character is charged with carrying out a duty. This should be something that the Character has little choice in the matter, whether they want to do it or not. Whoever the duty is coming from, that source has authority over the Character. For instance, a soldier wants to join in the pivotal battle but his commander gives him the duty of guarding the fortress gate instead." ],
	[ "FORTUITOUS FIND", 0,0,36,0,0, "A Character runs across something very useful for resolving the Plotline. This may be a piece of information, a useful tool, a resource that is needed, a person who can help, etc. Whatever it is, it’s the right thing at the right time, and it falls into the Character’s lap." ],
	[ "CHARACTER CONNECTION SEVERED", 0,0,0,0,37, "A Character who has a connection with another Character severs that connection. This can happen for any of a number of reasons, from the Character dropping out of the story to the Character getting angry at the other Character for something. The severed connection does not have to be permanent." ],
	[ "ALL IS REVEALED!", 0,0,37,0,0, "A source in this Turning Point gives a lot of detail about something. For instance, a guard is captured and tells where the king has hidden the Sacred Scrolls." ],
	[ "HUMILIATION", 0,0,0,0,38, "This Turning Point involves a Character being humiliated or facing humiliation. Whatever is happening, it should be something deeply embarrassing to the Character. For instance, a member of an unpopular community is being bullied and mocked, or a public figure has something personal publicly exposed." ],
	[ "PEOPLE BEHAVING BADLY", 0,0,0,35,0, "This Turning Point involves someone behaving in a socially unacceptable way. For example, a group of drunks throwing bottles, or a heckler in a crowd yelling at a speaker." ],
	[ "USEFUL INFORMATION FROM A KNOWN SOURCE", 0,0,39,0,0, "A Character acquires useful information from a known source. For instance, a detective investigating a homicide gets a tip from an informant she sometimes uses, giving her a clue." ],
	[ "CRYPTIC INFORMATION FROM A KNOWN SOURCE", 0,0,40,0,0, "A Character acquires information that is not immediately useful from a known source. The information is cryptic, the Character doesn’t know what it means. For instance, a crewmember leaves behind a note to be found that simply says, “Kraton,” where the Character receiving the note has no idea what “Kraton” is." ],
	[ "LIE DISCOVERED", 0,0,42,0,0, "This Turning Point involves the discovery of a lie. The lie could have happened within this Turning Point, or it could have happened earlier in the Adventure or even before the Adventure. For instance, Characters may learn that the detective did not destroy the cult artifact like he said he did, but instead took it home to try and summon the Beast From Beyond." ],
	[ "A CHARACTER IS ATTACKED TO ABDUCT", 39,0,0,0,0, "An assailant is attempting to abduct a Character." ],
	[ "SOMETHING EXOTIC", 40,44,43,0,0, "Whatever is happening in this Turning Point it involves an unusual or exotic element. For instance, if the Turning Point is about someone being attacked by an assassin, the assassin may have a very unusual identity or mode of attack (maybe he’s disguised as a clown and attacks with exploding balloons, or he is a martial artist with fantastic moves)." ],
	[ "IMMEDIATELY", 42,45,0,0,0, "Immediate action is required in this Turning Point, whatever is going on. For instance, if this Turning Point involves engine failure on a starship, the Character doesn’t have days to resolve the issue, he may only have an hour. Whatever is going on, it requires immediate action." ],
	[ "FAME", 0,0,0,36,0, "Whatever is happening in this Turning Point involves someone famous to some extent. This doesn’t necessarily mean that a famous Character is Invoked, just that the Turning Point has some connection to fame. For instance, if this Turning Point involves learning a secret about another Character, you may learn that they were once a member of a famous superhero group decades ago." ],
	[ "CHASE", 44,0,0,0,0, "This Turning Point involves a chase, where one Character is pursuing another." ],
	[ "BETRAYAL!", 0,46,0,0,40, "A Character, who was thought to be an ally or to be benign, turns on another Character. This can be a fundamental betrayal, such as they are actually on opposing sides, or it can be a momentary betrayal, such as attacking someone out of a fit of anger." ],
	[ "A CRIME IS COMMITTED", 0,0,45,0,0, "A crime is committed either in this Turning Point or is learned about in this Turning Point." ],
	[ "A CHARACTER IS INCAPACITATED", 0,47,0,0,42, "A Character is rendered out of commission for some reason. Perhaps they are wounded badly, they lose their powers, are trapped somewhere, etc." ],
	[ "IT’S A SECRET", 0,0,47,0,0, "This Turning Point involves an activity that is done in secret, such as smuggling or embezzlement. The activity doesn’t have to be illegal, but whatever it is, it is something hidden or being done behind an otherwise legitimate front. For instance, a fast food chain is using it’s delivery trucks to smuggle drugs across the border." ],
	[ "SOMETHING LOST HAS BEEN FOUND", 0,0,48,0,0, "Something that has been lost turns up in this Turning Point. The thing could have been lost in this Adventure or before. It can be an object, a person, or anything. For instance, a ring of power suddenly turns up in a creek bed, or a Character who disappeared early in the Adventure suddenly makes a reappearance." ],
	[ "SCAPEGOAT", 0,0,0,37,0, "This Turning Point involves an innocent Character accused of wrongdoing to throw suspicion off of the real culprit. For instance, the woman who took all the ammo blames the newcomer to the zombie survivalist group, or the mayor of the little New England town blames the practitioners of a religion for the bizarre events going on when he is actually at fault." ],
	[ "NOWHERE TO RUN", 0,48,0,0,0, "A Character faces a peril with no means to escape." ],
	[ "AT NIGHT", 0,50,0,0,0, "This Turning Point takes place at night." ],
	[ "THE OBSERVER", 0,0,49,38,43, "Whatever is happening in this Turning Point that is presumed to be private from someone, is actually being witnessed or observed. The observed are not aware of this observer. For instance, two enemy generals are meeting in secret to form an alliance and betray their respective kings, but the meeting is observed by a princess who knows exactly what it means." ],
	[ "ESCAPE", 46,0,0,0,0, "This Turning Point involves an escape of some sort. For instance, a Character who was captured by brigands in an earlier Turning Point manages to slip away from his captors and escape into the forest." ],
	[ "A SECRET WEAPON", 0,51,50,0,0, "This Turning Point involves the reveal of a secret weapon in possession by a Character. This weapon should be significant enough to sway the balance of power or to otherwise require a solution to resolve. For instance, the motley band of orcs is unexpectedly backed by a large ogre whose aid they enlisted. Or, the galactic empire unveils a new, planet-busting warship that changes everything" ],
	[ "HEAVILY GUARDED", 48,52,0,0,0, "This Turning Point involves entering a heavily guarded and dangerous location. For instance, this could be needing to infiltrate a high tech security facility to steal information, or breaking into the necromancers lair full of guardian zombies to destroy his magic crystal." ],
	[ "RESCUE", 50,0,0,0,0, "A Character needs to be rescued in this Turning Point." ],
	[ "LIAR!", 0,0,52,39,0, "This Turning Point involves an active lie. The lie is being committed in this Turning Point. Something someone said or claimed is false. For instance, a vampire lord claims he knows nothing of a magic book, when actually he is seeking it himself. The lie may or may not be detected in this Turning Point." ],
	[ "HOME SWEET HOME", 0,0,0,0,45, "This Turning Point takes place in the private home of a Character." ],
	[ "A CHARACTER ACTS OUT OF CHARACTER", 0,0,53,0,0, "A Character does something that runs counter to that Character’s perceived goals or personality. The action may seem at odds to how they’ve been acting (such as a trusted member of a team sabotaging a crucial resource) or the action is vague with no discernible purpose (such as a Character meeting with an unknown person in secret)." ],
	[ "HEADQUARTERS", 0,0,0,41,46, "A setting in this Turning Point is a Character’s main headquarters. For instance, it may be the ritzy bar where the mob boss runs his empire, or the wizard’s wilderness tower." ],
	[ "PHYSICAL CONTEST OF SKILLS", 52,0,0,0,0, "This Turning Point involves Characters squaring off against each other in a physical contest of skills. This can be anything such as combat, a sporting event, duel, arm wrestling contest, etc." ],
	[ "DEAD", 0,53,54,0,0, "A Character is dead. This can either be expected or unexpected, but whatever the circumstances, this Turning Point involves a dead Character." ],
	[ "A COMMON SOCIAL GATHERING", 0,0,0,43,0, "This Turning Point involves a social gathering. This can be any gathering of people, generally for a common purpose, such as gathering for dinner at a home or restaurant, or an afternoon at a mall. The social gathering itself should be considered of a mundane nature, although what else transpires at the gathering doesn’t necessarily have to be." ],
	[ "LIGHT URBAN SETTING", 0,0,0,45,0, "This Turning Point takes place in a light urban setting, such as a small town or village." ],
	[ "MYSTERY SOLVED", 0,0,56,0,0, "A mystery is solved. This can be a large, unanswered question in the Adventure or something minor, but it is not a Plotline resolved unless this Turning Point is also a Plotline Conclusion. A Mystery Solved could be any number of things, from finally figuring out what a device does to locating the missing Chancellor." ],
	[ "A WORK RELATED GATHERING", 0,0,0,47,0, "This is a social gathering that involves professionals or workers. The gathering itself may or may not involve their actual work. For instance, police officers gathering at a “cop bar” or a team of super heroes gathering at their headquarters would both count." ],
	[ "FAMILY MATTERS", 0,0,0,0,48, "This Turning Point involves a family member or members of a Character. For instance, an occult investigator is about to head off on a mission when his sister unexpectedly appears on his doorstep, or one of the Characters has an uncle who is a feudal lord and is summoning them for their help in defending his land because no one else will stand by him." ],
	[ "SECRET INFORMATION LEAKED", 0,0,57,0,0, "Information that should not have gotten into the wrong hands has. For instance, outlaws always seem to know when the stagecoach is coming through Gateway Gulch with the railroad payroll. How are they finding out? Or, an enemy spy has learned of the realm’s secret military plans." ],
	[ "SUSPICION", 0,54,59,48,0, "This Turning Point involves a Character being suspicious of another Character for some reason. For instance, a beloved leader on a space station is murdered and suddenly every newcomer on board is viewed with suspicion." ],
	[ "LOSE LOSE", 0,55,0,0,0, "This Turning Point involves a choice where both or all options are bad in some way." ],
	[ "A FIGURE FROM THE PAST", 0,0,0,0,49, "A new Character joins the Adventure, someone from a Character’s past. This Plot Point requires a new Character to be added to the Characters List and Invoked." ],
	[ "MASS BATTLE", 54,0,0,0,0, "This Turning Point involves a combat between many combatants. This can be a throw down between two teams or a battle in a war, for instance." ],
	[ "OUT IN THE OPEN", 0,56,0,0,0, "Whatever is happening in this Turning Point, it is happening out in the open for all to see. For instance, a Character is attacked at a public festival in the middle of the day, or, something a Character is doing that they thought is private is actually being filmed and viewed by others." ],
	[ "EVIDENCE", 0,0,61,0,0, "A Character finds something that helps settle an existing question. For instance, the gun that killed a victim is found stashed under a suspect’s bed." ],
	[ "A CHARACTER IS DIMINISHED", 0,58,0,0,51, "A Character is reduced in some way that makes them less effective. Perhaps they are wounded, or their energy is low, or they lose some authority, etc. The Character is not entirely powerless, but loses a significant portion of their power or utility." ],
	[ "THE PLOT THICKENS", 0,0,63,0,0, "A promising lead or clue to solving an open question turns out to be a dead end. For instance, Characters follow through on a tip to go to a warehouse to find an abducted heiress, but instead of finding a nest of bad guys they just find an empty building." ],
	[ "ENEMIES", 0,59,0,49,53, "This Turning Point involves enemies of a Character. Whatever activity is going on in this Turning Point, those enemies play an important role." ],
	[ "DUBIOUS RATIONALE", 0,0,64,0,0, "A Character does something that is in keeping with their Character, but the action could also have been for another reason and it is not clear which reason the Character acted on. For instance, the CEO goes into his office late at night, as he sometimes does, on the same night another executive is murdered. The action should seem innocent, except for other events or information that cast doubt on it." ],
	[ "MENACING TONE", 0,60,0,0,0, "This Turning Point involves a menacing tone of some kind. For instance, one Character may be threatening another Character, or a villain may be gloating over a captured opponent." ],
	[ "A CRUCIAL LIFE SUPPORT SYSTEM BEGINS TO FAIL", 55,61,65,0,0, "This can be an actual life support system, like the oxygen ventilation of a starship, or a safety system, like the brakes on a car. The failure will constitute an emergency for the Characters involved." ],
	[ "DENSE URBAN SETTING", 0,0,0,51,0, "This Turning Point takes place in a heavily urban area, such as a large city." ],
	[ "DOING THE RIGHT THING", 0,0,0,0,54, "A Character who is acting in bad faith in some way has a change of heart and decides to do the right thing. For instance, a con man stealing medicine from a diseased community decides he can’t leave all those people to die." ],
	[ "VICTORY!", 57,62,0,0,0, "A Character achieves a victory over another Character in this Turning Point. For instance, a band of marauders successfully waylay the king’s couriers, or a hacker worms his way into a corporate computer system." ],
	[ "TAKING CHANCES", 59,63,0,0,0, "A Character acts in a very risky way. For instance, a Character may suddenly show no regard for their life as they walk out across a narrow beam above a valley to save a friend. Or, the villain you are fighting takes a drug that makes him go into a battle frenzy where he loses all caution." ],
	[ "A GROUP IS IN TROUBLE", 0,0,0,53,0, "A group, such as a community, is in trouble in this Plot Point. The group or community is facing a difficulty. For instance, maybe a village is being harassed by monsters, or a corporation is facing a lawsuit that could destroy it. Whatever the trouble is, it should be something that can be solved and will likely constitute a problem for a Character." ],
	[ "SOLE SURVIVOR", 61,64,0,0,0, "This Turning Point involves some kind of process of elimination where there is only one left. This can be a battle, but doesn’t have to be. For instance, maybe a sinking ship has a single survivor who washes up on shore, or a group of crewmen from a starship playing chess with an alien intelligence is down to their last crewmember who is now chosen for the alien’s ultimate challenge." ],
	[ "TOKEN RESPONSE", 0,0,0,54,0, "A Character or organization acting in this Turning Point does the bare minimum to address a problem, or makes just a token effort, as opposed to doing something truly effective. For instance, a notorious space pirate has been captured, but instead of receiving serious prison time, the federation government goes very lenient on him and releases him from prison in a week." ],
	[ "CRYPTIC INFORMATION FROM AN UNKNOWN SOURCE", 0,0,67,0,0, "Information that is unclear what it means is received from an anonymous source. Maybe an odd word is found scrawled on a mirror, or a stranger’s diary is found talking about events similar to the Plotline." ],
	[ "A COMMON THREAD", 0,0,69,0,0, "It is learned that events that appeared to be unrelated have a commonality after all. For instance, a rash of crimes has beset the city, from car jackings to break ins. It turns out the culprits all work as security guards in the same building." ],
	[ "A PROBLEM RETURNS", 0,66,0,0,0, "A problem that had been thought resolved returns in some fashion. This can be a problem from this Adventure, from a previous Adventure, or something inferred from the past. For instance, a kingdom may be enjoying a decade of peace following the vanquishing of the Dark Lord, but it is discovered that he is not dead and is now returning. The magnitude of the problem is open to interpretation and can range from large to minor, such as a previously sealed leak in a boat has sprung open again." ],
	[ "STUCK", 0,68,0,0,0, "A Character is stuck in this Turning Point, unable to act, while the events of the Turning Point transpire. Whatever has them stuck is not necessarily permanent, but at the moment it renders them powerless or mostly powerless. For instance, maybe the character is bound or trapped in a jail cell." ],
	[ "AT YOUR MERCY", 0,0,0,0,56, "A Character is helpless and desperate for some reason, and must rely on the mercy of another Character who has the power to address their problem. For instance, a Character is afflicted with a magical curse that only one sorcerer can cure." ],
	[ "STOP THAT", 63,0,0,0,0, "A Character takes action to stop something from happening in this Turning Point. The action could be expected, such as a hero putting an arrow through the executioner before he drops his axe. Or, it could be unexpected, like a Character suddenly shooting a captured villain right before he was about to reveal crucial details." ],
	[ "NOT THEIR MASTER", 0,0,70,55,0, "A Character in this Turning Point who is assumed to be working for one source turns out to be working for another. For instance, the hitman who’s been trying to kill a Character doesn’t work for the mafia like you thought, but for a corporation who has an interest in that Character." ],
	[ "FALL FROM POWER", 0,0,0,0,58, "A Character loses their power in this Turning Point. For instance, a king is found to be a fraud by his brother, who asserts his own claim to the throne and takes it." ],
	[ "HELP IS OFFERED, FOR A PRICE", 0,0,0,0,60, "A Character offers to help another Character in exchange for something. What’s being asked for could be anything, from mutual aid to a fee. Whatever the price, it should be steep enough to be of real significance to the paying Character." ],
	[ "PUBLIC LOCATION", 0,0,0,57,0, "This Turning Point involves a public location, such as a town square or a park in the middle of the day." ],
	[ "THE LEADER", 0,0,0,59,0, "This Turning Point involves the leader of someone or some organization." ],
	[ "PRIZED POSSESSION", 0,0,0,0,62, "Whatever is happening in this Turning Point, it involves an important possession of a Character. For instance, if the Turning Point is about something being stolen, maybe a sorcerer’s magic staff is taken." ],
	[ "SAVIOR", 0,0,0,61,0, "A Character is involved in this Turning Point who offers to save the day." ],
	[ "DISARMED", 0,70,0,0,63, "A Character loses their primary method of defending themselves. This could mean the loss of a weapon, or maybe a powerful bureaucrat is powerless in another’s kingdom, etc. The disarmament should be temporary for the Turning Point and deprive the Character of crucial defenses." ],
	[ "THE SECRET TO THE POWER", 0,0,72,0,0, "There is a power, and it has a secret source. For instance, an evil wizard may derive his abilities from his ancient staff, or the warship hurtling through space may be dependent on a simple power core inside that will cripple the ship if it is damaged. This secret gives Characters an option to stop an otherwise overwhelming or powerful problem." ],
	[ "HIDDEN AGENDA", 0,0,74,0,0, "A Character either reveals, or is found out to have, a motive that they had not previously exposed. For instance, maybe the detective isn’t investigating the murder out of dedication to his job, but the victim used to be a love interest of his. Classically, this can also be the ally who turns out to be an enemy. The hidden agenda doesn’t have to be something nefarious, although it can be. Whichever the case, the agenda now becomes known to others." ],
	[ "DEFEND OR NOT TO DEFEND", 65,0,0,0,0, "This Turning Point involves a confrontation between two Characters, where another Character views it and has the option to intervene or not. The observing Character is not directly part of the confrontation, but will become so if they step in. This Plot Point calls for three Characters to be Invoked." ],
	[ "CRASH", 67,0,0,0,0, "This Turning Point involves a vehicle carrying a Character to crash or threaten to crash. The Character(s) involved must either mitigate the damage of the crash, prevent the crash in the first place, and/or survive the crash. The vehicle can be anything from a plane to a car to a snow sled ... anything that can transport a Character and its crashing would be dangerous." ],
	[ "REINFORCEMENTS", 0,0,0,63,0, "A Character who is running low on a human resource gets a boost. For instance, the battle is going poorly for King Leonard, but just before they lose King Ferdinand appears on the hill with his forces ready to save the day." ],
	[ "GOVERNMENT", 0,0,0,65,0, "This Turning Point involves government in some way. Maybe a Character has to deal with a border crossing checkpoint, or a starship needs to get proper authorization to leave port." ],
	[ "PHYSICAL BARRIER TO OVERCOME", 69,0,0,0,0, "A Character faces a physical barrier of some sort that must be overcome. It could be a cliff that needs to be climbed, a rickety bridge to cross, a door that needs to be knocked down, etc. Whatever the barrier is, it will require physical action to get past." ],
	[ "INJUSTICE", 0,0,0,67,0, "This Turning Point involves a social injustice of some kind. For instance, a corrupt politician uses a civic ordinance to foreclose on an apartment building where friends of a certain hero, who has upset the politician, live." ],
	[ "QUIET CATASTROPHE", 0,71,0,0,0, "Just about the worst thing that can happen does happen. This is similar to the Action Plot Point Catastrophe, except that it is accompanied by less action. For instance, a colonizing spaceship stops midway through a 40 year journey, waking everyone up from their cryo sleep. Or, the investigator discovers the ancient vampire he had destroyed is, somehow, back." ],
	[ "AN OBJECT OF UNKNOWN USE IS FOUND", 0,0,75,0,0, "A Character finds something that they think is useful, but they do not know in what way. This may be a magic wand that they don’t know how to use, a key that they don’t know the lock it goes to, a device with an unknown purpose but currently has no power, etc." ],
	[ "IT’S ALL ABOUT YOU", 0,0,0,0,65, "Whatever the main action of this Turning Point, it is focused primarily on one Character." ],
	[ "A CELEBRATION", 0,0,0,69,0, "This Plot Point involves a celebration of some sort, such as a birthday party or a high school graduation party." ],
	[ "STANDOFF", 0,72,0,70,0, "This Turning Point involves two or more Characters in a tense standoff. For instance, a group of mercenaries have the Characters pinned down behind rubble with gunfire, while the Characters fire back. Neither side can take out the other, but neither can they leave without resolving the conflict." ],
	[ "DOUBLE DOWN", 71,0,0,0,0, "Whatever is happening in this Turning Point, those events will intensify. For instance, if a ship is leaking on the high seas during a storm, maybe torrential winds tear down the sails." ],
	[ "HIDDEN THREAT", 0,73,0,0,0, "There is a threat in this Turning Point that has been in the Adventure previous to this Turning Point but went undetected. This could be anything from an evil spirit lurking in an ancient vase to a virus in a person’s body to a good guy who turns out to be a bad guy, etc." ],
	[ "CHARACTER CONNECTION", 0,0,0,0,67, "A Character forms a connection with another Character. This connection can be anything from showing a personal interest in the Character to asking them to become a business partner, etc. Whatever the connection is, it will have a lasting impact beyond this Turning Point." ],
	[ "RELIGION", 0,0,0,71,0, "This Turning Point involves some aspect of religion or religious belief. For instance, maybe an event is taking place at a church, or Characters stumble upon a cult preparing a magic ritual for their otherworldly god." ],
	[ "INNOCENCE", 0,0,0,72,68, "This Turning Point involves an element of innocence, usually an innocent person in an otherwise less than innocent situation. For instance, an average citizen finds herself in the middle of two vampires battling. This can also be considered a “fish out of water” Plot Point, where someone who does not belong in a situation finds themselves in that situation." ],
	[ "CLEAR THE RECORD", 0,0,76,0,0, "A Character is given the task of clearing someone or something of a false claim. For instance, a friend says they are wrongly convicted of a crime and that the evidence is out there to prove it. The task may come to the Character officially, given by another Character, or it may be something that falls into their lap, such as discovering the truth themselves and only they know it. For instance, a foreign power has staged a catastrophe to start a war, but a handful of Characters know the truth ... if only they can reach headquarters in time to tell them before warships are launched." ],
	[ "WILLING TO TALK", 0,0,0,0,70, "A Character is in a mood to talk. Whatever it is they have to say, it’s important to furthering the Plotline." ],
	[ "THEFT", 73,0,0,0,0, "This Turning Point involves a theft, whether attempted or successful. What is being stolen is an object of some kind, or information, or anything that can be taken. This Turning Point involves the actual activity and action of the theft or attempted theft. For instance, the Character is strolling through a museum when a group of men burst in to steal a ritual mask." ],
	[ "CHARACTER HARM", 0,0,0,0,72, "A Character hurts another Character in some personal way. For instance, a villain harms a wizard’s familiar or a Character hurls a personal insult at another Character." ],
	[ "A NEED TO HIDE", 0,75,0,0,0, "A Character must hide from something or someone in this Turning Point. For instance, the Character may have escaped from a bounty hunter but must hide long enough to recover their wounds. Or, a terrible storm has struck and the Character must take shelter, hiding from the storm." ],
	[ "FOLLOWED", 0,77,0,0,0, "A Character is being followed by another Character." ],
	[ "FRAMED", 0,0,77,0,73, "A Character is unfairly framed by another Character. For instance, a mob boss plants evidence to make it look like a police detective has committed a crime." ],
	[ "PREPARATION", 0,0,0,74,75, "This Turning Point involves a Character needing to prepare for something. For instance, a wizard must study up on how to banish demons before a villain arrives, or a town of prospectors and merchants must learn how to fight before the band of outlaws arrives to exact their revenge for hanging a comrade." ],
	[ "AN IMPROBABLE CRIME", 0,0,78,0,0, "This Turning Point involves a crime that seems either improbable or impossible to have occurred, such as someone found murdered in a secure room or a piece of artwork stolen from a museum with no visible break in." ],
	[ "FRIEND FOCUS", 0,0,0,0,76, "Whatever the main action of this Turning Point, it is focused on a friend or someone close to a Character. This friend can be an already existing Character in the Adventure or someone not on the Characters List. Whoever the friend is attached to, that is the Character Invoked, not the friend." ],
	[ "UNTOUCHABLE", 0,0,0,0,77, "A Character is, in some manner, untouchable by others in this Turning Point. For instance, a villain who is a world leader and thus can’t be directly attacked without triggering an international incident, or a superhero who is nearly impervious to harm. The untouchableness should serve a plot purpose, so that Characters are forced to take other actions to advance the Plotline." ],
	[ "BRIBE", 0,0,0,0,78, "A Character is offered a bribe by another Character to do something that is not legitimate. For instance, a villain may offer money to a Character if they walk away from a murder scene." ],
	[ "DEALING WITH A CALAMITY", 75,0,0,0,0, "This Turning Point involves a Character having to “fight” a calamity of some kind. For instance, maybe the Character is battling a fire to put it out, or he must fight his way through an ancient stone temple as it collapses around him." ],
	[ "SUDDEN CESSATION", 77,0,0,0,0, "Whatever is happening in this Turning Point, it will suddenly cease. This could occur at any time and the causes may be unknown. For instance, if Characters are attacked by a group, the group may suddenly break off and run away." ],
	[ "IT’S A TRAP!", 0,79,0,0,0, "This Turning Point involves a trap of some kind. This can be a physical trap, such as adventurers falling prey to a pit in a hallway, to other kinds of traps, such as the summons to the peace negotiation was really just a ruse to get the leader in sights for an assassination." ],
	[ "A MEETING OF MINDS", 0,0,0,75,0, "This Turning Point involves two Characters coming together for a discussion of importance." ],
	[ "TIME LIMIT", 0,81,0,0,0, "A task must be accomplished within a certain amount of time or a Character will suffer consequences. The time limit does not need to expire within this Turning Point and could extend beyond it further into the Adventure, but it should terminate within this Adventure to give the Characters a reason to accomplish the task. Failure to accomplish the task should be significant. For instance, if a cure to a toxin isn’t found within a day, the prince will die." ],
	[ "THE HIDDEN HAND", 0,0,80,0,0, "Whatever is happening in this Turning Point it is clear that it was caused on purpose by someone of unknown identity. For instance, if a Character is ambushed by bandits, the bandit leader may make a mysterious reference to their “benefactor” having paid for the attack. Or, an engine failure on a ship may be found to have been caused by obvious tampering." ],
	[ "A NEEDED RESOURCE IS RUNNING SHORT", 0,83,0,0,0, "A resource a Character needs is running low and will need to be replenished. This causes problems for the Character. For instance, a starship’s warp engine functions on crystals that are running out." ],
	[ "ORGANIZATIONS IN CONFLICT", 0,0,0,76,0, "This Turning Point involves two or more organizations that are at odds with each other. For instance, two rival mafia organizations may be trying to capture a master counterfeiter to use for their own purposes." ],
	[ "BAD NEWS", 0,85,0,0,0, "Something negative that happens in this Turning Point doesn’t happen directly in the Turning Point but is delivered in the form of information. The event happened remotely, and a Character is learning of it. For instance, Characters may learn their allies lost a crucial battle elsewhere." ],
	[ "CHARACTER ASSISTANCE", 0,0,0,0,80, "A Character assists another Character in some way. This assistance can be anything from coming to their aid in battle to giving them a shoulder to cry on." ],
	[ "ASKING FOR HELP", 0,0,0,0,82, "A Character approaches another Character to ask for help." ],
	[ "HUNKER DOWN", 0,86,0,0,0, "This Turning Point involves a Character needing to fortify a place of refuge. For instance, a baron must shore up his castle defenses against an impending attack, or a generator must be fueled up to increase a force field’s power before a meteor storm rains down on the planet surface." ],
	[ "ABANDONED", 0,88,0,0,0, "Something needs to be abandoned or has been abandoned already in this Turning Point. For instance, a heavily damaged starship is going to explode in two hours and must be evacuated. Or, a Character comes upon an empty village in a forest." ],
	[ "FIND IT OR ELSE", 0,0,82,0,0, "Something needs to be found in this Turning Point to help resolve the Plotline. The act of finding the thing could take place in this Turning Point, or a Character learns of the need to find something. The thing to be found can be just about anything, from an object such as a magic ring to open a portal, to a special person like the lone witness to a crime that proves an accused person is innocent." ],
	[ "USED AGAINST THEM", 78,89,0,0,0, "A resource owned or aligned with one Character is somehow turned against them in this Turning Point. For instance, a small starship is being pursued by three massive battle cruisers. By skillful piloting, the smaller ship causes the larger ships to collide with each other, using their size against them. Or, a wizard may command a powerful golem, but another wizard casts a spell to make the golem attack its master." ],
	[ "POWERFUL PERSON", 0,0,0,77,0, "This Turning Point involves a powerful person. The Character’s power can be of any nature, from a physically powerful warrior to a government figure with a lot of influence. Invoke a Character. If the Character is powerful, then that is the powerful person. If they are not, then the powerful person is someone associated with that Character in some way." ],
	[ "CREEPY TONE", 0,91,0,0,0, "This Turning Point involves a creepy tone, such as a dark and forbidding place or a Character who is extremely menacing in a disturbing way." ],
	[ "WELCOME TO THE PLOT", 0,0,0,0,83, "A Character learns that they are connected to this Plotline somehow in a personal way. Maybe it involves something from their past or someone in their life. For instance, a detective may discover that the crime syndicate he is trying to take down is run by his long lost brother." ],
	[ "TRAVEL SETTING", 79,92,83,78,0, "This Turning Point takes place in a traveling vehicle. For instance, a ship at sea, a train, a ship hurtling through space, etc." ],
	[ "ESCORT DUTY", 0,0,0,79,0, "A Character must escort another Character somewhere. For instance, this could be a bodyguard transporting a high powered executive to a remote location, or a band of warriors trying to get a princess through a valley full of monsters." ],
	[ "AN OLD DEAL", 0,0,84,80,0, "This Turning Point involves an agreement made long ago, probably even before this Adventure began. For instance, occult investigators researching a mysterious death discover that the deceased person sold his soul to a demon ten years ago, and they suspect the death is the demon having come to collect." ],
	[ "A NEW ENEMY", 0,93,0,0,0, "This Turning Point presents a new threat to a Character. It is a threat that may or may not be directly related to any Plotlines but must be dealt with all the same. For instance, explorers deep under the earth are moving through an ancient ruin to find their lost comrade when they are beset upon by dinosaurs who nest in the area. This results automatically in a New Character." ],
	[ "ALLIANCE", 0,0,0,82,0, "One group offers to ally with another. This may be a surprise alliance, such as an enemy wanting to join with another enemy to take on a common foe, or it could be something less dramatic, such as the FBI offering to assist local law enforcement in solving a crime. The “groups” in question can be formal organizations or something looser, such as groups of individuals." ],
	[ "POWER OVER OTHERS", 0,0,0,84,0, "A Character has power over other Characters in some way, shape, or form in this Turning Point. This power puts the Character in a commanding position in regards to the others. For instance, the lord of a land demands all the peasants pay high taxes or else his men will oppress them. Or, the producer of an anti-toxin for a disease that an entire village has demands they give him whatever he wants in order to receive the medicine." ],
	[ "A MYSTERIOUS NEW PERSON", 0,0,85,0,0, "This Turning Point automatically Invokes a New Character, added to the List, whose identity or purpose is not fully known. Maybe they are a shadowy visitor at a meeting, or someone who seems to have authority over someone else." ],
	[ "FRENETIC ACTIVITY", 81,0,0,0,0, "This Turning Point involves action coming fast and furious at a Character. It should be a rapid fire succession of action, for instance a series of attackers, an out of control boat rocketing down a rapids approaching peril after peril, running a gauntlet of some kind through a series of traps, etc." ],
	[ "RURAL SETTING", 0,94,86,85,0, "This Turning Point involves a rural setting, such as out in the country or at a farm." ],
	[ "LIKEABLE", 0,0,0,0,84, "This Turning Point involves a Character who is very likable to another Character. Whoever it is, it should be someone who generates sympathy. The Character’s likability should be strong enough to motivate the other Character’s actions. For instance, a jaded cop thought he has seen it all, but a kidnapped girl kindles in him a desire to save her and redeem himself." ],
	[ "SOMEONE IS WHERE THEY SHOULD NOT BE", 0,0,88,0,0, "A Character is at a location where they should not normally be. For instance, an ally is seen at the headquarters of an enemy, a wealthy socialite is found meeting with a mafia boss at a restaurant, etc." ],
	[ "SNEAKY BARRIER", 83,0,0,0,0, "A barrier needs to be overcome through stealth or dexterity. For instance, a monster lives in a cave that is only accessible by climbing a high, treacherous cliffside. Or, there are too many ninjas guarding the villain to fight your way through, but you can slip past them unseen if you are skilled enough." ],
	[ "CORRUPTION", 0,0,0,87,0, "This Turning Point involves corruption of a social apparatus of some kind. For instance, a police officer on the take from the mob, or the villain of the Adventure turns out to be a local bureaucrat using his position to give smugglers access to a dock at night." ],
	[ "VULNERABILITY EXPLOITED", 0,95,89,0,0, "This Turning Point involves a vulnerability of some kind being exploited by a Character. For instance, someone knowing of another’s crime and blackmailing them, Characters learning of a starbase’s secret vulnerability that allows it to be destroyed, etc. This Turning Point can either involve learning about the vulnerability or actively exploiting it." ],
	[ "THE PROMISE OF REWARD", 0,0,0,0,86, "This Turning Point involves a Character faced with a substantial reward for their participation. For instance, maybe a village is willing to give a group of adventurers everything they have if they fight off a band of marauding goblins. The reward should be for doing something that is considered legitimate or good." ],
	[ "FRAUD", 0,0,91,0,0, "A Character is a fraud. Whatever it is they are presenting themselves as, or whatever story they have told of themselves, is false. This result differs from Hidden Agenda, where in Hidden Agenda the Character may legitimately have both motives in mind, whereas in Fraud the image or story they are presenting is completely fake. For instance, the prince claiming he is the rightful ruler of a kingdom is actually a shapeshifting doppelgänger assuming the role." ],
	[ "IT’S BUSINESS", 0,0,0,89,0, "This Turning Point involves business or commerce in some way. It can either be a business transaction, or a business is involved in the Turning Point. For instance, a corporation hires a super hero to protect an important shipment, or a book of antiquity containing a needed spell has to be purchased from an auction house." ],
	[ "JUST CAUSE GONE AWRY", 0,0,0,90,0, "This Turning Point involves something that began as a just cause but has spiraled into something unjust. For instance, a hero takes down a group of orcs terrorizing a town, saving the people, but now the hero has installed himself as the overlord of the town and is demanding tribute." ],
	[ "EXPERT KNOWLEDGE", 0,0,0,0,87, "This Turning Point involves a Character who has very specific and specialized knowledge or skills that come into play during the Turning Point. For instance, only the genius of Dr. Rayder can figure out the intricacies of the alien device, or it’s discovered that a killer is murdering people with his knowledge of exotic poisons." ],
	[ "A MOMENT OF PEACE", 85,0,0,0,0, "Whatever else is going on in this Turning Point, it should overall be a peaceful time for a Character. For instance, there is a lull in the war where the combatants have a chance to enjoy a drink together and relax before they must fight again." ],
	[ "A FOCUS ON THE MUNDANE", 0,0,0,0,89, "This Turning Point involves a focus on something mundane and ordinary, such as a person’s living room or a meal. This mundane thing may be coupled with something extraordinary in the Turning Point. For instance, a Character is killed when his nightly dinner is poisoned, or a family portrait is found to be a cursed item." ],
	[ "RUN AWAY!", 0,0,0,0,91, "A Character flees or has fled. The actual flight may occur in this Turning Point or it may be learned of. For instance, a Character runs screaming as a horrible monster appears on the scene, or, a Character who disappeared earlier in the Adventure is learned to have left town fearing for his life." ],
	[ "BEAT YOU TO IT", 87,0,93,0,0, "Whatever is happening in this Turning Point that involves arriving at a location for some purpose, a Character discovers that someone else has arrived before them. For instance, a Character goes to the morgue to check out a clue and learns that another investigator already showed up and took the body." ],
	[ "CONFRONTATION", 89,0,0,91,0, "This Turning Point involves Characters meeting in a confrontation that may turn physical if things don’t go well. For instance, a Character meets the leader of a street gang to get information, but the gang is notoriously twitchy and violent." ],
	[ "ARGUMENT", 0,0,0,93,0, "A disagreement between two Characters leads to a conflict in this Turning Point." ],
	[ "SOCIAL TENSION SET TO BOILING", 0,0,0,94,0, "An element of extreme social tension is near the breaking point. This Turning Point involves some aspect of that, such as an event that increases the tension or an event that is a result of the tension. For instance, two nations at the brink of war have a border skirmish as pressure rises among soldiers." ],
	[ "PROTECTOR", 91,0,0,0,93, "A Character must protect someone or something in this Turning Point. If this is an Action Plot Point, the Character must actively protect in this Turning Point from a threat. If it is a Personal Plot Point, then the Character receives the protection duty in this Turning Point." ],
	[ "CRESCENDO", 93,0,0,0,0, "A series of events that has taken place in this Adventure culminates in this Turning Point. If this is early in the Adventure or in this Plotline, then instead the Adventure or Plotline gets off to a fiery start. For instance, Characters following clues to track a cult finally discover their lair, resulting in a mass battle. Or, a Plotline about retrieving a stolen gem begins with a very elaborate theft" ],
	[ "DESTROY THE THING", 95,0,0,0,0, "A Character must destroy or try to destroy something in this Turning Point. Maybe a party of dungeon delvers reaches the heart of the cavern where they must break a mystic seal." ],
	[ "CONSPIRACY THEORY", 0,0,94,0,0, "A Character believes in a scenario that explains a problem in this Adventure. The Character may be right or wrong, but the theory may cause action on the part of the Character. For instance, a group is holed up in a mall during a zombie apocalypse. One Character believes it’s just a disease, so they encourage the others not to shoot the zombies." ],
	[ "SERVANT", 0,0,0,95,95, "This Turning Point involves a servant or proxy of another Character. Invoke a Character for the servant to represent." ],
	[ "AN OPPOSING STORY", 0,0,95,0,0, "A Character learns of an alternate version of something they already know about from this Adventure. For instance, while investigating a starship that had been waylaid by aliens, Characters discover a crewmember who claims the attackers were members of a rival guild and not aliens." ],
	[ "META", 100,100,100,100,100, "This is a special Plot Point category with Plot Points that change the Characters List or combine Plotlines. Go to the Meta Plot Points Table and roll 1d100 on it for your Plot Point.", 2 ]
]);
```
__
Sets up this shortcut-file
