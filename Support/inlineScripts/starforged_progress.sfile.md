---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged Progress Tracks.

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
function getTrackImage(currentProgress) {
    let trackImage = "\"[[progress-track-" + currentProgress + ".svg]]\"";
    return trackImage;
}
```
__


__

__
```js
function ticksPP(difficulty) {
    var tpp = 8;
    switch (difficulty) {
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
}
```
__

__

__
```js
function calcXP(characterFile) {
    let characterPath = "Characters/" + characterFile;
    var bProg = getVar(characterPath, "Bonds_Progress");
    if (!bProg) { bProg = 0; }
    var dProg = getVar(characterPath, "Discoveries_Progress");
    if (!dProg) { bProg = 0; }
    var qProg = getVar(characterPath, "Quests_Progress");
    if (!qProg) { bProg = 0; }
    let Progs = [bProg, dProg, qProg];
    alert(Progs);
    var xpEarned = 0;
    var i = 0;
    for (i = 0; i < 3; i = i + 1) {
        if (Progs[i] > 40) {
            xpEarned = xpEarned + 20 + Math.floor((Progs[i] - 40)/4);
        } else {
            xpEarned = xpEarned + (2 * Math.floor(Progs[i]/4));
        }
    }
    return xpEarned;
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
^test
__
```js
let bProg = Number(getVar("Characters/Char2", "Bonds_Progress"));
alert(bProg);
let dProg = Number(getVar("Characters/Char2", "Discoveries_Progress"));
alert(dProg);
let qProg = Number(getVar("Characters/Char2", "Quests_Progress"));
alert(qProg);
return
```
__
test - put it to the test

__
^setprogress ([_a-zA-Z0-9]*) ([0-9]+) ?([_a-zA-Z0-9]*)$
__
```js
let givenTrack = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
var trackTitle = "";
var name = "Stand-in";
var progVar = "Progress";
var trackImageVar = "TrackImage";
var progMax = 40;
var characterFile = "Character_File_Name_Here";
var characterName = "Character_Name_Here";
var difficulty = "Epic";

switch (givenTrack) {
    case "Quests":
    case "Bonds":
    case "Discoveries":
        if (!$3) {
            characterFile = getSoloCharacter();
        } else {
            characterFile = $3;
        }
        trackTitle = "Characters/" + characterFile;
        characterName = getVar(trackTitle, "Name");
        name = characterName + "'s " + givenTrack;
        progVar = givenTrack + "_Progress";
        trackImageVar = givenTrack + "_TrackImage";
        progMax = 80;
        break;
    default:
        trackTitle = "Progress/" + $1;
        name = getVar(trackTitle, "Name");
        difficulty = getVar(trackTitle, "Difficulty");
        break;
}

let progToSet = $2;

if (progToSet > progMax) {
    progToSet = progMax;
}

expand("notevars set " + trackTitle + " " + progVar + " " + progToSet);

var xpEarned = 0;
if (givenTrack == "Quests" || givenTrack == "Bonds" || givenTrack == "Discoveries") {
    if (progToSet > 40) {
        xpEarned = 20 + Math.floor((progToSet - 40)/4);
    } else {
        xpEarned = Math.floor(progToSet/4) * 2;
    }
    let xpVar = givenTrack + "_XPEarned";
    expand("notevars set " + trackTitle + " " + xpVar + " " + xpEarned);
}

var fullBoxes = Math.floor(progToSet/4);
if (fullBoxes > 10) { 
    fullBoxes = fullBoxes - 10;
    progToSet = progToSet - 40;
}

let trackImage = getTrackImage(progToSet);
expand("notevars set " + trackTitle + " " + trackImageVar + " " + trackImage);

var trueTrack = "![[" + trackImage.replace(/["]+/g , "").replace(/\[/g, "").replace(/\]/g, "") + "|350]]";

let callout = "> [!progress]- " + name + ", Progress Set To: " + progToSet + " (Total: " + fullBoxes + " ![[progress-box-4.svg|15]])\n> File Name: [[" + trackTitle + "]], Difficulty: " + difficulty + "\n> " + fullBoxes + " full boxes or " + progToSet + " ticks\n> \n> " + trueTrack + "\n> \n> Milestone: \n\n";

return callout;
```
__
setprogress {either the filename that contains the progress track or use the one-word references of Bonds, Discoveries, or Quests} {# of ticks from 0-40} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  This sets a progress track to the given number of TICKS.  Remember there are four ticks per box.


__
^markprogress ([_a-zA-Z0-9]*) ([0-9]*) ?([_a-zA-Z0-9]*)$
__
```js
let givenTrack = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
var trackTitle = "";
var name = "Stand-in";
var progVar = "Progress";
var trackImageVar = "TrackImage";
var progMax = 40;
var characterFile = "Character_File_Name_Here";
var characterName = "Character_Name_Here";
var difficulty = "Epic";

switch (givenTrack) {
    case "Quests":
    case "Bonds":
    case "Discoveries":
        if (!$3) {
            characterFile = getSoloCharacter();
        } else {
            characterFile = $3;
        }
        trackTitle = "Characters/" + characterFile;
        characterName = getVar(trackTitle, "Name");
        name = characterName + "'s " + givenTrack;
        progVar = givenTrack + "_Progress";
        trackImageVar = givenTrack + "_TrackImage";
        progMax = 80;
        break;
    default:
        trackTitle = "Progress/" + $1;
        difficulty = getVar(trackTitle, "Difficulty");
        name = getVar(trackTitle, "Name");
        break;
}

var currentProgress = getVar(trackTitle, progVar);
let ticksPerProgress = ticksPP(difficulty);
let progressToMark = $2;
let ticksToAdd = Number(ticksPerProgress) * Number(progressToMark);
currentProgress = currentProgress + ticksToAdd;
if (currentProgress > progMax) {
    currentProgress = progMax;
}

expand("notevars set " + trackTitle + " " + progVar + " " + currentProgress);

var xpEarned = 0;
if (givenTrack == "Quests" || givenTrack == "Bonds" || givenTrack == "Discoveries") {
    if (currentProgress > 40) {
        xpEarned = 20 + Math.floor((currentProgress - 40)/4);
    } else {
        xpEarned = Math.floor(currentProgress/4) * 2;
    }
    let xpVar = givenTrack + "_XPEarned";
    expand("notevars set " + trackTitle + " " + xpVar + " " + xpEarned);
}

var fullBoxes = Math.floor(currentProgress/4);
if (fullBoxes > 10) { 
    fullBoxes = fullBoxes - 10;
    currentProgress = currentProgress - 40;
}

let trackImage = getTrackImage(currentProgress);
expand("notevars set " + trackTitle + " " + trackImageVar + " " + trackImage);

var trueTrack = "![[" + trackImage.replace(/["]+/g , "").replace(/\[/g, "").replace(/\]/g, "") + "|350]]";

let callout = "> [!progress]- " + name + ", " + progressToMark + " Progess Marked (Total: " + fullBoxes + " ![[progress-box-4.svg|15]])\n> File Name: [[" + trackTitle + "]], Difficulty: " + difficulty + "\n> " + progressToMark + " progress marked or " + ticksToAdd + " ticks for a total of " + fullBoxes + " full boxes or " + currentProgress + " ticks\n> \n> " + trueTrack + "\n> \n> Milestone: \n\n";

return callout;
```
__
markprogress {either the filename that contains the progress track or use the one-word references of Bonds, Discoveries, or Quests} {times to mark progress} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  The number of times to mark progress will typically be only 1 or 2 (unless a strong hit takes you to 3 or something).  You don't need to calculate the number of ticks to mark UNLESS you are marking on Bonds, Discoveries, or Quests.  For example, on a Dangerous track if you want to mark progress once, just use the number 1.  It will calculate that one progress means 8 ticks (or two boxes) on its own.  However, on Bonds, if you wanted to mark 2 boxes, you would need to input 8 for the 8 tick marks necessary to get 2 boxes.


__
^clearprogress ([_a-zA-Z0-9]*) ([0-9]+) ?([_a-zA-Z0-9]*)$
__
```js
let givenTrack = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
var trackTitle = "";
var name = "Stand-in";
var progVar = "Progress";
var trackImageVar = "TrackImage";
var progMax = 40;
var characterFile = "Character_File_Name_Here";
var characterName = "Character_Name_Here";

switch (givenTrack) {
    case "Quests":
    case "Bonds":
    case "Discoveries":
        if (!$3) {
            characterFile = getSoloCharacter();
        } else {
            characterFile = $3;
        }
        trackTitle = "Characters/" + characterFile;
        characterName = getVar(trackTitle, "Name");
        name = characterName + "'s " + givenTrack;
        progVar = givenTrack + "_Progress";
        trackImageVar = givenTrack + "_TrackImage";
        progMax = 80;
        break;
    default:
        trackTitle = "Progress/" + $1;
        difficulty = getVar(trackTitle, "Difficulty");
        name = getVar(trackTitle, "Name");
        break;
}

var currentProgress = getVar(trackTitle, progVar);
let ticksToClear = $2;
currentProgress = currentProgress - ticksToClear;
if (currentProgress < 0) { currentProgress = 0; }

expand("notevars set " + trackTitle + " " + progVar + " " + currentProgress);

var xpEarned = 0;
if (givenTrack == "Quests" || givenTrack == "Bonds" || givenTrack == "Discoveries") {
    if (currentProgress > 40) {
        xpEarned = 20 + Math.floor((currentProgress - 40)/4);
    } else {
        xpEarned = Math.floor(currentProgress/4) * 2;
    }
    let xpVar = givenTrack + "_XPEarned";
    expand("notevars set " + trackTitle + " " + xpVar + " " + xpEarned);
}

var fullBoxes = Math.floor(currentProgress/4);
if (fullBoxes > 10) { 
    fullBoxes = fullBoxes - 10;
    currentProgress = currentProgress - 40;
}

let trackImage = getTrackImage(currentProgress);
expand("notevars set " + trackTitle + " " + trackImageVar + " " + trackImage);

var trueTrack = "![[" + trackImage.replace(/["]+/g , "").replace(/\[/g, "").replace(/\]/g, "") + "|350]]";


let callout = "> [!progress]- " + name + ", " + ticksToClear + " Progess Ticks Cleared (Total: " + fullBoxes + " ![[progress-box-4.svg|15]])\n> File Name: [[" + trackTitle + "]], Difficulty: " + difficulty + "\n> " + ticksToClear + " progress ticks cleared bringing progress down to " + fullBoxes + " full boxes or " + currentProgress + " ticks\n> \n> " + trueTrack + "\n> \n> Milestone: \n\n";

return callout;
```
__
clearprogress {either the filename that contains the progress track or use the one-word references of Bonds, Discoveries, or Quests} {TICKS to clear} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  THIS USES TICKS ONLY!  If the move says clear so many progress boxes, multiply the number of boxes by 4 to get the number of ticks to clear.  If the move says clear that many units of progress, you'll need to figure out how many ticks that is first.

__
^resetlegacies ?([_a-zA-Z0-9]*)$
__
```js
var characterFile = "Characters/";
if (!$1) {
    characterFile = characterFile + getSoloCharacter();
} else {
    characterFile = characterFile + $1;
}
let characterName = getVar(characterFile, "Name");
let trackImage = getTrackImage(0);
expand("notevars set " + characterFile + " Bonds_Progress 0");
expand("notevars set " + characterFile + " Bonds_TrackImage " + trackImage);
expand("notevars set " + characterFile + " Bonds_XPEarned 0");
expand("notevars set " + characterFile + " Discoveries_Progress 0");
expand("notevars set " + characterFile + " Discoveries_TrackImage " + trackImage);
expand("notevars set " + characterFile + " Discoveries_XPEarned 0");
expand("notevars set " + characterFile + " Quests_Progress 0");
expand("notevars set " + characterFile + " Quests_TrackImage " + trackImage);
expand("notevars set " + characterFile + " Quests_XPEarned 0");
expand("notevars set " + characterFile + " XPSpent 0");

let callout = "> [!progress]- Legacies Reset for " + characterName + "\n> \n\n";

return callout;
```
__
resetlegacies {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  This resets all legacy tracks to 0.

__
^endprogress ([_a-zA-Z0-9]*) ?([_a-zA-Z0-9]*)$
__
```js
let givenTrack = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
var trackTitle = "";
var name = "";
var progVar = "Progress";

switch (givenTrack) {
    case "Quests":
    case "Bonds":
    case "Discoveries":
        if (!$2) {
            characterFile = getSoloCharacter();
        } else {
            characterFile = $2;
        }
        trackTitle = "Characters/" + characterFile;
        characterName = getVar(trackTitle, "Name");
        name = characterName + "'s " + givenTrack;
        progVar = givenTrack + "_Progress";
        break;
    default:
        trackTitle = "Progress/" + $1;
        name = getVar(trackTitle, "Name");
        expand("notevars set " + trackTitle + " tags complete");
        break;
}

let currentProgress = getVar(trackTitle, progVar);
let d10A = Math.floor(Math.random() * 10) + 1;
let d10B = Math.floor(Math.random() * 10) + 1;
var actionRoll = Math.floor(currentProgress/4);
if (actionRoll > 10) { actionRoll = 10; }
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
endprogress {either the filename that contains the progress track or use the one-word references of Bonds, Discoveries, or Quests} {optional: Which character filename?} - For this to know which character stat to use, make sure to use the EXACT file name of the character in the Character folder which can include letters, numbers, and underscore. If left out, it defaults to the first file name in the Character folder.  This will make a challenge roll versus the named progress track and then mark the progress track as complete.

__
^createprogress
__
```js
let options = ["Troublesome", "Dangerous", "Formidable", "Extreme", "Epic"];
var fileName = popups.input("What is the filename of the file to create?", "Progress1");
_inlineScripts.inlineScripts.HelperFncs.fileWrite("Progress/" + fileName + ".md", "");
var name = popups.input("What is the name of the vow, bond, or progress track?", "Find the lost relic");
var difficulty = popups.pick("What is the difficulty of the vow, bond, or progress track?", options, 1);
expand("notevars set Progress/" + fileName + " Name " + name);
expand("notevars set Progress/" + fileName + " Difficulty " + options[difficulty]);
let internalLink = "[[Progress/" + fileName + "|" + fileName + "]]";
let callout = "> [!progress]- New Progress Track Created: " + internalLink + ", Total: 0 ![[progress-box-4.svg|15]]\n> **Name:** " + name + "\n> **Difficulty:** " + options[difficulty] + "\n> **Additional Details:** \n\n"
return callout;
```
__
createprogress - Creates a new file with the Progress Template in the Progress folder and set the filename, vow name, and difficulty through popups.