---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged.

__

__
```js
function countImpacts() {
    let impacts = ["Wounded", "Shaken", "Unprepared", "Harmed", "Traumatized", "Doomed", "Tormented", "Indebted"];
    const file = app.vault.fileMap["Character/Conditions"] || app.vault.fileMap["Character/Conditions.md"];
    const cache = app.metadataCache.getFileCache(file);
    const fm = cache.frontmatter;
    var i = 0;
    var countedImpacts = impacts.length;
    var currentImage = "";
    for (i = 0; i < impacts.length; i = i + 1) {
        currentImage = fm[impacts[i]];
        if (currentImage.contains("unchecked")) {
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
^gettrackimage ([0-9]*)$
__
```js
// NOTE: imageLocation should return the image folder of your vault. 
// "(app://local/C:/Users/ericb/Desktop/SFV3/Images/"
let imageLocation = expand("notevars get Support/Vault_Info imageLocation ");
let trackImage = "![Img|350]" + imageLocation + "ProgressTracks/progress-track-" + $1 + ".svg)";
return trackImage;
```
__
gettrackimage {amount of ticks} - Produces the image for the progress track of the specified amount of ticks.

__
^tickspp ([a-zA-Z]*)$
__
```js
var tpp = 8;
switch ($1) {
    case "Troublesome":
        tpp = 12;
        break;
    case "Dangerous":
        tpp = 8;
        break;
    case "Formidable":
        tpp = 4;
        break;
    case "Extreme":
        tpp = 2;
        break;
    case "Epic":
        tpp = 1;
        break;
    default:
        tpp = 8;
        break;
            }
return tpp;
```
__
tickspp {difficulty} - Turns the difficulty level in words (like "Dangerous") into the number of ticks to be marked (like 8).

__
^markprogress ([a-zA-Z0-9]*) ([0-9]*)$
__
```js
let givenTrack = $1.toUpperCase();
var trackTitle = "";
switch (givenTrack) {
    case "QUESTS":
        trackTitle = "Character/Quests";
        break;
    case "BONDS":
        trackTitle = "Character/Bonds";
        break;
    case "DISCOVERIES":
        trackTitle = "Character/Discoveries";
        break;
    default:
        trackTitle = "Progress/" + $1;
}
var currentProgress = getVar(trackTitle, "Progress");
let difficulty = getVar(trackTitle, "Difficulty");
let ticksPerProgress = expand("tickspp " + difficulty);
let name = getVar(trackTitle, "Name");
let progressToMark = $2;
let ticksToAdd = Number(ticksPerProgress) * Number(progressToMark);
currentProgress = currentProgress + ticksToAdd;

if (currentProgress > 40) {
    currentProgress = 40;
}
let fullBoxes = Math.floor(currentProgress/4);

expand("notevars set " + trackTitle + " Progress " + currentProgress);
let trackImage = expand("gettrackimage " + currentProgress);
expand("notevars set " + trackTitle + " TrackImage \"" + trackImage + "\"");
if (givenTrack == "QUESTS" || givenTrack == "BONDS" || givenTrack == "DISCOVERIES") {
    let xpEarned = fullBoxes * 2;
    expand("notevars set " + trackTitle + " XPEarned " + xpEarned);
}

let callout = "> [!progress]- " + name + ", " + progressToMark + " Progess Marked (Total: " + fullBoxes + " ![[progress-box-4.svg|15]])\n> File Name: [[" + trackTitle + "]], Difficulty: " + difficulty + "\n> " + progressToMark + " progress marked or " + ticksToAdd + " ticks for a total of " + fullBoxes + " full boxes or " + currentProgress + " ticks\n> \n> " + trackImage + "\n> \n> Milestone: \n\n";

