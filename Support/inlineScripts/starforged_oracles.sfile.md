---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged Oracles.

__

__
```js
function getVar(notePath, fmVar) {
    // Remove any quotes around the note path parameter
    notePath = notePath.replaceAll(/^\"|\"$/g, "");

    // If notename is ".", change it to the current file
    if (notePath === ".") { notePath = app.workspace.getActiveFile()?.path; }

    // Get the file object for the specified note.  Early if not available or is a folder
    const file = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
    if (!file)
    {
        return expFormat([ "", "No value.  Note __" + notePath + "__ not found." ]);
    }
    if (file.children)
    {
        return expFormat(
            [ "", "No value.  __" + notePath + "__ is a folder." ]);
    }

    // Get the file's cached data.  Early out if not available.
    const cache = app.metadataCache.getFileCache(file);
    if (!cache)
    {
        return expFormat(
            [ "", "No value.  Note __" + notePath + "__ is not properly cached by Obsidian." ]);
    }

    // Get the front-matter object
    // If no front-matter, early out (no message, since it technically worked, but the
    // variable is empty)
    const fm = cache.frontmatter;
    if (!fm)
    {
        return null;
    }

    // Get the variable valuable
    const result = fm[fmVar] || fm[fmVar + ":"] || null;

    // Return the result.  If it's an array, it's joined to a single string.
    return Array.isArray(result) ? result.join(",") : result;
}
```
__

__
^getOracle ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
__
```js
$1 = $1.replaceAll(/^\"|\"$/g, "");
let iResult = expand("tbl roll " + $1);
let capResult = iResult[0].toUpperCase();
var trueResult = "";
var result1 = "";
var result2 = "";
if (capResult.contains("ROLL TWICE")) {
    result1 = expand("tbl roll " + oracle);
    result2 = expand("tbl roll " + oracle);
    trueResult = "Roll Twice - " + result1 + ", " + result2;
} else if (capResult.contains("ACTION") && capResult.contains("THEME")) {
    result1 = expand("tbl roll Oracles/Core/Core_Action.md");
    result2 = expand("tbl roll Oracles/Core/Core_Theme.md");
    trueResult = "Action/Theme - " + result1 + " " + result2;
} else if (capResult.contains("DESCRIPTOR") && iResult[0].contains("FOCUS")) {
    result1 = expand("tbl roll Oracles/Core/Core_Descriptor.md");
    result2 = expand("tbl roll Oracles/Core/Core_Focus.md");
    trueResult = "Descriptor/Focus - " + result1 + " " + result2;
} else {
    trueResult = iResult[0];
}
return trueResult;
```
__
getOracle {path} - get oracle result but check for Roll Twice, Action/Theme, and Descriptor/Focus

__
^oracle (.+)$
__
```js
var result1 = "";
var result2 = "";
var addDetails = "";
var extra1 = "";
var extra2 = "";
var question = $1;
if ($1.toUpperCase() == "AT") {
    result1 = expand("tbl roll Oracles/Core/Core_Action.md");
    result2 = " " + expand("tbl roll Oracles/Core/Core_Theme.md");
    question = "Action/Theme";
} else if ($1.toUpperCase() == "DF") {
    result1 = expand("tbl roll Oracles/Core/Core_Descriptor.md");
    result2 = " " + expand("tbl roll Oracles/Core/Core_Focus.md");
    question = "Descriptor/Focus";
} else {
    result1 = expand("tbl roll");
}
if (result1[0].includes("Descriptor") && result1[0].includes("Focus")) {
    extra1 = expand("tbl roll Oracles/Core/Core_Descriptor.md");
    extra2 = expand("tbl roll Oracles/Core/Core_Focus.md");
    addDetails = "Descriptor/Focus: " + extra1 + " " + extra2 + "\n\n";
} else if (result1[0].includes("Action") && result1[0].includes("Theme")) {
    extra1 = expand("tbl roll Oracles/Core/Core_Action.md");
    extra2 = expand("tbl roll Oracles/Core/Core_Theme.md");
    addDetails = "Action/Theme: " + extra1 + " " + extra2 + "\n\n";
} else {
    addDetails = "\n\n"
}
let calloutTitle = "> [!oracle]- " + question + " Result: " + result1 + result2 + " " + extra1 + " " + extra2;
let outcome = "\n> **Additional Details:** ";
let callout = calloutTitle + outcome + addDetails;
return callout;
```
__
oracle {the question you are asking} - Type in the oracles initials to roll.

