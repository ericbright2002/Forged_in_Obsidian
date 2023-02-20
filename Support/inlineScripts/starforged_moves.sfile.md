---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged Moves.

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
^getmovename ([a-zA-Z]*)$
__
```js
let anyCase = $1;
let moveInitials = anyCase.toUpperCase();

var moveName = "";

switch (anyCase.toUpperCase()) {
    case "ADVANCE":
        moveName = "Advance";
        break;
    case "AYA":
        moveName = "Aid Your Ally";
        break;
    case "ATO":
        moveName = "Ask the Oracle";
        break;
    case "BATTLE":
        moveName = "Battle";
        break;
    case "BAS":
        moveName = "Begin a Session";
        break;
    case "CYF":
        moveName = "Change Your Fate";
        break;
    case "CYG":
        moveName = "Check Your Gear";
        break;
    case "CLASH":
        moveName = "Clash";
        break;
    case "CTH":
        moveName = "Companion Takes a Hit";
        break;
    case "COMPEL":
        moveName = "Compel";
        break;
    case "CC":
        moveName = "Confront Chaos";
        break;
    case "CAL":
        moveName = "Continue a Legacy";
        break;
    case "DYR":
        moveName = "Develop Your Relationship";
        break;
    case "EE":
        moveName = "Earn Experience";
        break;
    case "EAS":
        moveName = "End a Session";
        break;
    case "EH":
        moveName = "Endure Harm";
        break;
    case "ES":
        moveName = "Endure Stress";
        break;
    case "ETF":
        moveName = "Enter the Fray";
        break;
    case "EAW":
        moveName = "Explore a Waypoint";
        break;
    case "FD":
        moveName = "Face Danger";
        break;
    case "FDEATH":
        moveName = "Face Death";
        break;
    case "FDEFEAT":
        moveName = "Face Defeat";
        break;
    case "FDESOLATION":
        moveName = "Face Desolation";
        break;
    case "FAE":
        moveName = "Finish an Expedition";
        break;
    case "FAB":
        moveName = "Forge a Bond";
        break;
    case "FORSAKE":
        moveName = "Forsake Your Vow";
        break;
    case "FULFILL":
        moveName = "Fulfill Your Vow";
        break;
    case "GG":
        moveName = "Gain Ground";
        break;
    case "GI":
        moveName = "Gather Information";
        break;
    case "HEAL":
        moveName = "Heal";
        break;
    case "HEARTEN":
        moveName = "Hearten";
        break;
    case "LM":
        moveName = "Lose Momentum";
        break;
    case "MAC":
        moveName = "Make a Connection";
        break;
    case "MAD":
        moveName = "Make a Discovery";
        break;
    case "OD":
        moveName = "Overcome Destruction";
        break;
    case "PTP":
        moveName = "Pay the Price";
        break;
    case "RAM":
        moveName = "Reach a Milestone";
        break;
    case "RUF":
        moveName = "React Under Fire";
        break;
    case "REPAIR":
        moveName = "Repair";
        break;
    case "RESUPPLY":
        moveName = "Resupply";
        break;
    case "SR":
        moveName = "Sacrifice Resources";
        break;
    case "SAA":
        moveName = "Secure an Advantage";
        break;
    case "SAC":
        moveName = "Set a Course";
        break;
    case "SAF":
        moveName = "Set a Flag";
        break;
    case "SOJOURN":
        moveName = "Sojourn";
        break;
    case "STRIKE":
        moveName = "Strike";
        break;
    case "SAIV":
        moveName = "Swear an Iron Vow";
        break;
    case "TAB":
        moveName = "Take a Break";
        break;
    case "TDA":
        moveName = "Take Decisive Action";
        break;
    case "TYR":
        moveName = "Test Your Relationship";
        break;
    case "UAE":
        moveName = "Undertake an Expedition";
        break;
    case "WD":
        moveName = "Withstand Damage";
        break;
    default:
        moveName = "No such move!";
}

var splitStr = moveName.toLowerCase().split(' ');
for (var i = 0; i < splitStr.length; i++) {
   // You do not need to check if i is larger than splitStr length, as your for does that for you
   // Assign it back to the array
   splitStr[i] = splitStr[i].charAt(0).toUpperCase() + splitStr[i].substring(1);     
}
// Directly return the joined string
let capName = splitStr.join(' '); 

return moveName;
```
__
getmovename {move initials} - A helper function to get the full name of a move or the markdown of the full move.  IMPORTANT: Single word moves use the full word rather than the initials.

