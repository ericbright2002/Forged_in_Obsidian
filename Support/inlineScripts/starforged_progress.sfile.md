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
     // NOTE: imageLocation should return the image folder of your vault. 
    // "(app://local/C:/Users/ericb/Desktop/SFV3/Images/"
    let imageLocation = getVar("Support/Vault_Info", "imageLocation");
    let trackImage = "![Img|350]" + imageLocation + "IS_Shortcut_Images/ProgressTracks/progress-track-" + currentProgress + ".svg)";
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
^markprogress ([_a-zA-Z0-9]*) ([0-9]*)$
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
let ticksPerProgress = ticksPP(difficulty);
let name = getVar(trackTitle, "Name");
let progressToMark = $2;
let ticksToAdd = Number(ticksPerProgress) * Number(progressToMark);
currentProgress = currentProgress + ticksToAdd;

if (currentProgress > 40) {
    currentProgress = 40;
}
let fullBoxes = Math.floor(currentProgress/4);

expand("notevars set " + trackTitle + " Progress " + currentProgress);
let trackImage = getTrackImage(currentProgress);
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
^endprogress ([_a-zA-Z0-9]*)
__
```js
let trackTitle = "Progress/" + $1;
expand("notevars set " + trackTitle + " tags complete");
let currentProgress = getVar(trackTitle, "Progress");
let name = getVar(trackTitle, "Name");
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

__
^setimagepath (.+)$
__
```js
let fullPath = $1;
let noQuotePath = fullPath.replace(/['"]+/g, '');
expand("notevars set Support/Vault_Info imageLocation " + fullPath);
let trackImage = "\"![Img|350]" + noQuotePath + "IS_Shortcut_Images/ProgressTracks/progress-track-";
let legacies = ["Bonds", "Discoveries", "Quests"];
var currentProgress = 0;
var path = "";
var trueTrack = "";
var currentCheck = "";
var trueCond = "";
for (let i = 0; i < legacies.length; i = i + 1) {
    path = "Character/" + legacies[i];
    currentProgress = getVar(path, "Progress");
    if (!currentProgress) { currentProgress = 0; }
    trueTrack = trackImage + currentProgress + ".svg)\"";
    expand("notevars set " + path + " TrackImage " + trueTrack);
}
let conditionImage = "\"![Img|25]" + noQuotePath + "IS_Shortcut_Images/Conditions/hex-";
let conditions = ["Wounded", "Shaken", "Unprepared", "Harmed", "Traumatized", "Doomed", "Tormented", "Indebted"];
for (let j = 0; j < conditions.length; j = j + 1) {
    path = "Character/Conditions";
    currentCheck = getVar(path, conditions[j]);
    if (currentCheck.includes("unchecked")) {
        trueCond = conditionImage + "uncheckedG.svg)\"";
    } else {
    trueCond = conditionImage + "checkedG.svg)\"";
    }
    expand("notevars set " + path + " " + conditions[j] + " " + trueCond);
}

return ";;setimagepath " + fullPath;
```
__
setimagepath {full path to the Images folder} - this should start and end with double quotes and be the full path such as "(app://local/C:/Users/ericb/Desktop/SFV3/Images/"