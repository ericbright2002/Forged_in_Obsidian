---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged Stats, Meters, and Asset Tracks.

__

__
```js
function countImpacts(characterFile) {
    let impacts = ["Wounded", "Shaken", "Unprepared", "Harmed", "Traumatized", "Doomed", "Tormented", "Indebted"];
    const file = app.vault.fileMap["Characters/" + characterFile] || app.vault.fileMap["Characters/" + characterFile + ".md"];
    const cache = app.metadataCache.getFileCache(file);
    const fm = cache.frontmatter;
    var i = 0;
    var countedImpacts = impacts.length;
    var currentValue = "";
    for (i = 0; i < impacts.length; i = i + 1) {
        currentImage = fm[impacts[i]];
        if (currentImage.contains("⬡")) {
            countedImpacts = countedImpacts - 1;
        }
    }
    return countedImpacts;
}
```
__


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

__
```js
function getAssetFM(asset) {
    // Remove any quotes around the note path parameter
    asset = asset.replaceAll(/^\"|\"$/g, "");

    // Get the file object for the specified note.  Early if not available or is a folder
    notePath = "Assets/Command_Vehicle/" + asset;
    const file1 = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
    if (!file1) {
        notePath = "Assets/Companion/" + asset;
        const file2 = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
        if (!file2) {
            notePath = "Assets/Deed/" + asset;
            const file3 = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
            if (!file3) {
                notePath = "Assets/Module/" + asset;
                const file4 = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
                    if (!file4) {
                        notePath = "Assets/Path/" + asset;
                        const file5 = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
                        if (!file5) {
                            notePath = "Assets/Support_Vehicle/" + asset;
                            const file6 = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
                            if (!file6) {
                                notePath = "No path";
                            }
                        }
                    }
            }
        }
    }
    const file = app.vault.fileMap[notePath] || app.vault.fileMap[notePath + ".md"];
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
        return "Found no variable";
    }

    var finalName = "";
    // Get the variable valuable
    for ( var property in fm ) {
        if (property != "Max" && property != "position") {
            finalName = property;
        }
    }
    if (finalName == "") { finalName = "No such asset"; }

    // Return the result.  If it's an array, it's joined to a single string.
    return [notePath, finalName];
}
```
__

__

__
```js
function getSoloCharacter() {
    const file = app.vault.fileMap["Characters"];
    return file.children[0].name.replace(".md", "");
}
```
__

__
^impact ([a-zA-Z]*) ?([_a-zA-Z0-9]*)$
__
```js
var onOff = "";
var characterFile = "Character_File_Name_Here";
if (!$2) {
    characterFile = getSoloCharacter();
} else {
    characterFile = $2;
}

let impact = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
let currentValue = getVar("Characters/" + characterFile, impact);
let characterName = getVar("Characters/" + characterFile, "Name");
var newValue = "⬡";
if (currentValue.contains("⬡")) {
    newValue = "⬢";
    onOff = "on. ⬢";
} else {
    newValue = "⬡";
    onOff = "off. ⬡";
}
expand("notevars set Characters/" + characterFile + " " + impact + " " + newValue);
return "> [!mechanics]- " + characterName + " " + impact + " set to " + onOff + "\n\n";
```
__
impact {impact name} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this toggles a condition on/off using the full name of the condition except for "permanently harmed" which should be shortened to just "harmed".


__
^burnmom ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
if (!$1) {
    characterFile = getSoloCharacter();
} else {
    characterFile = $1;
}
let characterName = getVar("Characters/" + characterFile, "Name");
let countedImpacts = countImpacts(characterFile);
var momReset = 2;
var momMax = 10;
if (countedImpacts > 1) {
    expand("notevars set Characters/" + characterFile + " Momentum 0");
    momReset = 0;
    momMax = 10 - countedImpacts;
} else if (countedImpacts == 1) {
    expand("notevars set Characters/" + characterFile + " Momentum 1");
    momReset = 1;
    momMax = 9;
} else {
    expand("notevars set Characters/" + characterFile + " Momentum 2");
}