return callout;
```
__
markprogress {note file name that contains the progress track} {times to mark progress} - The number of times to mark progress will typically be only 1 or 2 (unless a strong hit takes you to 3 or something).  You don't need to calculate the number of ticks to mark.  For example, on a Dangerous track if you want to mark progress once, just use the number 1.  It will calculate that one progress means 8 ticks (or two boxes) on its own.


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
getmovename {move initials} {return full description? 1 = Yes, 0 = No} - A helper function to get the full name of a move or the markdown of the full move.  IMPORTANT: Single word moves use the full word rather than the initials.

__
^move ([a-zA-Z]*) ([a-zA-Z]*) ([0-9]*)$
__
```js
let moveName = expand("getmovename " + $1);
let statName = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase(); // $2;
let addValue = $3;
var statOrMeter = "Stats ";
switch (statName) {
    case "Heart":
    case "Iron":
    case "Edge":
    case "Wits":
    case "Shadow":
        statOrMeter = "Stats ";
        break;
    case "Health":
    case "Spirit":
    case "Supply":
    case "Wealth":
        statOrMeter = "Meters ";
        break;
    default:
        statOrMeter = "Stats ";
        break;
}
let statValue = expand("notevars get Character/" + statOrMeter + statName);
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

let calloutTitle = calloutType + " " + moveName + ": " + statName + " + " + addValue;
let actionResult = "\n> ![[d6-" + d6 + "-t.svg#invert_W|50]]![[plus-t.svg#invert_W|15]]![[stat-" + statValue + "-t.svg#invert_W|50]]![[plus-t.svg#invert_W|15]]![[add-" + addValue + "-t.svg#invert_W|50]]![[equals-t.svg#invert_W|15]]![[total-" + actionRoll + "-t.svg#invert_W|50]]";
let challengeResult = "\n> ![[vs-t.svg#invert_W|50]]![[d10-" + d10A + "-t.svg#invert_W|50]]![[and-t.svg#invert_W|50]]![[d10-" + d10B + "-t.svg#invert_W|50]]";
let outcome = "\n> ### Result: " + image + " " + result;
resultCallout = calloutTitle + actionResult + challengeResult + outcome + "\n\n";
//let moveCallout = "> [!mechanics]- " + moveName + "\n> ![[" + moveName.replace(/ /g,"_") + "]]\n\n";
return resultCallout;
```
__
move {move initials} {stat} {add} - This will make a challenge roll using the provided stat and add amount on the move provided.  IMPORTANT: Single word moves use the full word rather than the initials.

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
return ">[!mechanics]- Mechanical Results or Choices\n>";
```
__
mech - Returns the beginning of a mechanics box for you to detail the mechanical consequences of a move or action.

__
^endprogress ([a-zA-Z0-9]*)
__
```js
let trackTitle = "Progress/" + $1;
expand("notevars set " + trackTitle + " tags complete");
let currentProgress = expand("notevars get " + trackTitle + " Progress");
let name = expand("notevars get " + trackTitle + " Name");
let d10A = Math.floor(Math.random() * 10) + 1;
let d10B = Math.floor(Math.random() * 10) + 1;
let actionRoll = Math.floor(currentProgress/4);
var result = "";
var image = "";
if (actionRoll > d10A && actionRoll > d10B) {
    result = "Strong Hit";
    image = "![[outcome-strong-hit.svg|35]]";
    if (d10A == d10B) {
        result = result + " with a MATCH!";
    }
} else if (actionRoll > d10A || actionRoll > d10B) {
    result = "Weak Hit";
    image = "![[outcome-weak-hit.svg|35]]";
    if (d10A == d10B) {
        result = result + " with a MATCH!";
    }
} else {
    result = "Miss";
    image = "![[outcome-miss.svg|35]]";
    if (d10A == d10B) {
        result = result + " with a MATCH!";
    }
}
let calloutTitle = "> [!progress]+ " + name + ", Roll to Finish: " + image;
let actionResult = "\n> ![[progress-" + actionRoll + "-t.svg#invert_W|50]]";
let challengeResult = "![[vs-t.svg#invert_W|50]]![[d10-" + d10A + "-t.svg#invert_W|50]]![[and-t.svg#invert_W|50]]![[d10-" + d10B + "-t.svg#invert_W|50]]";
let outcome = "\n> ### Result: " + image + " " + result;
callout = calloutTitle + actionResult + challengeResult + outcome + "\n\n";
return callout;
```
__
endprogress {note file name that contains the progress track} - This will make a challenge roll versus the named progress track and then mark the progress track as complete.


