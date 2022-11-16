---
obsidianUIMode: preview
---

An extension to __lists.sfile__ that provides graphical ui versions of shortcuts.


__
```
^ui lists? reset$
```
__
```js
return expand("list reset");
```
__
ui lists reset - Clears all lists.
***


__
```
^ui lists?$
```
__
```js
// Get the list of list names, early out empty
let lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();
if (!lists.length)
{
	return expand("lists");
}

// Add "all lists" option
lists.unshift("<All lists>");

// Choose a list (or "all lists")
const pick = popups.pick("Choose a list to view", lists, 0, "adaptive");
if (pick === null) { return null; }

// return expansion of the list info (or ALL lists if pick is 0)
return expand("lists" + (pick ? (" " + lists[pick]) : ""));
```
__
ui lists - User chooses a list (options include "all lists"), then shows info on the chosen list.


__
```
^ui lists? rename$
```
__
```js
// Get the list of list names, early out if empty
let lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();
if (!lists.length)
{
	return expFormat("List not renamed.  No lists to rename.");
}

// Choose a list
const pick = popups.pick("Choose a list to rename", lists, 0, "adaptive");
if (pick === null) { return null; }

// Enter a new name, early out if not in a valid format
const newName =
	popups.input("Enter a new name for __" + lists[pick] + "__", lists[pick]);
if (newName === null) { return null; }
if (!newName.match(/^[_a-zA-Z][_a-zA-Z0-9]*$/))
{
	return expFormat(
		"List item not added.  List name __\"" + newName + "\"_ isn't valid.");
}

// If newName IS oldName, do nothing
if (newName === lists[pick]) { return null; }

return expand("lists rename " + lists[pick] + " " + newName);
```
__
ui lists rename - User chooses a list, and enters a new name for it.  The list is renamed.


__
```
^ui lists? removelist$
```
__
```js
// Get the list of list names, early out if empty
let lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();
if (!lists.length)
{
	return expFormat("List not removed.  No lists to remove.");
}

// Choose a list
const pick = popups.pick("Choose a list to remove", lists, 0, "adaptive");
if (pick === null) { return null; }

return expand("lists removelist " + lists[pick]);
```
__
ui lists removelist - User chooses a list, which is removed.


__
__
```js
// get the items of a given list, regardless of list type.
async function getListItems(name)
{
	return expand("lists listraw " + name);
}

// returns the names of lists that have items.
async function getNamesOfPopulatedLists()
{
	let result = Object.keys(_inlineScripts.state.sessionState.lists).sort();
	return await _inlineScripts.inlineScripts.HelperFncs.asyncFilter(
		result, async v => (await getListItems(v)).length);
};

// Filter list names to those of basic and combo type
function filterListsToBasicAndCombo(lists)
{
	return lists.filter(v =>
	{
		const type = _inlineScripts.state.sessionState.lists[v].type;
		return (type === "basic" || type === "combo");
	});
}
```
__
Helper scripts


__
```
^ui lists? pick$
```
__
```js
// Get the list of populated list's names, early out if empty
const lists = await getNamesOfPopulatedLists();
if (!lists.length)
{
	return expFormat("No item picked.  There are no non-empty lists.");
}

// Choose a list
const pick =
	popups.pick("Choose a list to randomly pick from", lists, 0, "adaptive");
if (pick === null) { return null; }

return expand("lists pick " + lists[pick]);
```
__
ui lists pick - User chooses a non-empty list, a random item is picked from that list and returned.
***


__
```
^ui lists? add$
```
__
```js
// Get the list of list names
let lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();

// Only include lists of basic and combo type
lists = filterListsToBasicAndCombo(lists);

// Choice defaults to 0 (which means creating a new list to add to)
let listName = "";
let listItems = [];

// If choices aren't empty, ask user to choose which list to add to
if (lists.length)
{
	// Add option for a new list
	lists.unshift("<New list>");

	// Choose a list
	const pick = popups.pick("Choose a list to add to", lists, 0, "adaptive");
	if (pick === null) { return null; }

	// If user's choice wasn't "new list", record the choice
	if (pick > 0)
	{
		listName = lists[pick];
		listItems = [...new Set(await getListItems(listName))];
	}
}

// If choice was "new list", OR there are no lists, get a name for the new list.
if (listName === "")
{
	// Enter name, early out if not in a valid format
	listName = popups.input("Enter a list to add to");
	if (!listName) { return null; }
	if (!listName.match(/^[_a-zA-Z][_a-zA-Z0-9]*$/))
	{
		return expFormat(
			"List item not added.  List name __\"" + listName + "\"_ isn't valid.");
	}
}

// Get the item to add to the chosen list
const item =
	popups.input("Type up an item to add to list <b>" + listName + "</b>", "",
		listItems);
if (!item) { return null; }

return expand("lists add " + listName + " " + item);
```
__
ui lists add - User chooses a list to add to and an item to add.  The item is added to the list.
    - Identical items within a list are allowed.
    - Can only add to (1) basic lists and (2) combo lists with at least one basic list as a sub-list.