__
^yesno ([a-zA-Z]*) (.+)$
__
```js
let theRoll = Math.floor(Math.random() * 100 + 1);
var yesCeiling = 50;
var chances = "Fifty-Fifty";
var randD6 = Math.floor(Math.random() * 6 + 1);
switch ($1.toUpperCase()) {
    case "AC":
        yesCeiling = 90;
        chances = "Almost Certain";
        break;
    case "L":
        yesCeiling = 75;
        chances = "Likely";
        break;
    case "FF":
        yesCeiling = 50;
        chances = "Fifty-Fifty";
    case "U":
        yesCeiling = 25;
        chances = "Unlikely";
        break;
    case "SC":
        yesCeiling = 10;
        chances = "Small Chance";
        break;
    default:
        yesCeiling = 50;
        chances = "Fifty-Fifty";
}
var result = theRoll.toString();
if (theRoll <= yesCeiling) {
    result += ", Yes";
} else {
    result += ", No";
}
if (theRoll == 100 || theRoll % 11 == 0) {
    result += " with a MATCH!";
} else if (theRoll <= yesCeiling && randD6 == 1) {
    result += " but";
} else if (theRoll > yesCeiling && randD6 == 6) {
    result += " but";
}
let calloutTitle = "> [!oracle]- " + $2 + " (" + chances + ") Result: " + result;
let outcome = "\n> **Additional Details:** ";
callout = calloutTitle + outcome + "\n\n";
return callout;
```
__
yesno {chances initials} {question to ask} - AC for Almost Certain, L for Likely, FF for Fifty-Fifty, U for Unlikely, SC for Small Chance.

__
^at (.+)$
__
```js
let action = expand("tbl roll Oracles/Core/Core_Action.md");
let theme = expand("tbl roll Oracles/Core/Core_Theme.md");
let calloutTitle = "> [!oracle]- " + $1 + " Action/Theme: " + action + " " + theme;
let outcome = "\n> **Additional Details:** \n\n";
return calloutTitle + outcome;
```
__
at {question you need action/theme for} - Use this function to ask a question seeking more details with the Action/Theme oracles.

__
^df (.+)$
__
```js
let descriptor = expand("tbl roll Oracles/Core/Core_Descriptor.md");
let focus = expand("tbl roll Oracles/Core/Core_Focus.md");
let calloutTitle = "> [!oracle]- " + $1 + " Descriptor/Focus: " + descriptor + " " + focus;
let outcome = "\n> **Additional Details:** \n\n";
return calloutTitle + outcome;
```
__
df {question you need descriptor/focus for} - Use this function to ask a question seeking more details with the Descriptor/Focus oracles.

__
^character
__
```js
let given = expand("tbl roll Oracles/Characters/Characters_Given_Name.md");
let family = expand("tbl roll Oracles/Characters/Characters_Family_Name.md");
let callsign = expand("tbl roll Oracles/Characters/Characters_Callsign.md");
let name = given + " \"" + callsign + "\" " + family;
let fl = "**First Look:** " + expand("getOracle Oracles/Characters/Characters_First_Look.md");
let goal = "**Goal:** " + expand("getOracle Oracles/Characters/Characters_Goal.md");
let iniDis = "**Initial Disposition:** " + expand("getOracle Oracles/Characters/Characters_Initial_Disposition.md");
let revAsp = "**Revealed Aspect:** " + expand("getOracle Oracles/Characters/Characters_Revealed_Aspect.md");
let role = "**Role:** " + expand("getOracle Oracles/Characters/Characters_Role.md");
let callout = "> [!oracle]- NPC: " + name;
return callout + "\n> " + fl + "\n> " + iniDis + "\n> " + revAsp + "\n> " + role + "\n> " + goal + "\n\n";
```
__
character - gets a random NPC