__
^move ([a-zA-Z]*) ([a-zA-Z]*) ([0-9]*) ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
let moveName = expand("getmovename " + $1);
if (moveName == "No such move!") { return moveName; }
let statName = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
var trueStat = "";
if (statName.contains("_")) {
    let nameArray = statName.split("_");
    for (let i = 0; i < nameArray.length; i = i + 1) {
        if (i < 1) {
            trueStat = nameArray[i].charAt(0).toUpperCase() + nameArray[i].slice(1).toLowerCase();
        } else {
            trueStat = trueStat + "_" + nameArray[i].charAt(0).toUpperCase() + nameArray[i].slice(1).toLowerCase();
        }
    }
} else {
    trueStat = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase();
}

if (trueStat == "Cv") { trueStat = "Starship"; }

var addValue = $3;
if (!$4) {
	characterFile = getSoloCharacter();
} else {
    characterFile = $4;
}

var statPath = "Characters/" + characterFile;
var finalName = trueStat;
switch (trueStat) {
    case "Health":
    case "Spirit":
    case "Supply":
    case "Wealth":
    case "Edge":
    case "Heart":
    case "Iron":
    case "Shadow":
    case "Wits":
        break;
    default:
        let fm = getAssetFM(trueStat);
        statPath = fm[0];
        finalName = fm[1];
        break;
}

