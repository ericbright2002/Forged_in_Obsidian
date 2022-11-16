Shortcuts for working with files in the vault.


__
```
^files extensionchange ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?([^ \t\\:*?"<>|]+|)$
```
__
```js
// File path parameter - remove quotes
$1 = $1.replaceAll(/^\"|\"$/g, "");

// Extension parameter - default to "md"
$2 ||= "md";

// Confirm file path parameter is valid
if (!app.vault.fileMap[$1])
{
	return expFormat("Extension not changed.  File __" + $1 + "__ not found.");
}

// Get the current extension position
let extensionIndex = $1.lastIndexOf(".");
if (extensionIndex == -1) { extensionIndex = $1.length; }

// Add new extension
const newPath = $1.slice(0, extensionIndex) + "." + $2;
app.vault.adapter.rename($1, newPath);

return expFormat("File __" + $1 + "__ is now __" + newPath + "__.");
```
__
files extensionchange {file name: path text} {extension: nospace text, default: "md"} - Changes the extension of file {file name} to {extension}.


__
```
^files? shortcutbatch ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) (.*)$
```
__
```js
// Remove quotes around path parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// Confirm that the path is a valid folder, early out if not
const folder = app.vault.fileMap[$1];
if (!folder)
{
	return expFormat("Extension not changed.  Folder __" + $1 + "__ not found.");
}
if (!folder.children)
{
	return expFormat("Extension not changed.  __" + $1 + "__ is not a folder.");
}

// Find an unused list name (to use "lists shortcutbatch" shortcut)
let tmpListName = 0;
while (_inlineScripts.state.sessionState.lists["L" + tmpListName])
{
	tmpList++;
}
tmpListName = "L" + tmpListName;

// Popuplate list with filenames
for (const child of folder.children)
{
	expand("lists add " + tmpListName + " " + child.path);
}

// Run shortcutbatch on the list
let result = expand("lists shortcutbatch " + tmpList + " " + $2);

// Fux the resulting expansion - "for folder" instead of "for list"
result = result.replace("for list __" + tmpList, "for folder __" + $1);

// Remove the list now that we're done with it
expand("lists removelist " + tmpListName);

// Return the result
return result;
```
__
files shortcutbatch {folder name: path text} {shortcut: text} - Runs shortcut {shortcut} once for each file in folder {folder name}, replacing "%1" in {shortcut} with the file's name.