__
^settlement ?([a-zA-Z]*)$
__
```js
var area = "";
if (!$1) {
    area = "Terminus";
} else {
    area = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
}
let name = expand("tbl roll Oracles/Settlements/Settlements_Name.md");
let location = "**Location:** " + expand("tbl roll Oracles/Settlements/Settlements_Location.md");
let fl = "**First Look:** " + expand("tbl roll Oracles/Settlements/Settlements_First_Look.md");
let iniCon = "**Initial Contact:** " + expand("tbl roll Oracles/Settlements/Settlements_Initial_Contact.md");
let authority = "**Authority:** " + expand("tbl roll Oracles/Settlements/Settlements_Authority.md");
let project = "**Project:** " + expand("tbl roll Oracles/Settlements/Settlements_Projects.md");
let trouble = "**Trouble:** " + expand("tbl roll Oracles/Settlements/Settlements_Trouble.md");
let pop = "**Population:** " + expand("tbl roll Oracles/Settlements/Settlements_Population_" + area + ".md");
let callout = "> [!oracle]- Settlement: " + name;
return callout + "\n> " + location + "\n> " + pop + "\n> " + iniCon + "\n> " + authority + "\n> " + fl + "\n> " + trouble + "\n> " + project + "\n\n";
```
__
settlement {optional region: terminus, expanse, outlands} - gets a random settlement in {region} with a default region of Terminus if you don't type in a region

__
^faction
__
```js
let type = "**Type:** " + expand("tbl roll Oracles/Factions/Factions_Type.md");
var specificType = "";
if (type.includes("Dominion")) {
    specificType = "**Dominion:** " + expand("tbl roll Oracles/Factions/Factions_Dominion.md");
} else if (type.includes("Guild")) {
    specificType = "**Guild:** " + expand("getOracle Oracles/Factions/Factions_Guild.md");
} else if (type.includes("Fringe")) {
    specificType = "**Fringe Group:** " + expand("getOracle Oracles/Factions/Factions_Fringe_Group.md");
} else if (type.includes("Corporation")) {
    specificType = "**Corporation Field:** " + expand("getOracle Oracles/Factions/Factions_Corporation_Field.md");
} else if (type.includes("Military")) {
    specificType = "**Military Specialty:** " + expand("getOracle Oracles/Factions/Factions_Military_Specialty.md");
} else if (type.includes("Religious")) {
    specificType = "**Religious Group Role:** " + expand("getOracle Oracles/Factions/Factions_Religious_Role.md");
} else if (type.includes("Research")) {
    specificType = "**Research Field of Study:** " + expand("getOracle Oracles/Factions/Factions_Research_Field_of_Study.md");
} else if (type.includes("Data")) {
    specificType = "**Data Harvesters Role:** " + expand("getOracle Oracles/Factions/Factions_Data_Harvesters_Role.md");
} else if (type.includes("AI Hive")) {
    specificType = "**AI Hive Prime Directive:** " + expand("getOracle Oracles/Factions/Factions_AI_Hive_Prime_Directive.md");
} else {
    specificType = "**Dominion:** " + expand("tbl roll Oracles/Factions/Factions_Dominion.md");
}
let influence = "**Influence:** " + expand("tbl roll Oracles/Factions/Factions_Influence.md");
let leadership = "**Leadership:** " + expand("tbl roll Oracles/Factions/Factions_Leadership.md");
let project = "**Project:** " + expand("getOracle Oracles/Factions/Factions_Projects.md");
let quirk = "**Quirk:** " + expand("getOracle Oracles/Factions/Factions_Quirks.md");
let rumor = "**Rumor:** " + expand("getOracle Oracles/Factions/Factions_Rumors.md");
let affil = expand("tbl roll Oracles/Factions/Factions_Name_Affiliation.md");
let legacy = expand("tbl roll Oracles/Factions/Factions_Name_Legacy.md");
let identity = expand("tbl roll Oracles/Factions/Factions_Name_Identity.md");
let template = expand("tbl roll Oracles/Factions/Factions_Name_Template.md");
let last6 = template[0].slice(-6);
let name = "";
if (template[0].contains(" of the")) {
    if (last6.contains("y")) {
        name = affil + " of the " + legacy + " " + identity;
    } else {
        name = identity + " of the " + legacy + " " + affil;
    }
} else {
    if (last6.contains("y")) {
        name = legacy + " " + identity;
    } else {
        name = legacy + " " + affil;
    }   
}
let callout = "> [!oracle]- Faction: " + name;
return callout + "\n> " + type + "\n> " + specificType + "\n> " + influence + "\n> " + leadership + "\n> " + quirk + "\n> " + project + "\n> " + rumor + "\n\n";
```
__
faction - gets a random faction