__
```
^ui lists? replace$
```
__
```js
// Get the list of populated list's names
let lists = await getNamesOfPopulatedLists();

// Only include lists of basic and combo type, early out if empty
lists = filterListsToBasicAndCombo(lists);
if (!lists.length)
{
	return expFormat("No item replaced.  There are no non-empty lists.");
}

// Choose a list
const pick = popups.pick("Choose a list to replace items from", lists, 0, "adaptive");
if (pick === null) { return null; }

// Get items from the list
let items = await getListItems(lists[pick]);

// Count diplicate items
let itemCounts = {};
for (const item of items)
{
	itemCounts[item] = (itemCounts[item] || 0) + 1;
}

// Remove duplicate items
items = [...new Set(items)];
let itemLabels = [...items];

// Add count to items with duplicates
for (let i = 0; i < items.length; i++)
{
	const count = itemCounts[items[i]];
	itemLabels[i] = items[i] + (count <= 1 ? "" : "  (x " + count + ")");
}

// Choose an item
const pick2 = popups.pick("Choose an item to replace.", itemLabels, 0, "adaptive");
if (pick2 === null) { return null; }
const item = items[pick2];

// Eenter a new item text
let replacement =
	popups.input("Enter text to replace <b>\"" + item + "\"</b> with.", item);
if (!replacement) { return null; }

return expand("lists replace " + lists[pick] + " \"" + item + "\" " + replacement);
```
__
ui lists replace - User chooses a list, an item from the list and enters text to replace the item with.  All instances of the item are replaced with the text.
    - Can only replace from (1) basic lists and (2) combo lists that contain basic lists.


__
```
^ui lists? remove$
```
__
```js
// Get the list of populated list's names
let lists = await getNamesOfPopulatedLists();

// Only include lists of basic and combo type, early out if empty
lists = filterListsToBasicAndCombo(lists);
if (!lists.length)
{
	return expFormat("No item removed.  There are no non-empty lists.");
}

// Choose a list
const pick = popups.pick("Choose list to remove an item from", lists, 0, "adaptive");
if (pick === null) { return null; }

// Get items from the list
let items = await getListItems(lists[pick]);

// Remove duplicate items
items = [...new Set(items)];

// Choose an item from the list
const pick2 = popups.pick("Choose an item to remove", items, 0, "adaptive");
if (pick2 === null) { return null; }

return expand("lists remove " + lists[pick] + " " + items[pick2]);
```
__
ui lists remove - User choses a list and an item from the list.  The last instance of the item is removed from the list.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.
***


__
```
^ui lists? addfolder$
```
__
```js
// Enter a list name, early out if not in a valid format or unavailable
listName = popups.input("Enter a name for the new folder-list");
if (!listName) { return null; }
if (!listName.match(/^[_a-zA-Z][_a-zA-Z0-9]*$/))
{
	return expFormat(
		"Folder-List not added.  List name __\"" + listName + "\"_ isn't valid.");
}
if (_inlineScripts.state.sessionState.lists[listName])
{
	return expFormat(
		"Folder-list not added.  Name __\"" + listName + "\"__ unavailable.");
}

// Get a list of folders
const folders =
	Object.keys(app.vault.fileMap).filter(v => app.vault.fileMap[v].children);

// Choose a folder
const pick =
	popups.pick("Choose the folder to attach list <b>" + listName + "</b> to.",
	folders, 0, "adaptive");
if (pick === null) { return null; }

return expand("lists addfolder " + listName + " " + folders[pick]);
```
__
ui lists addfolder - Asks the user to type a name for the new folder-list.
Asks the user to choose a folder.
Creates the folder-list, attached to the folder.