return "> [!mechanics]+ " + characterName + " burned their momentum!\n> This changed the result to: \n> \n> Momentum Max: " + momMax + "\n> Momentum Reset: " + momReset + "\n\n";
```
__
burnmom {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut burns your momentum and resets it.

__
^take ([0-9]*) ([_a-zA-Z]*) ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
if (!$3) {
    characterFile = getSoloCharacter();
} else {
    characterFile = $3;
}
let characterName = getVar("Characters/" + characterFile, "Name");

var name = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
var meter = "";
if (name.contains("_")) {
    let nameArray = name.split("_");
    for (let i = 0; i < nameArray.length; i = i + 1) {
        if (i < 1) {
            meter = nameArray[i].charAt(0).toUpperCase() + nameArray[i].slice(1).toLowerCase();
        } else {
            meter = meter + "_" + nameArray[i].charAt(0).toUpperCase() + nameArray[i].slice(1).toLowerCase();
        }
    }
} else {
    meter = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
}
if (meter == "Cv") { meter = "Starship"; }
let gain = Number($1);
var meterPath = "Characters/" + characterFile;
var max = 5;
let countedImpacts = countImpacts(characterFile);
switch (meter) {
    case "Health":
    case "Spirit":
    case "Supply":
    case "Wealth":
    case "Edge":
    case "Heart":
    case "Iron":
    case "Shadow":
    case "Will":
        break;
    case "Momentum":
        max = 10 - countedImpacts;
        break;
    default:
        let fm = getAssetFM(meter);
        meterPath = fm[0];
        max = getVar(meterPath, "Max");
        name = fm[1];
        break;
}

let current = getVar(meterPath, name);
if (!current) { current = 0; }
var newValue = Number(current) + gain;
if (newValue > Number(max)) {
    newValue = Number(max);
}
expand("notevars set " + meterPath + " " + name + " " + newValue);
let callout = "> [!mechanics]- " + characterName + "'s " + meter + " set to " + newValue + " out of " + max + ".\n\n";
return callout;
```
__
take {amount to add from 1-9} {meter, stat, or asset name} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut adds to Health, Spirit, Supply, Wealth, Momentum, Edge, Heart, Iron, Shadow, Will, or asset which has a meter.  If you are using an asset meter, use the name of the asset file including any underscore.

__
^suffer ([0-9]*) ([_a-zA-Z]*) ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
if (!$3) {
    characterFile = getSoloCharacter();
} else {
    characterFile = $3;
}
let characterName = getVar("Characters/" + characterFile, "Name");

var name = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
var meter = "";
if (name.contains("_")) {
    let nameArray = name.split("_");
    for (let i = 0; i < nameArray.length; i = i + 1) {
        if (i < 1) {
            meter = nameArray[i].charAt(0).toUpperCase() + nameArray[i].slice(1).toLowerCase();
        } else {
            meter = meter + "_" + nameArray[i].charAt(0).toUpperCase() + nameArray[i].slice(1).toLowerCase();
        }
    }
} else {
    meter = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
}
if (meter == "Cv") { meter = "Starship"; }
let loss = Number($1);
var meterPath = "Characters/" + characterFile;
var min = 0;
var max = 5;
let countedImpacts = countImpacts(characterFile);
switch (meter) {
    case "Health":
    case "Spirit":
    case "Supply":
    case "Wealth":
    case "Edge":
    case "Heart":
    case "Iron":
    case "Shadow":
    case "Will":
        break;
    case "Momentum":
        min = -6;
        max = 10 - countedImpacts;
        break;
    default:
        let fm = getAssetFM(meter);
        meterPath = fm[0];
        max = getVar(meterPath, "Max");
        name = fm[1];
        break;
}
let current = getVar(meterPath, name);
if (!current) { current = 0; }
var newValue = Number(current) - loss;
var text = "";
var leftover = min - newValue;
if (newValue < min) {
    newValue = min;
        text = " You had to lose more " + name + " than you had. " + leftover + " still to lose.";
}
expand("notevars set " + meterPath + " " + name + " " + newValue);
let callout = "> [!mechanics]+ " + characterName + "'s " + meter + " set to " + newValue + " out of " + max + ".\n> " + text + "\n\n";
return callout;
```
__
suffer {amount to subtract from 1-9} {meter, stat, or asset name} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut subtracts from Health, Spirit, Supply, Wealth, Momentum, Edge, Heart, Iron, Shadow, Will, or asset meter.  If you are using an asset meter, use the name of the asset file including any underscore.

__
^stats ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
if (!$1) {
	characterFile = getSoloCharacter();
} else {
    characterFile = $1;
}
var health = getVar("Characters/" + characterFile, "Health");
if (!health) { health = 0; }
var spirit = getVar("Characters/" + characterFile, "Spirit");
if (!spirit) { spirit = 0; }
var supply = getVar("Characters/" + characterFile, "Supply");
if (!supply) { supply = 0; }
var wealth = getVar("Characters/" + characterFile, "Wealth");
if (!wealth) { wealth = 0; }
var momentum = getVar("Characters/" + characterFile, "Momentum");
if (!momentum) { momentum = 0; }
var edge = getVar("Characters/" + characterFile, "Edge");
if (!edge) { edge = 0; }
var heart = getVar("Characters/" + characterFile, "Heart");
if (!heart) { heart = 0; }
var iron = getVar("Characters/" + characterFile, "Iron");
if (!iron) { iron = 0; }
var shadow = getVar("Characters/" + characterFile, "Shadow");
if (!shadow) { shadow = 0; }
var wits = getVar("Characters/" + characterFile, "Wits");
if (!wits) { wits = 0; }
var callout = "> [!mechanics]+ Stat check!" + "\n> Edge: " + edge + "   Heart: " + heart + "   Iron: " + iron + "   Shadow: " + shadow + "   Wits: " + wits + "\n>   Health: " + health + "   Spirit: " + spirit + "   Supply: " + supply + "   Momentum: " + momentum + "   Wealth: " + wealth + "\n\n";
return callout;
```
__
stats {optional: Which character file?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut will give that character's current stat and meter values.