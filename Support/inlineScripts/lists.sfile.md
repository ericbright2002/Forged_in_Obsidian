---
obsidianUIMode: preview
---

Shortcuts for working with lists.

This shortcut-file has a tutorial video available:
- [Using the "list" shortcut-file to manage lists](https://www.youtube.com/watch?v=xIYpnBKdYRg) (runtime 5:34)

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the lists.

This shortcut-file has supplementary shortcut files:
__lists_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Check that the state is initialized
confirmObjectPath("_inlineScripts.state.sessionState.lists");

// Event callback for state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.lists",
	function()
	{
		expand("lists reset noconfirm");
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
//Event callback removal
delete _inlineScripts?.state?.listeners?.onReset?.lists;

// State removal
delete _inlineScripts?.state?.sessionState?.lists;
```
__
Shuts down this shortcut-file


__
```
^lists? reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Lists</b> system")) { return null; }

// Reset
expand("lists reset noconfirm");

return expFormat("All lists cleared.");
```
__
lists reset - Clears all lists.
***


__
```
^lists? reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Confirm that the state is initialized
confirmObjectPath("_inlineScripts.state.sessionState");

// Reset lists
_inlineScripts.state.sessionState.lists = {};
```
__
hidden - No-confirm reset


__
__
```js
function caseInsensitiveSort(lhs, rhs)
{
	return lhs.toLowerCase().localeCompare(rhs.toLowerCase());
}

// a function to get the items of a given list, for all possible list types.
function getListItems(name)
{
	// The list to get items for
	let list = _inlineScripts.state.sessionState.lists[name];
	if (!list) { return []; }

	// Handle different list types
	switch (list.type)
	{
		// basic type - contain their items in their "content" property
		case "basic":
			return list.content.sort(caseInsensitiveSort);
			break;
		// folder type - items are the files in the associated folder ("content" prop)
		case "folder":
			// Get folder and confirm it's valid
			const targetFile = app.fileManager.vault.fileMap[list.content];
			if (!targetFile || !targetFile.children)
			{
				return [];
			}
			// Return links to folder children that are notes
			return [... targetFile.children ]
				.filter(v => !v.children)
				.filter(v => v.name.endsWith(".md"))
				.map(v => "[[" + v.basename + "]]")
				.sort(caseInsensitiveSort);
		// combo type - items are from the sub-lists of this combo ("content" prop)
		case "combo":
		{
			let result = [];
			// Iterate over lists and add items from each
			for (const sublist of list.content)
			{
				result = result.concat(getListItems(sublist));
			}
			// Sort and return the resulting list
			return result.sort(caseInsensitiveSort);
		}
	}
}
```
__
Helper scripts


__
```
^lists?$
```
__
```js
// Get all lists names
const lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();

// Start the expansion
let result = "Lists:";

// Early out with the result if no lists
if (!lists.length)
{
	return expFormat(result + "\n- NONE");
}

// Add each list's individual expansion to the final expansion
for (let i = 0; i < lists.length; i++)
{
	// Individual list's expansion (but without the header, and as a single string)
	const listExpansion = expand("lists " + lists[i]).slice(1).join("");
	// Unformat the individual list expansion, replace period-bullets with indented
	// dash-bullets, then add it to the final expansion.
	result +=
		"\n- " + expUnformat(listExpansion).replaceAll("\n. ", "\n    - ");
}

return expFormat(result);
```
__
lists - Shows all lists and their info: type, associations and items.


__
```
^lists? ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Create items list, or "NONE" if no items
let itemText = getListItems($1);
itemText = (itemText?.length ? (". " + itemText.join("\n. ")) : ". NONE");

// Create the type info.  If list isn't registered, type defaults to basic.
let typeText = _inlineScripts.state.sessionState.lists[$1]?.type || "basic";
// No type info for basic types
if (typeText === "basic")
{
	typeText = "";
}
// Non-basic types show the list's type and associations.  For folder-list,
// association is the folder.  For combo-list, association is the sub-lists.
else
{
	const associations = _inlineScripts.state.sessionState.lists[$1].content;
	typeText = "    _(" + typeText + ": " + associations + ")_";
}

return expFormat([ "List ", "__" + $1 + "__" + typeText + ":\n", itemText, "" ]);
```
__
lists {list name: name text} - Shows all info for the single list {list name}: type, associations and items.


__
```
^lists? rename ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Confirm that old list name is valid and new list name is NOT valid
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat("List not renamed.  List __" + $1 + "__ not found.");
}
if (_inlineScripts.state.sessionState.lists[$2])
{
	return expFormat("List not renamed.  List name __" + $2 + "__ is already used.");
}

// Copy the list to the new list name
_inlineScripts.state.sessionState.lists[$2] = _inlineScripts.state.sessionState.lists[$1];

// Remove the old list name
delete _inlineScripts.state.sessionState.lists[$1];

return expFormat("List __" + $1 + "__ renamed to __" + $2 + "__.");
```
__
lists rename {original list name: name text} {new list name: name text} - Changes the name of list {original list name} to {new list name}.


__
```
^lists? removelist ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Error out if specified list doesn't exist
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat("List __" + $1 + "__ not removed.  List does not exist.");
}