__
^starship ?([a-zA-Z]*)$
__
```js
var area = "";
if (!$1) {
    area = "Terminus";
} else {
    area = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
}
let name = expand("tbl roll Oracles/Starships/Starships_Name.md");
let fl = "**First Look:** " + expand("getOracle Oracles/Starships/Starships_First_Look.md");
let iniCon = "**Initial Contact:** " + expand("tbl roll Oracles/Starships/Starships_Initial_Contact.md");
let type = "**Class:** " + expand("tbl roll Oracles/Starships/Starships_Type.md");
let mission = "**Mission:** " + expand("getOracle Oracles/Starships/Starships_Mission_" + area + ".md");
let callout = "> [!oracle]- Starship: " + name;
return callout + "\n> " + type + "\n> " + iniCon + "\n> " + fl + "\n> " + mission + "\n\n";
```
__
starship {optional region: terminus, expanse, outlands} - gets a random starship from {region} with a default of Terminus if you don't type in a region

__
^creature ?([a-zA-Z]*)$
__
```js
let input = $1;
var environment = "Air";
if (input == null) {
    environment = expand("tbl roll Oracles/Creatures/Creatures_Environment.md");
} else {
    environment = input.charAt(0).toUpperCase() + input.slice(1).toLowerCase();
}
let form = "Form: " + expand("getOracle Oracles/Creatures/Creatures_Basic_Form_" + environment + ".md");
let trueEnv = "**Environment:** " + environment;
let scale = expand("tbl roll Oracles/Creatures/Creatures_Scale.md");
var trueScale = "";
if (scale.contains("scale")) {
    trueScale = "**Scale:** " + expand("tbl roll Oracles/Creatures/Creatures_Ultra_Scale.md");
} else {
    trueScale = "**Scale:** " + scale[0];
}
let fl = "**First Look:** " + expand("tbl roll Oracles/Creatures/Creatures_First_Look.md");
let ra = "**Revealed Aspect:** " + expand("tbl roll Oracles/Creatures/Creatures_Revealed_Aspect.md");
let eb = "**Encountered Behavior:** " + expand("getOracle Oracles/Creatures/Creatures_Encountered_Behavior.md");
let callout = "> [!oracle]- Creature: " + form + " (" + environment + ")";
return callout + "\n> " + trueEnv + "\n> " + trueScale + "\n> " + fl + "\n> " + ra + "\n> " + eb + "\n\n";
```
__
creature {environment} - gets a random creature in environment (air, interior, land, liquid, space, or leave blank for a random environment)

__
^derelict
__
```js
let type = expand("tbl roll Oracles/Derelicts/Derelicts_Location_and_Type.md");
let fl = "**Outer First Look:** " + expand("tbl roll Oracles/Derelicts/Derelicts_Outer_First_Look.md");
let con = "**Condition:** " + expand("tbl roll Oracles/Derelicts/Derelicts_Condition.md");
var zone = "";
if (type[0].contains("Starship")) {
    zone = "**Starting Zone:** " + expand("tbl roll Oracles/Derelicts/Derelicts_Zones_Starship.md");
} else {
    zone = "**Starting Zone:** " + expand("tbl roll Oracles/Derelicts/Derelicts_Zones_Settlement.md");
}
let callout = "> [!oracle]- Derelict: " + type;
return callout + "\n> " + fl + "\n> " + con + "\n> " + zone + "\n\n";
```
__
derelict - gets a random derelict

