---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts to get and set note-variables.  Note-variables are variables set in the YAML frontmatter.  They can be used by many plugins, including DataView and TTRPG StatBlocks.

This shortcut file can be used by other shortcut-files to let them read and manipulate data in notes for many uses, including TTRPG character sheets.

This shortcut-file has a tutorial video available:
- [Using the "notevars" shortcut-file to work with note variables](https://www.youtube.com/watch?v=EsV4WcMwhbA) (runtime 6:59)

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the isMarkdownRefreshed flag.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Initialize state (just a flag)
confirmObjectPath(
	"_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed", true);

// Event callback - state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.notevars",
	function()
	{
		expand("notevars reset noconfirm");
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
delete _inlineScripts.state?.listeners?.onReset?.notevars;

// State removal
delete _inlineScripts.state?.sessionState?.notevars;
```
__
Shuts down this shortcut-file


__
```
^notevars reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Notevars</b> system")) { return null; }

// Reset
expand("notevars reset noconfirm");

return expFormat("Notevars cleared.");
```
__
notevars reset - Clears the isMarkdownRefreshed flag.
***


__
```
^notevars reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Reset the state
confirmObjectPath("_inlineScripts.state.sessionState.notevars");
_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed = true;
```
__
hidden - No-confirm reset


__
```
^notevars isMarkdownRefreshed ?(|[y|n])$
```
__
```js
// If parameter is specified, update the flag based on it
if ($1)
{
	_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed = ($1 === "y");
}

// Return a notification announcing the flag's current state
return expFormat(
	"notevars isMarkdownRefreshed is " +
	(_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed ?
	"__enabled__" : "__disabled__"));
```
__
notevars isMarkdownRefreshed {state: y OR n, default: ""} - If {state} is given, assigns it to the notevars "isMarkdownRefreshed" flag.  Otherwise, displays the current "isMarkdownRefreshed" flag.
    - If isMarkdownRefreshed flag is set then a note's markdown is refreshed each time one of it's variables is set.
***


__
```
^notevars get ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*)
```
__
```js
// Remove any quotes around the note path parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path; }

// Get the file object for the specified note.  Early if not available or is a folder
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];
if (!file)
{
	return expFormat([ "", "No value.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "No value.  __" + $1 + "__ is a folder." ]);
}

// Get the file's cached data.  Early out if not available.
const cache = app.metadataCache.getFileCache(file);
if (!cache)
{
	return expFormat(
		[ "", "No value.  Note __" + $1 + "__ is not properly cached by Obsidian." ]);
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
const result = fm[$2] || fm[$2 + ":"] || null;

// Return the result.  If it's an array, it's joined to a single string.
return Array.isArray(result) ? result.join(",") : result;
```
__
notevars get {note name: path text} {variable name: name text} - Expands to the value of variable {variable name} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars getArray ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*) ([0-9]+)$
```
__
```js
// Remove any quotes around the note path parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path; }

// Get the file object for the specified note.  Early out if unavailable, or folder
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];
if (!file)
{
	return expFormat([ "", "No value.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "No value.  __" + $1 + "__ is a folder." ]);
}

// Get the file's cached data.  Early out if unavailable
const cache = app.metadataCache.getFileCache(file);
if (!cache)
{
	return expFormat(
		[ "", "No value.  Note __" + $1 + "__ is not properly cached by " +
		"Obsidian." ]);
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
const result = fm[$2] || fm[$2 + ":"] || null;

// Return the result, indexed by $3 (as an array)
return Array.isArray(result) ? result[$3] : ($3 === "0") ? result : null;
```
__
notevars getArray {note name: path text} {array name: name text} {index: >=0} - Expands to the value of item {index} of array {array name} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars set ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
// Remove any quotes that surround the path
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path || ""; }

// Get the file object for the specified note.  Early out if unavailable or is folder
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];
if (!file)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set.  __" + $1 + "__ is a folder." ]);
}

// Get the file's content.  Early out if unavailable.
const content = await app.vault.cachedRead(file);
if (content === null || content === undefined)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set. Note __" + $1 + "__ read failed." ]);
}

// Adjust value to resolve escape characters for newline and tab
$3 = $3.trim().replaceAll("\\n", "\n").replaceAll("\\t", "\t");