__
^createtrack ([a-zA-Z0-9]*)$
__
```js
let trackName = $1;
let creation = "[[Progress/" + trackName + "|" + trackName + "]]\n";
let setName = ";;notevars set Progress/" + trackName + " Name 'Insert new name here':\n";
let setDifficulty = ";;notevars set Progress/" + trackName + " Difficulty 'Insert difficulty here':\n";
let directions = "Click on the link to create the new page. Then, set the Name and Difficulty in the frontmatter (YAML).";
return creation + directions;
```
__
createtrack {file name} - Creates a new file with the Progress Template in the Progress folder.


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
let outcome = "\n> Additional Details: ";
let callout = calloutTitle + outcome + addDetails;
return callout;
```
__
oracle {the question you are asking} - Type in the oracles initials to roll.

__
^toggle ([a-z]*)$
__
```js
var onOff = "";
let capWord = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
let imgPath = getVar("Character/Conditions", capWord);
var imgLoc = getVar("Support/Vault_Info", "imageLocation");
var newPath = "";
if (imgPath.contains("unchecked")) {
    newPath = "\"![Img|25]" + imgLoc + "hex-checkedR.svg)\"";
    onOff = "on.";
} else {
    newPath = "\"![Img|25]" + imgLoc + "hex-uncheckedR.svg)\"";
    onOff = "off.";
}
expand("notevars set Character/Conditions " + capWord + " " + newPath);
return "> [!mechanics]- " + capWord + " set to " + onOff + "\n\n";
```
__
toggle {condition name} - toggles a condition on/off using the full name of the condition except for "permanently harmed" which should be shortened to just "harmed"


__
^burnmom
__
```js
let countedImpacts = countImpacts();
if (countedImpacts > 1) {
    expand("notevars set Character/Meters Momentum 0");
} else if (countedImpacts == 1) {
    expand("notevars set Character/Meters Momentum 1");
} else {
    expand("notevars set Character/Meters Momentum 2");
}
return "> [!mechanics]+ You burned your momentum!\n> This changed the result to: [Add your result here]";
```
__
burnmom - burns your momentum and resets

__
^gain ([_a-zA-Z]*) ([1-9])$
__
```js
var name = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
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
    meter = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
}
let gain = Number($2);
var meterPath = "Character/Meters";
var max = 5;
let countedImpacts = countImpacts();
switch (meter) {
    case "Health":
    case "Spirit":
    case "Supply":
    case "Wealth":
        break;
    case "Momentum":
        max = 10 - countedImpacts;
        meterPath = "Character/Meters";
        break;
    default:
        let fm = getAssetFM(meter);
        meterPath = fm[0];
        max = getVar(meterPath, "Max");
        name = fm[1];
        break;
}