// Remove the list
delete _inlineScripts.state.sessionState.lists[$1];

return expFormat("List __" + $1 + "__ removed.");
```
__
lists removelist {list name: name text} - Removes the entire list {list name}.


__
```
^lists? pick ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Helper function - Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Get the number of items.  Early out if no items
let itemCount = getListItems($1).length;
if (itemCount === 0)
{
	return expFormat([ "Failed to pick from list __" + $1 + "__.  List is empty." ]);
}

// Use the unlisted pick shortcut, which takes the index to pick.  The unlisted
// version is used to handle the randomness externally.  It is used by the
// adventurecrafter shortcut-file.
return expand("lists pick " + $1 + " " + roll(itemCount));
```
__
lists pick {list name: name text} - Gets a random item from the list {list name}.


__
```
^lists? pick ([_a-zA-Z][_a-zA-Z0-9]*) ([1-9][0-9]*)$
```
__
```js
// Convert $2 from 1-index to 0-index
$2 = Number($2) - 1;

// Generic expansion messages
const ERROR_MSG_PREFIX = "Item not picked from list __" + $1 + "__.  ";

// Get the list's items.  Early out if parameter is out-of-bounds
let items = getListItems($1);
if ($2 >= items.length)
{
	return expFormat(
		[ ERROR_MSG_PREFIX + "The item index " + $2 + " is out of range." ]);
}

// Return expansion with the picked item
return expFormat([ "__", items[$2], "__ picked from list __", $1, "__." ]);
```
__
hidden - lists pick {list name: name text} {item index: >0} - Gets the item number {item index} from the list {list name}.
***


__
```
^lists? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
// If the given list isn't yet defined, auto-define it as a basic list.
_inlineScripts.state.sessionState.lists[$1] ||= { type: "basic", content: [] };

// Generic expansion messages
const SUCCESS_MSG = "Item __" + $2 + "__ added to list __" + $1 + "__.";
const ERROR_MSG_PREFIX = "Item __" + $2 + "__ not added to list __" + $1 + "__.  ";