// Get the file content's frontmatter
const fmMatch = content.match(/^(\n*---)(\n[\S\s]*\n)(\n*---\n)/);

// Is the new value multiline?
const isValueMultiline = $3.includes("\n") || $3[0] === "-";

// Determine what to add and where
let toAdd = { text: "", start: 0, end: 0 };

// If there isn't yet a frontmatter, create it along with the variable name/value pair
if (!fmMatch)
{
	// Define what to add
	toAdd.text = "---\n" + $2 + ": " + $3 + "\n---\n\n";
}

// If there IS a frontmatter, add the variable name/value pair to it
else
{
	// Find the variable in the frontmatter
	const varMatch =
		fmMatch[2].match("\n( *)(" + $2 + " *:)(\n|(?: |\t)[^\n]*\n)");

	// If the variable is NOT found append it to the frontmatter
	if (!varMatch)
	{
		toAdd.text = $2 + (
			isValueMultiline ?
			":\n  " + $3.replaceAll("\n", "\n  ") + "\n" :
			": " + $3 + "\n"
		);
		toAdd.start = fmMatch[1].length + fmMatch[2].length;
		toAdd.end = toAdd.start;
	}

	// If the variable IS found, modify it
	else
	{
		// Define value-entry
		const lineIndent = "\n" + varMatch[1] + "  ";
		toAdd.text =
			isValueMultiline ?
			lineIndent + $3.replaceAll("\n", lineIndent) :
			" " + $3;
		// Define start of definition
		toAdd.start =
			fmMatch[1].length + varMatch.index + varMatch[1].length +
			varMatch[2].length + 1;
		// Define end of definition
		let lineStart = toAdd.start;
		let lineEnd = content.indexOf("\n", toAdd.start + 1);
		if (varMatch[3].trim())
		{
			// Old definition is single-line
			toAdd.end = lineEnd;
		}
		else
		{
			// Old definition is multi-line
			let lineStartRegex = new RegExp("^" + varMatch[1] + "(?:- | )");
			while (lineEnd !== -1)
			{
				var line = content.slice(lineStart + 1, lineEnd);
				if (!line.match(lineStartRegex))
				{
					toAdd.end = lineStart;
					break;
				}
				lineStart = lineEnd;
				lineEnd = content.indexOf("\n", lineStart + 1);
			}
		}
	}
}

// Force the reading views to update (dataview isn't currently working for this)
if (_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed)
{
	const leaves = _inlineScripts.inlineScripts.HelperFncs.getLeavesForFile(file);
	if (leaves.length)
	{
		const onChanged = (changedFile) =>
		{
			// If the changed file is the one we're monitoring
			if (changedFile === file)
			{
				// Stop monitoring for file changes
				app.metadataCache.off("changed", onChanged);

				// Force all views of the file to re-render on the next frame
				setTimeout(() =>
				{
					for (const leaf of leaves)
					{
						leaf.view.modes.preview.rerender(true);
					}
				}, 100);
			}
		}
		app.metadataCache.on("changed", onChanged);
	}
}

// Modify the variable in the frontmatter of the note
await _inlineScripts.inlineScripts.HelperFncs.addToNote(toAdd.text, toAdd, file.path);

return expFormat(
	"Note __" + $1 + "__, variable __" + $2 + "__ set to __" + $3 + "__.");
```
__
notevars set {note name: path text} {variable name: name text} {value: text} - Sets the value of variable {variable name} to {value} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars setArray ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
// Get the input array
const valueArray = $3.split(",").map(v => v.trim());

// Call "notevars set" with the input array
const result =
	expand("notevars set " + $1 + " " + $2 + " " + "- " + valueArray.join("\\n- "));

// If error with "notevars set", return that error
if (!result[0]) { return result; }

return expFormat(
	"Note __" + $1 + "__, variable __" + $2 + "__ set to array __\\[ " +
	valueArray.join(", ") + " \\]__.");
```
__
notevars setArray {note name: path text} {array name: name text} {values: text (comma separated)} - Sets the values of array {array name} to {value1}, {value2}, etc. in note {note name}.  If {note name} is "." then it represents the current note.