let current = getVar(meterPath, name);
var newValue = Number(current) + gain;
if (newValue > Number(max)) {
    newValue = Number(max);
}
expand("notevars set " + meterPath + " " + name + " " + newValue);
let callout = "> [!mechanics]- " + meter + "[" + name + "] set to " + newValue + " out of " + max + ".\n\n";
return callout;
```
__
gain {meter name} {amount to add from 1-5} - adds to Health, Spirit, Supply, Wealth, or Momentum

__
^lose ([a-zA-Z]*) ([1-5]*)$
__
```js
var name = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
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
    meter = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
}
let loss = Number($2);
var meterPath = "Character/Meters";
var min = 0;
var max = 5;
let countedImpacts = countImpacts();
switch (meter) {
    case "Health":
    case "Spirit":
    case "Supply":
    case "Wealth":
        break;
    case "Momentum":
        min = -6;
        max = 10 - countedImpacts;
        meterPath = "Character/Meters";
        break;
    default:
        let fm = getAssetFM(meter);
        meterPath = fm[0];
        max = getVar(meterPath, "Max");
        name = fm[1];
        break;
}
let current = getVar(meterPath, name);
var newValue = Number(current) - loss;
var text = "";
var leftover = min - newValue;
if (newValue < min) {
    newValue = min;
        text = " You had to lose more " + name + " than you had. " + leftover + " still to lose.";
}
expand("notevars set " + meterPath + " " + name + " " + newValue);
let callout = "> [!mechanics]+ " + meter + "[" + name + "] set to " + newValue + " out of " + max + ".\n> " + text + "\n\n";
return callout;
```
__
lose {meter name} {amount to add from 1-5} - subtracts from Health, Spirit, Supply, Wealth, or Momentum


__
^yesno ([a-zA-Z]*) (.+)$
__
```js
let theRoll = Math.floor(Math.random() * 100 + 1);
var yesCeiling = 50;
var chances = "Fifty-Fifty";
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
if (theRoll > chances) {
    result += ", Yes";
} else {
    result += ", No";
}
if (theRoll == 100 || theRoll % 11 == 0) {
    result += " with a MATCH!";
}
let calloutTitle = "> [!oracle]- " + $2 + " (" + chances + ") Result: " + result;
let outcome = "\n> Additional Details: ";
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
let outcome = "\n> Additional Details: \n\n";
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
let outcome = "\n> Additional Details: \n\n";
return calloutTitle + outcome;
```
__
df {question you need descriptor/focus for} - Use this function to ask a question seeking more details with the Descriptor/Focus oracles.

__

__
```js
function checkResult(oracle) {
    let iResult = expand("tbl roll " + oracle);
    var trueResult = "";
    var result1 = "";
    var result2 = "";
    if (iResult[0].contains("Roll Twice")) {
        result1 = expand("tbl roll " + oracle);
        result2 = expand("tbl roll " + oracle);
        trueResult = "Roll Twice - " + result1 + ", " + result2;
    } else if (iResult[0].contains("Action") && iResult[0].contains("Theme")) {
        result1 = expand("tbl roll Oracles/Core/Core_Action.md");
        result2 = expand("tbl roll Oracles/Core/Core_Theme.md");
        trueResult = "Action/Theme - " + result1 + " " + result2;
    } else if (iResult[0].contains("Descriptor") && iResult[0].contains("Focus")) {
        result1 = expand("tbl roll Oracles/Core/Core_Descriptor.md");
        result2 = expand("tbl roll Oracles/Core/Core_Focus.md");
        trueResult = "Action/Theme - " + result1 + " " + result2;
    } else {
        trueResult = iResult[0];
    }
    return trueResult;
}
```
__


__
^sectorname
__
```js
let prefix = expand("tbl roll Oracles/Space/Space_Sector_Name_Prefix.md");
let suffix = expand("tbl roll Oracles/Space/Space_Sector_Name_Suffix.md");
return "> [!oracle]- Sector Name: " + prefix + " " + suffix + "\n> Additional Details: \n\n";
```
__
sectorname - gets a random sector name

__
^character
__
```js
let given = expand("tbl roll Oracles/Characters/Characters_Given_Name.md");
let family = expand("tbl roll Oracles/Characters/Characters_Family_Name.md");
let callsign = expand("tbl roll Oracles/Characters/Characters_Callsign.md");
let name = "Name: " + given + " \"" + callsign + "\" " + family;
let fl = "**First Look:** " + expand("tbl roll Oracles/Characters/Characters_First_Look.md");
/* let iGoal = expand("tbl roll Oracles/Characters/Characters_Goal.md");
var trueGoal = "";
if (iGoal[0].contains("Roll Twice")) {
    let goal1 = expand("tbl roll Oracles/Characters/Characters_Goal.md");
    let goal2 = expand("tbl roll Oracles/Characters/Characters_Goal.md");
    trueGoal = "Roll Twice - " + goal1 + ", " + goal2;
} else if (iGoal[0].contains("Action")) {
    let goal1 = expand("tbl roll Oracles/Core/Core_Action.md");
    let goal2 = expand("tbl roll Oracles/Core/Core_Theme.md");
    trueGoal = "Action/Theme - " + goal1 + " " + goal2;
} else {
    trueGoal = iGoal[0];
} */
let goal = "**Goal:** " + checkResult("Oracles/Characters/Characters_Goal.md");
let iniDis = "**Initial Disposition:** " + expand("tbl roll Oracles/Characters/Characters_Initial_Disposition.md");
let revAsp = "**Revealed Aspect:** " + expand("tbl roll Oracles/Characters/Characters_Revealed_Aspect.md");
/* let iRole = expand("tbl roll Oracles/Characters/Characters_Role.md");
var trueRole = "";
if (iRole[0].contains("Roll Twice")) {
    let role1 = expand("tbl roll Oracles/Characters/Characters_Role.md");
    let role2 = expand("tbl roll Oracles/Characters/Characters_Role.md");
    trueRole = "Roll Twice - " + role1 + ", " + role2;
} else if (iRole[0].contains("Action")) {
    let role1 = expand("tbl roll Oracles/Core/Core_Action.md");
    let role2 = expand("tbl roll Oracles/Core/Core_Theme.md");
    trueRole = "Action/Theme - " + role1 + " " + role2;
} else {
    trueRole = iRole[0];
} */
let role = "**Role:** " + checkResult("Oracles/Characters/Characters_Role.md");
let callout = "> [!oracle]- NPC: " + name;
return callout + "\n> " + fl + "\n> " + iniDis + "\n> " + revAsp + "\n> " + role + "\n> " + goal + "\n\n";
```
__
character - gets a random NPC

__
^settlement ([a-zA-Z]*)$
__
```js
let area = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
let name = expand("tbl roll Oracles/Settlements/Settlements_Name.md");
let fl = "**First Look:** " + expand("tbl roll Oracles/Settlements/Settlements_First_Look.md");
let iniCon = "**Initial Contact:** " + expand("tbl roll Oracles/Settlements/Settlements_Initial_Contact.md");
let authority = "**Authority:** " + expand("tbl roll Oracles/Settlements/Settlements_Authority.md");
let project = "**Project:** " + expand("tbl roll Oracles/Settlements/Settlements_Projects.md");
let trouble = "**Trouble:** " + expand("tbl roll Oracles/Settlements/Settlements_Trouble.md");
let pop = "**Population:** " + expand("tbl roll Oracles/Settlements/Settlements_Population_" + area + ".md");
let callout = "> [!oracle]- Settlement: " + name;
return callout + "\n> " + pop + "\n> " + iniCon + "\n> " + authority + "\n> " + fl + "\n> " + trouble + "\n> " + project + "\n\n";
```
__
settlement {location: terminus, expanse, outlands} - gets a random settlement

__
^faction
__
```js
let type = expand("tbl roll Oracles/Factions/Factions_Type.md");
var newType = "";
switch (type[0]) {
    case "Dominion":
        newType = "**Dominion:** " + expand("tbl roll Oracles/Factions/Factions_Dominion.md");
        break;
    case "Guild":
        newType = "**Guild:** " + expand("tbl roll Oracles/Factions/Factions_Guild.md");
        break;
    case "Fringe Group":
        newType = "**Fringe Group:** " + expand("tbl roll Oracles/Factions/Factions_Fringe_Group.md");
        break;
    default:
        newType = "**Dominion:** " + expand("tbl roll Oracles/Factions/Factions_Dominion.md");
        break;
}
let influence = "**Influence:** " + expand("tbl roll Oracles/Factions/Factions_Influence.md");
let leadership = "**Leadership:** " + expand("tbl roll Oracles/Factions/Factions_Leadership.md");
let project = "**Project:** " + expand("tbl roll Oracles/Factions/Factions_Projects.md");
let quirk = "**Quirk:** " + expand("tbl roll Oracles/Factions/Factions_Quirks.md");
let rumor = "**Rumor:** " + expand("tbl roll Oracles/Factions/Factions_Rumors.md");
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
return callout + "\n> " + newType + "\n> " + influence + "\n> " + leadership + "\n> " + quirk + "\n> " + project + "\n> " + rumor + "\n\n";
```
__
faction - gets a random faction

__
^starship ([a-zA-Z]*)$
__
```js
let area = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
let name = expand("tbl roll Oracles/Starships/Starships_Name.md");
let fl = "**First Look:** " + expand("tbl roll Oracles/Starships/Starships_First_Look.md");
let iniCon = "**Initial Contact:** " + expand("tbl roll Oracles/Starships/Starships_Initial_Contact.md");
let type = "Type: " + expand("tbl roll Oracles/Starships/Starships_Type.md");
let mission = "**Mission:** " + expand("tbl roll Oracles/Starships/Starships_Mission_" + area + ".md");
let callout = "> [!oracle]- Starship: " + name + " (" + type + " class)";
return callout + "\n> " + iniCon + "\n> " + fl + "\n> " + mission + "\n\n";
```
__
starship {location: terminus, expanse, outlands} - gets a random starship

__
^creature ([a-zA-Z]*)$
__
```js
let input = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
var environment = "Air";
if (input == "Random") {
    environment = expand("tbl roll Oracles/Creatures/Creatures_Environment.md");
} else {
    environment = input;
}
let form = expand("tbl roll Oracles/Creatures/Creatures_Basic_Form_" + environment + ".md");
if (form[0] == "Roll Twice") {
    let form1 = expand("tbl roll Oracles/Creatures/Creatures_Basic_Form_" + environment + ".md");
    let form2 = expand("tbl roll Oracles/Creatures/Creatures_Basic_Form_" + environment + ".md");
    let trueForm = "Form: " + form1 + ", " + form2;
} else {
    trueForm = "Form: " + form;
}
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
let eb1 = expand("tbl roll Oracles/Creatures/Creatures_Encountered_Behavior.md");
var trueEB = "";
if (eb1[0].contains("Roll Twice")) {
    let eb2 = expand("tbl roll Oracles/Creatures/Creatures_Encountered_Behavior.md");
    let eb3 = expand("tbl roll Oracles/Creatures/Creatures_Encountered_Behavior.md");
    trueEB = eb2 + ", " + eb3;
} else {
    trueEB = eb1;
}
var eb = "**Encountered Behavior:** " + trueEB;
let callout = "> [!oracle]- Creature: " + trueForm + " (" + environment + ")";
return callout + "\n> " + trueEnv + "\n> " + trueScale + "\n> " + fl + "\n> " + ra + "\n> " + eb + "\n\n";
```
__
creature {environment} - gets a random creature in environment (air, interior, land, liquid, space, or random for a random environment)


__
^derelict
__
```js
let type = expand("tbl roll Oracles/Derelicts/Derelicts_Location_Type.md");
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
let type = expand("tbl roll Oracles/Derelicts/Derelicts_Location_Type.md");
let fl = "**Outer First Look:** " + expand("tbl roll Oracles/Vaults/Vaults_Outer_First_Look.md");
let form = "**Form:** " + expand("tbl roll Oracles/Vaults/Vaults_Form.md");
let location = "**Location:** " + expand("tbl roll Oracles/Vaults/Vaults_Location.md");
let scale = "**Scale:** " + expand("tbl roll Oracles/Vaults/Vaults_Scale.md");
let iMat = expand("tbl roll Oracles/Vaults/Vaults_Material.md");
var trueMat = "";
if (iMat == "Roll Twice") {
    let mat1 = expand("tbl roll Oracles/Vaults/Vaults_Material.md");
    let mat2 = expand("tbl roll Oracles/Vaults/Vaults_Material.md");
    trueMat = mat1 + ", " + mat2;
} else {
    trueMat = iMat;
}
let material = "**Material:** " + trueMat;
let iShape = expand("tbl roll Oracles/Vaults/Vaults_Shape.md");
var trueShape = "";
if (iShape == "Roll Twice") {
    let shape1 = expand("tbl roll Oracles/Vaults/Vaults_Shape.md");
    let shape2 = expand("tbl roll Oracles/Vaults/Vaults_Shape.md");
    trueShape = shape1 + ", " + shape2;
} else {
    trueShape = iShape;
}
let shape = "**Shape:** " + trueShape;
let callout = "> [!oracle]- Precursor Vault";
return callout + "\n> " + scale + "\n> " + location + "\n> " + form + "\n> " + material + "\n> " + shape + "\n> " + fl + "\n\n";
```
__
vault - gets a random vault


__
^planet ([a-zA-Z]*) ([a-zA-Z]*)$
__
```js
let pClass = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
let region = $2.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
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
let atmo = "**Atmosphere:** " + expand("tbl roll Oracles/Planets/Planets_" + trueClass + "_Atmosphere.md");
let feat1 = expand("tbl roll Oracles/Planets/Planets_" + trueClass + "_Feature.md");
var trueFeat = "";
if (feat1[0].contains("Descriptor")) {
    let descriptor = expand("tbl roll Oracles/Core/Core_Descriptor.md");
    let focus = expand("tbl roll Oracles/Core/Core_Focus.md");
    trueFeat = "Descriptor/Focus - " + descriptor + " " + focus;
} else {
    trueFeat = feat1;
}
let feat = "**Feature:** " + trueFeat;
let life = "**Life:** " + expand("tbl roll Oracles/Planets/Planets_" + trueClass + "_Life.md");
let obsv1 = expand("tbl roll Oracles/Planets/Planets_" + trueClass + "_Observed_from_Space.md");
var trueObsv = "";
if (obsv1[0].contains("Descriptor")) {
    let descriptor = expand("tbl roll Oracles/Core/Core_Descriptor.md");
    let focus = expand("tbl roll Oracles/Core/Core_Focus.md");
    trueObsv = "Descriptor/Focus - " + descriptor + " " + focus;
} else {
    trueObsv = obsv1;
}
let obsv = "**Observed from Space:** " + trueObsv;
let sett = "**Settlements:** " + expand("tbl roll Oracles/Planets/Planets_" + trueClass + "_Settlements_" + region + ".md");
let callout = "> [!oracle]- Planet Class: " + trueClass;
let nextStep = "\n> " + obsv + "\n> " + atmo + "\n> " + feat + "\n> " + life;
var finalAdd = "";
if (trueClass == "Vital") {
    let diversity = expand("tbl roll Oracles/Planets/Planets_Vital_Diversity.md");
    var numBio = 2;
    if (diversity[0].contains("Diverse")) { numbBio = 3;}
    if (diversity[0].contains("Complex")) { numbBio = 4;}
    if (diversity[0].contains("Garden")) { numbBio = 5;}
    var biomes = "**Biomes:** ";
    for (let i = 0; i < numbBio; i = i + 1) {
        if (i == 0) {
            biomes = biomes + expand("tbl roll Oracles/Planets/Planets_Vital_Biomes.md");
        } else {
            biomes = biomes + ", " + expand("tbl roll Oracles/Planets/Planets_Vital_Biomes.md");
        }
    }
    finalAdd = "\n> **Diversity:** " + diversity + "\n> " + biomes;
}
return callout + nextStep + finalAdd + "\n\n";
```
__
planet {class} {region} - gets a random planet of {class: desert, furnace, grave, ice, jovian, jungle, ocean, rocky, shattered, tainted, vital or random} in {region: terminus, expanse, outlands}


__
^tbl multiroll (n|y) (.*)$
__
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
__
tbl multiroll {use expansion format: y OR n} {table files: space-separated path texts} - Rolls one instance of each table in {table files}. If {use expansion format} is "y", prints the expansion using the standard expansion format.