__
^vault
__
```js
let fl = "**Outer First Look:** " + expand("tbl roll Oracles/Vaults/Vaults_Outer_First_Look.md");
let form = "**Form:** " + expand("tbl roll Oracles/Vaults/Vaults_Form.md");
let location = "**Location:** " + expand("tbl roll Oracles/Vaults/Vaults_Location.md");
let scale = "**Scale:** " + expand("tbl roll Oracles/Vaults/Vaults_Scale.md");
if (scale.includes("Ultra")) {
    scale = "**Scale:** " + expand("tbl roll Oracles/Vaults/Vaults_Ultra_Scale.md");
}
let material = "**Material:** " + expand("getOracle Oracles/Vaults/Vaults_Material.md");
let shape = "**Shape:** " + expand("getOracle Oracles/Vaults/Vaults_Shape.md");
let callout = "> [!oracle]- Precursor Vault";
return callout + "\n> " + scale + "\n> " + location + "\n> " + form + "\n> " + material + "\n> " + shape + "\n> " + fl + "\n\n";
```
__
vault - gets a random vault

__
^planet ([a-zA-Z]*) ?([a-zA-Z]*)$
__
```js
let pClass = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
var givenReg = $2;
var region = "Terminus";
if (givenReg == null) {
	region = "Terminus";
} else {
	region = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
}
var trueClass = "";
if (pClass == "Random") {
    let rolledClass = expand("tbl roll Oracles/Planets/Planets_Class.md");
    if (rolledClass[0].contains("Desert")) { trueClass = "Desert"; }
    if (rolledClass[0].contains("Furnace")) { trueClass = "Furnace"; }
    if (rolledClass[0].contains("Grave")) { trueClass = "Grave"; }
    if (rolledClass[0].contains("Ice")) { trueClass = "Ice"; }
    if (rolledClass[0].contains("Jovian")) { trueClass = "Jovian"; }
    if (rolledClass[0].contains("Jungle")) { trueClass = "Jungle"; }
    if (rolledClass[0].contains("Ocean")) { trueClass = "Ocean"; }
    if (rolledClass[0].contains("Rocky")) { trueClass = "Rocky"; }
    if (rolledClass[0].contains("Shattered")) { trueClass = "Shattered"; }
    if (rolledClass[0].contains("Tainted")) { trueClass = "Tainted"; }
    if (rolledClass[0].contains("Vital")) { trueClass = "Vital"; }
} else {
    trueClass = pClass;
}
let atmo = "**Atmosphere:** " + expand("tbl roll Oracles/Planets/" + trueClass + "/Planets_" + trueClass + "_Atmosphere.md");
let feat = "**Feature:** " + expand("getOracle Oracles/Planets/" + trueClass + "/Planets_" + trueClass + "_Feature.md");
let life = "**Life:** " + expand("tbl roll Oracles/Planets/" + trueClass + "/Planets_" + trueClass + "_Life.md");
let obsv = "**Observed from Space:** " + expand("getOracle Oracles/Planets/" + trueClass + "/Planets_" + trueClass + "_Observed_from_Space.md");
let sett = "**Settlements:** " + expand("tbl roll Oracles/Planets/" + trueClass + "/Planets_" + trueClass + "_Settlements_" + region + ".md");
let name = "**Planet:** " + expand("tbl roll Oracles/Planets/" + trueClass + "/Planets_" + trueClass + "_Name.md");
let area = "**Region:** " + region;
let callout = "> [!oracle]- " + name + ", Class: " + trueClass;
let nextStep = "\n>" + area + "\n> " + obsv + "\n> " + atmo + "\n> " + feat + "\n> " + life;
var finalAdd = "";
if (trueClass == "Vital") {
    let diversity = expand("tbl roll Oracles/Planets/Vital/Planets_Vital_Diversity.md");
    var numBio = 2;
    if (diversity[0].contains("Diverse")) { numbBio = 3;}
    if (diversity[0].contains("Complex")) { numbBio = 4;}
    if (diversity[0].contains("Garden")) { numbBio = 5;}
    var biomes = "**Biomes:** ";
    for (let i = 0; i < numbBio; i = i + 1) {
        if (i == 0) {
            biomes = biomes + expand("tbl roll Oracles/Planets/Vital/Planets_Vital_Biomes.md");
        } else {
            biomes = biomes + ", " + expand("tbl roll Oracles/Planets/Vital/Planets_Vital_Biomes.md");
        }
    }
    finalAdd = "\n> **Diversity:** " + diversity + "\n> " + biomes;
}
return callout + nextStep + finalAdd + "\n\n";
```
__
planet {class} {region: terminus, expanse, outlands} - gets a random planet of {class: desert, furnace, grave, ice, jovian, jungle, ocean, rocky, shattered, tainted, vital or RANDOM} in {region: terminus, expanse, outlands}