__
```
^ui lists? addcombo$
```
__
```js
// Enter a list name, early out if not in a valid format or unavailable
listName = popups.input("Enter a name for the new combo-list");
if (!listName) { return null; }
if (!listName.match(/^[_a-zA-Z][_a-zA-Z0-9]*$/))
{
	return expFormat(
		"Combo-List not added.  List name __\"" + listName + "\"_ isn't valid.");
}
if (_inlineScripts.state.sessionState.lists[listName])
{
	return expFormat(
		"Combo-list not added.  Name __\"" + listName + "\"__ unavailable.");
}

// Get the list of list names
let lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();

// Get all lists to use as sub-lists
let subLists = [];
let noMoreMsg = "";
do
{
	// Show selected lists so far
	const listsSoFar =
		!subLists.length ? "" :
		"<br/><br/>Lists so far:</br>- " + subLists.join("<br/>- ") + "<br/><br/>";

	// Pick the next sub-list
	let pick =
		popups.pick("Choose a list to link as a sub-list" + noMoreMsg + listsSoFar,
		lists, 0, "adaptive");
	if (pick === null) { return null; }

	// After the first choice, add the option to finish the list of sub-lists
	if (!subLists.length)
	{
		lists.unshift("<No more lists>");
		pick++;
		noMoreMsg = "<br/>or choose \"&lt;No more lists&gt;\" if done linking";
	}

	// A choice of 0 means "No more lists" was chosen
	if (pick === 0)
	{
		break;
	}

	// Add the chosen list to the sub-lists
	subLists.push(lists[pick]);
}
while (true);

return expand("lists addcombo " + listName + " " + subLists.join(" "));
```
__
ui lists addcombo - User enters a name for he comb-list and chooses sub-lists.  A combo-list is made from the choices.
***


__
```
^ui lists? shortcutbatch$
```
__
```js
// Get the list of populated list's names, early out if empty
const lists = await getNamesOfPopulatedLists();
if (!lists.length)
{
	return expFormat("No shorcut batched.  There are no non-empty lists.");
}

// Choose a list
const pick = popups.pick("Choose a list for a batch shortut", lists, 0, "adaptive");
if (pick === null) { return null; }

// Enter the shortcut text
const batchShortcut =
	popups.input("Enter a shortcut-text.  Put \"%1\" where the list item should be.");

return expand("lists shortcutbatch " + lists[pick] + " " + batchShortcut);
```
__
ui lists shortcutbatch - User chooses a list and enters a shortcut-text.  The shortcut-text is run for each item in the list, with "%1" being replaced by the list item.


__
```
^ui lists? fromfile lines$
```
__
```js
// Get the list of list names
let lists = Object.keys(_inlineScripts.state.sessionState.lists).sort();

// Only include lists of basic and combo type
lists = filterListsToBasicAndCombo(lists);

// Choice defaults to 0 (which means creating a new list to add to)
let listName = "";

// If choices aren't empty, ask user to choose which list to add to
if (lists.length)
{
	// Add option for a new list
	lists.unshift("<New list>");

	// Choose a list
	const pick = popups.pick("Choose a list to add to", lists, 0, "adaptive");
	if (pick === null) { return null; }

	// If user's choice wasn't "new list", record the choice
	if (pick > 0) { listName = lists[pick]; }
}

// If choice was "new list", OR there are no lists, get a name for the new list.
if (listName === "")
{
	// Enter a new list name.  Early out if not in a valid format
	listName = popups.input("Enter a list to add to");
	if (!listName) { return null; }
	if (!listName.match(/^[_a-zA-Z][_a-zA-Z0-9]*$/))
	{
		return expFormat(
			"List item not added.  List name __\"" + listName + "\"_ isn't valid.");
	}
}

// Get a list of files
const filenames =
	Object.keys(app.vault.fileMap).filter(v => !app.vault.fileMap[v].children);

// Choose a file
const pick2 =
	popups.pick("Choose the file to load lines from.", filenames, 0, "adaptive");
if (pick2 === null) { return null; }

return expand("lists fromfile lines " + listName + " \"" + filenames[pick2] + "\"");
```
__
ui lists fromfile lines - Use chooses a list (or enters a new list) and chooses a file.  The file is broken into lines, which are added to the list as items.
