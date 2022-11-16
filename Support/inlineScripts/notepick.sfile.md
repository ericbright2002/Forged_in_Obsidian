---
obsidianUIMode: preview
---

Shortcuts to pick X random notes from a folder of notes and to get their front matter data.

This shortcut-file has a tutorial video available:
- [Using the "notepick" shortcut-file to randomly pick notes & get variables](https://www.youtube.com/watch?v=G1mvl-VwbIQ) (runtime 3:22)


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Setup the state
confirmObjectPath("_inlineScripts.state.sessionState.notepick");

// Event callback - state.onReseet
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.notepick",
	function()
	{
		expand("notepick reset noconfirm");
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
delete _inlineScripts.state?.listeners?.onReset?.notepick;

// State removal
delete _inlineScripts.state?.sessionState?.notepick;
```
__
Shuts down this shortcut-file


__
```
^notepick reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Notepick</b> system")) { return null; }

// Reset
expand("notepick reset noconfirm");

return expFormat("All notepicks cleared.");
```
__
notepick reset - Clears all picks.
***


__
```
^notepick reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Restart the state
confirmObjectPath("_inlineScripts.state.sessionState");
_inlineScripts.state.sessionState.notepick = {};
```
__
hidden - No-confirm reset


__
__
```js
// Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Pick an item from array a.
function aPick(a) { return a[roll(a.length)-1]; }
```
__
Helper scripts


__
```
^notepick pickFromFolderAndGetPick ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(.*)$
```
__
```js
// Remove any quotes around the folder path
$1 = $1.replaceAll(/^\"|\"$/g, "");

// Pick random items.  If error, early-out.
let result = expUnformat(
	expand("notepick pickFromFolder " + $1 + " " + $2 + " " + $3 + " " + $4));
if (!result[0]) { return expFormat(result); }

// Return the list of items picked
return expand("notepick getPick " + $3);
```
__
notepick pickFromFolderAndGetPick {folder name: path text} {pick count: >0, default: 1} {pick id: name text, default: ""} {to ignore: text ( | separated filenames)} - Combines the shortcuts "notepick pickFromFolder" and "notepick getPick".


__
```
^notepick pickFromFolderAndGetFrontmatter ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(.*)$
```
__
```js
// Remove any quotes around the folder path
$1 = $1.replaceAll(/^\"|\"$/g, "");

// Pick random items.  If error, early-out.
let result = expUnformat(
	expand("notepick pickFromFolder " + $1 + " " + $2 + " " + $3 + " " + $4));
if (!result[0]) { return expFormat(result); }

// Get the front matter for the items.  If error, early-out.
result = expUnformat(expand("notepick frontmatter " + $3));
if (!result[0]) { return expFormat(result); }

return result[1];
```
__
notepick pickFromFolderAndGetFrontmatter {folder name: path text} {pick count: >0, default: 1} {pick id: name text, default: ""} {to ignore: text ( | separated filenames)} - Combines the shortcuts "notepick pickFromFolder" and "notepick frontmatter".
***


__
```
^notepick pickFromFolder ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(.*)$
```
__
```js
// Remove any quotes around the folder path
$1 = $1.replaceAll(/^\"|\"$/g, "");
// Count defaults to 1
$2 = Number($2) || 1;
// Split toIgnore into filenames
$4 = $4.split("|");

// Get the file object for the given folder.  Early out of doesn't exist or is a file
const folder = app.vault.fileMap[$1];
if (!folder)
{
	return expFormat([ "","No files picked.  Folder __" + $1 + "__ doesn't exist." ]);
}
if (!folder.children)
{
	return expFormat([ "", "No files picked.  __" + $1 + "__ isn't a folder." ]);
}

// Get the non-folder children of folder
let files = folder.children.filter(v => !v.children).map(v => v.path);

// Remove the files excluded by the "to ignore" parameter
files = files.filter(v => !$4.includes(v));

// If file count is under the pick count, early out.
if (files.length < $2)
{
	return expFormat(
		[ "", "No files picked.  Not enough files in Folder __" + $1 + "__." ]);
}

// Randomly pick <count> of the files.
const pick = [];
while (pick.length < $2)
{
	const nextPickItem = aPick(files);
	if (!pick.includes(nextPickItem))
	{
		pick.push(nextPickItem);
	}
}

// Add the files picked to the state
_inlineScripts.state.sessionState.notepick[$3] = pick;

return expFormat([ $2 + " file(s) picked" + ($3 ? " for " + $3 : "") + "." ]);
```
__
notepick pickFromFolder {folder name: path text} {count: >0, default: 1} {pick id: name text, default: ""} {to ignore: text ( | separated filenames)} - Picks {count} random notes from folder {folder name} and remembers them as {pick id}.  Any files in {to ignore} are never picked.


__
```
^notepick getPick ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
// Get the specified pick from the state.  Early out if specified pick isn't stored.
const picks = _inlineScripts.state.sessionState.notepick[$1];
if (!picks)
{
	return expFormat([ "", "No pick.  Pick id __" + $1 + "__ not found." ]);
}

return expFormat(
	[ "Pick" + ($1 ? " __" + $1 + "__" : "") + ":\n", picks.join("\n"), "" ]);
```
__
notepick getPick {pick id: name text, default: ""} - Gets a list of the files last picked for {pick id}.


__
```
^notepick frontmatter ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
// Get the specified pick from the state.  Early out if specified pick isn't stored.
const pick = _inlineScripts.state.sessionState.notepick[$1];
if (!pick)
{
	return expFormat(
		[ "", "No frontmatter gathered.  Pick id __" + $1 + "__ not found." ]);
}

// Get the file objects of the picks
const files = pick.map(v => app.vault.fileMap[v]);

// Confirm that all picks have valid file objects.  If not, early out.
for (let i = 0; i < pick.length; i++)
{
	if (!files[i])
	{
		return expFormat(
			[ "", "No frontmatter gathered.  __" + fileNotFound + "__ not found." ]);
	}
}

// Get the frontmatter for each of the picks.
let result = {};
for (const file of files)
{
	result[file.name.replace(/.md$/, "")] =
		app.metadataCache.getFileCache(file).frontmatter || {};
}

return expFormat(
	[ "Frontmatter gathered for " + pick.length + " note(s).\n", result, "" ]);
```
__
notepick frontmatter {pick id: name text, default: ""} - Gets the frontmatter from the notes that are remembered in {pick id}.
