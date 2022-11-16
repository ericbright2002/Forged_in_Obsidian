---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged Stats, Meters, and Asset Tracks.

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
^toggle ([a-zA-Z]*)$
__
```js
var onOff = "";
let capWord = $1.charAt(0).toUpperCase() + $1.slice(1).toLowerCase();
let imgPath = getVar("Character/Conditions", capWord);
var imgLoc = getVar("Support/Vault_Info", "imageLocation");
var newPath = "";
if (imgPath.contains("unchecked")) {
    newPath = "\"![Img|25]" + imgLoc + "IS_Shortcut_Images/Conditions/hex-checkedG.svg)\"";
    onOff = "on.";
} else {
    newPath = "\"![Img|25]" + imgLoc + "IS_Shortcut_Images/Conditions/hex-uncheckedG.svg)\"";
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