let statValue = getVar(statPath, finalName);
if (!statValue) { statValue = 0; }
let d10A = Math.floor(Math.random() * 10) + 1;
let d10B = Math.floor(Math.random() * 10) + 1;
let d6 = Math.floor(Math.random() * 6) + 1;
var actionRoll = d6 + Number(statValue) + Number(addValue);
if (actionRoll > 10) {actionRoll = 10;}
var result = "";
var image = "";
let calloutType = "> [!challenge-miss]+";
if (actionRoll > d10A && actionRoll > d10B) {
    result = "Strong Hit";
    calloutType = "> [!challenge-strong]-";
    image = "![[outcome-strong-hit.svg|50]]";
    if (d10A == d10B) {
        result = result + " with a MATCH!";
    }
} else if (actionRoll > d10A || actionRoll > d10B) {
    result = "Weak Hit";
    calloutType = "> [!challenge-weak]-";
    image = "![[outcome-weak-hit.svg|50]]";
    if (d10A == d10B) {
        result = result + " with a MATCH!";
    }
} else {
    result = "Miss";
    calloutType = "> [!challenge-miss]-";
    image = "![[outcome-miss.svg|50]]";
    if (d10A == d10B) {
        result = result + " with a MATCH!";
    }
}
let characterName = getVar("Characters/" + characterFile, "Name");
let calloutTitle = calloutType + " " + characterName + " " + moveName + ": " + finalName + " + " + addValue;
let actionResult = "\n> ![[d6-" + d6 + "-t.svg#invert_W|50]]![[plus-t.svg#invert_W|15]]![[stat-" + statValue + "-t.svg#invert_W|50]]![[plus-t.svg#invert_W|15]]![[add-" + addValue + "-t.svg#invert_W|50]]![[equals-t.svg#invert_W|15]]![[total-" + actionRoll + "-t.svg#invert_W|50]]";
let challengeResult = "\n> ![[vs-t.svg#invert_W|50]]![[d10-" + d10A + "-t.svg#invert_W|50]]![[and-t.svg#invert_W|50]]![[d10-" + d10B + "-t.svg#invert_W|50]]";
let outcome = "\n> ### Result: " + image + " " + result;
resultCallout = calloutTitle + actionResult + challengeResult + outcome + "\n\n";
return resultCallout;
```
__
move {move initials} {stat/asset name} {add value} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut will make a challenge roll using the provided stat or asset name (if rolling +integrity for example) and add amount on the move provided.  IMPORTANT: Single word moves use the full word rather than the initials. i.e. Use "Battle" instead of just "B".   

__
^sh ([a-zA-Z]*) ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
let moveName = expand("getmovename " + $1);
if (moveName == "No such move!") { return moveName; }
if (!$2) {
	characterFile = getSoloCharacter();
} else {
    characterFile = $2;
}
let characterName = getVar("Characters/" + characterFile, "Name");
let result = "Strong Hit";
let calloutType = "> [!challenge-strong]-";
let image = "![[outcome-strong-hit.svg|50]]";
let calloutTitle = calloutType + " " + characterName + " Auto Strong Hit on " + moveName;
let outcome = "\n> ### Result: " + image + " " + result;
let resultCallout = calloutTitle + outcome + "\n\n";
return resultCallout;
```
__
sh {move initials} {optional: Which character file?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut will make an automatic strong hit for the move.  IMPORTANT: Single word moves use the full word rather than the initials. i.e. Use "Battle" instead of just "B".

__
^wh ([a-zA-Z]*) ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
let moveName = expand("getmovename " + $1);
if (moveName == "No such move!") { return moveName; }
if (!$2) {
	characterFile = getSoloCharacter();
} else {
    characterFile = $2;
}
let characterName = getVar("Characters/" + characterFile, "Name");
let result = "Weak Hit";
let calloutType = "> [!challenge-weak]-";
let image = "![[outcome-weak-hit.svg|50]]";
let calloutTitle = calloutType + " " + characterName + " Auto Weak Hit on " + moveName;
let outcome = "\n> ### Result: " + image + " " + result;
let resultCallout = calloutTitle + outcome + "\n\n";
return resultCallout;
```
__
wh {move initials} {optional: Which character file?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut will make an automatic weak hit for the move.  IMPORTANT: Single word moves use the full word rather than the initials. i.e. Use "Battle" instead of just "B".

__
^miss ([a-zA-Z]*) ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Character_File_Name_Here";
let moveName = expand("getmovename " + $1);
if (moveName == "No such move!") { return moveName; }
if (!$2) {
	characterFile = getSoloCharacter();
} else {
    characterFile = $2;
}
let characterName = getVar("Characters/" + characterFile, "Name");
let result = "Miss";
let calloutType = "> [!challenge-miss]-";
let image = "![[outcome-miss.svg|50]]";
let calloutTitle = calloutType + " " + characterName + " Auto Miss on " + moveName;
let outcome = "\n> ### Result: " + image + " " + result;
let resultCallout = calloutTitle + outcome + "\n\n";
return resultCallout;
```
__
miss {move initials} {optional: Which character file?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  Then this shortcut will make an automatic strong hit for the move.  IMPORTANT: Single word moves use the full word rather than the initials. i.e. Use "Battle" instead of just "B".

__
^moveref ([a-zA-Z]*)$
__
```js
let moveName = expand("getmovename " + $1);
let moveCallout = "> [!mechanics]- " + moveName + "\n> ![[" + moveName.replace(/ /g,"_") + "]]\n\n";
return moveCallout;
```
__
moveref {move initials} - gives a callout box with the move information

__
^mech
__
```js
return ">[!mechanics]- Mechanical Results or Choices\n> More Info: \n\n";
```
__
mech - Returns the beginning of a mechanics box for you to detail the mechanical consequences of a move or action.

__
^roll ([0-9]*)d([0-9]*)$
__
```js
let amount = $1;
let sides = $2;
let dieRoll = $1 + "d" + $2;

var diceArray = [];
var diceResult = 0;
var diceSum = 0;
for (let i = 0; i < amount; i = i + 1) {
    diceResult = Math.floor(Math.random() * sides) + 1;
    diceArray.push(diceResult);
    diceSum = diceSum + diceResult;
}
var returnResult = " Sum: " + diceSum;
var calloutType = ">[!mechanics]- " + dieRoll + returnResult;
var interior = "\n> Individual Rolls: " + diceArray.join(", ") + "\n> \n> More Info: \n\n";
return calloutType + interior;
```
__
roll {# of dice}d{# of sides on the dice} - This rolls the given dice and returns the sum in the header and individual results inside the callout.