__
^sector ?([a-zA-Z]*)$
__
```js
var area = "";
var settlements = "";
var settlementInfo = "";
if (!$1) {
    let randD10 = Math.floor(Math.random() * 10 + 1);
    if (randD10 < 7) {
        area = "Terminus";
    } else if (randD10 < 10) {
        area = "Outlands";
    } else {
        area = "Expanse";
    }
} else {
    area = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
}
switch (area) {
    case "Terminus":
        settlements = "**Settlements:** 4";
        settlementInfo = expand("settlement terminus") + expand("settlement terminus") + expand("settlement terminus") + expand("settlement terminus");
    case "Outlands":
        settlements = "**Settlements:** 3";
        settlementInfo = expand("settlement outlands") + expand("settlement outlands") + expand("settlement outlands");
    default:
        settlements = "**Settlements:** 2";
        settlementInfo = expand("settlement expanse") + expand("settlement expanse");
}
let numbPlan = Math.floor(Math.random() * 5);
let numberOfPlanets = "**Number of Planets:** " + numbPlan.toString();
var planetInfo = "";
for (let i = 0; i < numbPlan; i = i + 1) {
    planetInfo += expand("planet random " + area);
}
let sighting = "**Sighting:** " + expand("getOracle Oracles/Space/Space_Sighting_" + area + ".md") + " (Roll for the sighting separately)";
let stellar = "**Stellar Object:** " + expand("getOracle Oracles/Space/Space_Stellar_Object.md");
let region = "**Region:** " + area;
let prefix = expand("tbl roll Oracles/Space/Space_Sector_Name_Prefix.md");
let suffix = expand("tbl roll Oracles/Space/Space_Sector_Name_Suffix.md");
let name = "**Name:** The " + prefix + " " + suffix;
let trouble = "**Trouble**: " + expand("getOracle Oracles/Campaign/Campaign_Sector_Trouble.md");
return "> [!oracle]- " + name + "\n> " + region + "\n> " + stellar + "\n> " + numberOfPlanets + "\n> " + settlements + "\n> " + sighting + "\n> " + trouble + "\n\n" + planetInfo + settlementInfo;
```
__
sector {optional region: terminus, expanse, outlands} - Builds a random sector in {region}. If you don't provide a region, it randomly selects the region.

__
^ptp
__
```js
let result = expand("getOracle Oracles/Moves/Moves_Pay_the_Price.md");
let callout = "> [!oracle]- Pay the Price! **Result:** " + result + "\n> \n> **Additional Details:** \n\n";
return callout;
```
__
ptp - Rolls the oracle on the Pay the Price move.

__
^tbl multiroll (n|y) (.*)$
__
```js
// Turn the second parameter into an array of paths
const tables = $2.split(" ");

// Initialize an array for table roll results
let result = [];

// Roll once for each table
for (const table of tables)
{
	// Roll on the table
	const tableResult = expand("tbl roll " + table);

	// If an error is encountered then quit, returning the error
	if (!tableResult[0])
	{
		return tableResult;
	}

	// Add the table roll result to the array
	result.push(tableResult);
}

// Turn the array into a string of spaced items
result = result.join(" ");

// If the first parameter is "y" use expFormat()
return ($1 === "n") ? result : expFormat(result);
```
__
tbl multiroll {use expansion format: y OR n} {table files: space-separated path texts} - Rolls one instance of each table in {table files}. If {use expansion format} is "y", prints the expansion using the standard expansion format.