// Add is handled differently for different list types
switch (_inlineScripts.state.sessionState.lists[$1].type)
{
	// Basic type - Just append the item
	case "basic":
		_inlineScripts.state.sessionState.lists[$1].content.push($2);
		return expFormat([ SUCCESS_MSG ]);
	// Combo type - Append the item to the last possible sub-list
	case "combo":
	{
		// Iterate sub-lists IN REVERSE ORDER, to add to the LAST possible sublist
		const sublists = _inlineScripts.state.sessionState.lists[$1].content;
		for (let i = sublists.length-1; i >= 0; i--)
		{
			// Try adding
			const result = expand("lists add " + sublists[i] + " " + $2);
			// if add was successful (signified by size 1), expand success message
			if (result.length === 1)
			{
				return expFormat([ SUCCESS_MSG ]);
			}
		}
		// If failed to add to any sub-list, expand error message
		return expFormat([ ERROR_MSG_PREFIX, "No sub-lists support adding." ]);
	}
	// Any other type doesn't support adding
	default:
		return expFormat([ ERROR_MSG_PREFIX, "List-type doesn't support adding." ]);
}
```
__
lists add {list name: name text} {item: text} - Adds {item} to the list {list name}.  Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists which contain basic lists.


__
```
^lists? replace ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ ].*"|[^ ]+) (.+)$
```
__
```js
// Remove any quotes around the old item
$2 = $2.replaceAll(/^\"|\"$/g, "");

// Generic expansion messages
const SUCCESS_MSG =
	"All __" + $2 + "__ replaced with __" + $3 + "__ in list __" + $1 + "__.";
const ERROR_MSG_PREFIX = "Item __" + $2 + "__ not replaced in list __" + $1 + "__.  ";

// Confirm that the list exists
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat(ERROR_MSG_PREFIX + "List __" + $1 + "__ not found.");
}

// Relacement is handled differently for different list types
switch (_inlineScripts.state.sessionState.lists[$1].type)
{
	// Basic type - iterate over items, replacing any matches
	case "basic":
	{
		const items = _inlineScripts.state.sessionState.lists[$1].content;
		for (let i = 0; i < items.length; i++)
		{
			if (items[i] === $2)
			{
				items[i] = $3;
			}
		}
		return expFormat(SUCCESS_MSG);
	}
	// combo type - iterate over sub-lists, running the replacement for each
	case "combo":
	{
		const sublists = _inlineScripts.state.sessionState.lists[$1].content;
		for (const sublist of sublists)
		{
			expand("lists replace " + sublist + " " + $2 + " " + $3);
		}
		return expFormat(SUCCESS_MSG);
	}
	// Any other type doesn't support replacement
	default:
		return expFormat(ERROR_MSG_PREFIX + "List-type doesn't support replacement.");
}
```
__
lists replace {list name: name text} {item: text} {replacement: text} - Replaces all instances of {item} with {replacement}.  {item} must be quoted if it has any space.


__
```
^lists? remove ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
// Generic expansion messages
const SUCCESS_MSG = "__" + $2 + "__ removed from list __" + $1 + "__.";
const ERROR_MSG_PREFIX =
	"Item __" + $2 + "__ not removed from list __" + $1 + "__.  ";

// Confirm that the list exists
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat(ERROR_MSG_PREFIX + "List not found.");
}

// Remove is handled differently for different list types
switch (_inlineScripts.state.sessionState.lists[$1].type)
{
	// Basic type - Iterate over items backwards, removing the last instance found
	case "basic":
	{
		const items = _inlineScripts.state.sessionState.lists[$1].content;
		for (let i = items.length-1; i >= 0; i--)
		{
			if (items[i] === $2)
			{
				items.splice(i, 1);
				return expFormat([ SUCCESS_MSG ]);
			}
		}
		// If we get here, no items were removed
		return expFormat([ ERROR_MSG_PREFIX, "Item not found." ]);
	}
	// Combo type - Iterate over sub-items backwards, running remove until successful.
	case "combo":
	{
		const sublists =  _inlineScripts.state.sessionState.lists[$1].content;
		for (let i = sublists.length-1; i >= 0; i--)
		{
			const result = expand("lists remove " + sublists[i] + " " + $2);
			if (result.length === 1)
			{
				return expFormat([ SUCCESS_MSG ]);
			}
		}
		// If we get here, no items were removed
		return expFormat([ ERROR_MSG_PREFIX, "Unable to remove from any sub-list." ]);
	}
	// Any other type doesn't support removal
	default:
		return expFormat([ ERROR_MSG_PREFIX, "List type doesn't support removal." ]);
}
```
__
lists remove {list name: name text} {item: text} - Removes an instance of {item} from the list {list name}.
    - Can only remove from (1) basic lists and (2) combo lists which contain basic lists.
***


__
```
^lists? addfolder ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
// Remove any quotes around the specified folder path
$2 = $2.replaceAll(/^\"|\"$/g, "");

// Confirm the list name is available
if (_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat("File-list not created.  Name __\"" + $1 + "\"__ unavailable.");
}

// Create the folder-list
_inlineScripts.state.sessionState.lists[$1] = { type: "folder", content: $2 };

return expFormat("Folder-list __" +$1 + "__ added, linked to __\"" + $2 + "\"__.");
```
__
lists addfolder {list name: name text} {folder: path text} - Creates a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.


__
```
^lists? addcombo ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][ _a-zA-Z0-9]*)$
```
__
```js
// Turn the sub-lists parameter into an array of (non-empty) names
let sublists = $2.split(" ").filter(v => v);

