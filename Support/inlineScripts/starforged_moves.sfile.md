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
^move ([a-zA-Z]*) ([a-zA-Z]*) ?([0-9]*)$
__
```js
let moveName = expand("getmovename " + $1);
let statName = $2.charAt(0).toUpperCase() + $2.slice(1).toLowerCase(); // $2;
var addValue = $3;
if (!$3) {
	addValue = 0;
}
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