// Confirm the list name is available
if (_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat("Combo-list not created.  Name __\"" + $1 + "\"__ unavailable.");
}

// Create the combo-list
_inlineScripts.state.sessionState.lists[$1] = { type: "combo", content: sublists };

return expFormat(
	"Combo-list __" + $1 + "__ added, linked to:\n. " + sublists.join("\n. "));
```
__
lists addcombo {list name: name text} {sub-list: name text separated by spaces} - Creates a combo-list named {list name} that is linked to the lists given in {sub-list}.  A "combo-list" is a list who's items are all of the items of its linked sub-lists.
***


__
```
^lists? shortcutbatch ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
// Check that the specified list is valid
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return expFormat("Shortcut batch not run.  List __" + $1 + "__ not found.");
}

// Begin the expansion
let result = "Shortcut batch for list __" + $1 + "__...\n\n";

// Run the shortcut once for each item in the list, adding the result to the expansion
for (const item of getListItems($1))
{
	result += expUnformat( expand($2.replaceAll("%1", item)) ) + "\n\n";
}

// End and return the expansion
return expFormat(result + "... shortcut batch finished.");
```
__
lists shortcutbatch {list name: name text} {shortcut: text} - Runs shortcut {shortcut} once for each item in list {list name}, replacing "%1" in {shortcut} with the item.


__
```
^lists? fromfile lines ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
// Remove any quotes around the file path
$2 = $2.replaceAll(/^\"|\"$/g, "");

// Get the specified file
let file = app.vault.fileMap[$2];

// If file isn't valid, try adding an "md" extension
if (!file || file.children)
{
	file = app.vault.fileMap[$2 + ".md"];
}

// If file still isn't valid, give up
if (!file)
{
    return expFormat("Lines not added.  File __" + $2 + "__ not found.");
}
if (file.children)
{
    return expFormat("Lines not added.  __" + $2 + "__ is a folder.");
}

// Get the file's lines
const content = await app.vault.cachedRead(file);
const lines = content.split("\n").filter(v => v);

// Add the file's lines
for (const line of lines)
{
	const result = expand("lists add " + $1 + " " + line);
	if (result.length > 1)
	{
		return expFormat(
			"Lines not added.  Type of list __" + $1 + "__ can't be added to.");
	}
}

return expFormat("__" + lines.length + "__ items added to list __" + $1 + "__.");
```
__
lists fromfile lines {list name: name text} {file: path text} - Takes the file {file}, breaks it up into individual lines and adds each of those lines, as items, to list {list name}.


__
```
^lists? listraw ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Return the array of items directly
return getListItems($1);
```
__
hidden - Returns an array of the items in a list without formatting.  Useful internally (as a sub-shortcut).


__
```
^lists? type ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Get the specified list
const list = inlineScripts.state.sessionState.lists[$1];

// If list doesn't exist, return type of "none"
if (!list) { return "none"; }

// Return the list's type
return list.type;
```
__
hidden - gets the type of the list named {list name}.  Useful internally (as a sub-shortcut).
