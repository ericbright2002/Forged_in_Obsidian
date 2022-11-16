---
obsidianUIMode: preview
---

This shortcut-file works with the state of many other shortcut-files.  Notably, it automatically maintains their state between Obsidian sessions.  It also includes shortcuts to allow you to save, load and reset states.

This shortcut file can be used by other shortcut-files to let them setup their own state that persists across Obsidian sessions.

This shortcut-file has 2 tutorial videos available.  The first video is the best for most users, but if you are writing shortcuts, then the second video shows you how to setup your own shortcut-file to use the state system:
- [Using the "state" shortcut-file to save and load session state](https://www.youtube.com/watch?v=IiRHB0FfJcI) (runtime 2:45)
- [Setup a shortcut-file for saving & loading with the "state" shortcut-file](https://www.youtube.com/watch?v=K-6Xy3YLuh4) (runtime 6:12)


To save the session state, use the "state get" shortcut.  This expands into a "state-string"- a string which includes all data in the current session state.  You can save this state-string simply by leaving it as part of the note.  To load the session state, call the "state set" shortcut, passing in a state-string _(created earlier with the "state get" shortcut)_.

Alternately, "state set last" will load the state-string from the last call to the "state get" shortcut in the current document.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Prevent running this shortcut multiple times concurrently.  There are race-
// condition in the file handling.
if (_inlineScripts?.state?.inSetup) { return; }
confirmObjectPath("_inlineScripts.state.inSetup", true);

// The name of the state data-file - the file to store the state in
const STATE_FILE_NAME = "Ξ_state.data.md";

// Helper function - Remomve file without triggering an exception if it doesn't exist
async function removeFile(filename)
{
	if (await app.vault.adapter.exists(filename))
	{
		await app.vault.adapter.remove(filename);
	}
}

// Setup the event callback arrays
confirmObjectPath("_inlineScripts.state.listeners.onReset");
confirmObjectPath("_inlineScripts.state.listeners.onLoad");

// Determine root folder for state data-file (every time, in case sfiles are moved)
confirmObjectPath("_inlineScripts.state.stateFile");
let stateFileHasBeenSet = false;
// Find the "state" sfile in the sfile's list...
for (const sfile of _inlineScripts.inlineScripts.plugin.settings.shortcutFiles)
{
	// Found the "state" sfile!
	if (sfile.address.endsWith("state.sfile.md"))
	{
		// Make the state data-file be stored right next to the state sfile
		const path = sfile.address.slice(0, -14) + STATE_FILE_NAME;
		// If the new path is different, change it and remember that it's been changed
		if (_inlineScripts.state.stateFile.path != path)
		{
			_inlineScripts.state.stateFile.path = path;
			stateFileHasBeenSet = true;
		}
		break;
	}
}

// Handle old versions of state data-file locations by finding the latest one and
// copying it to the new state data-file.  Then remove all existing old version files.
// NOTE - this block is to upgrade old vaults to the latest state system version.
if (!_inlineScripts.state.sessionState)
{
	const oldStateFilePossibilities =
	[
		".obsidian/plugins/obsidian-text-expander-js/state.data.txt",
		".obsidian/plugins/obsidian-text-expander-js/state_onquit.data.txt",
		".obsidian/plugins/obsidian-text-expander-js/state_auto.data.txt"
	];
	let oldStateFiles = [];
	for (let i = oldStateFilePossibilities.length - 1; i >= 0; i--)
	{
		if (await app.vault.adapter.exists(oldStateFilePossibilities[i]))
		{
			oldStateFiles.push(oldStateFilePossibilities[i]);
		}
	}
	if (oldStateFiles.length > 1)
	{
		let times = [];
		for (let i = 0; i < oldStateFiles.length; i++)
		{
			times.push((await app.vault.adapter.stat(oldStateFiles[i])).mtime);
		}
		let latest = 0;
		let latestTime = times[0];
		for (let i = 1; i < oldStateFiles.length; i++)
		{
			if (times[i] > latestTime)
			{
				latest = i;
				latestTime = times[i];
			}
		}
		oldStateFiles = [ oldStateFiles[latest] ];
	}
	if (oldStateFiles.length == 1)
	{
		// Remove current state file (if it exists)
		await removeFile(_inlineScripts.state.stateFile.path);
		// Copy the latest old state file into the current state file
		await app.vault.adapter.copy(
			oldStateFiles[0],
			_inlineScripts.state.stateFile.path);
		stateFileHasBeenSet = true;
		// Delete oldStateFilePossibilities
		for (const oldStateFilePossibility of oldStateFilePossibilities)
		{
			await removeFile(oldStateFilePossibility);
		}
	}
}

// Load the state file if running initial setup, or if the state file has been moved
if (!_inlineScripts.state.sessionState || stateFileHasBeenSet)
{
	try
	{
		// Get the raw content
		const stateString =
			await app.vault.adapter.read(_inlineScripts.state.stateFile.path);

		// Parse the content into the state
		_inlineScripts.state.sessionState = JSON.parse(stateString);

		// Assign raw content to the "priorString" to recognize when state has changed
		_inlineScripts.state.stateFile.priorString = stateString;

		// Call event callbacks for the state.onLoad event
		_inlineScripts.inlineScripts.HelperFncs.callEventListenerCollection(
			"state.onLoad", _inlineScripts.state.listeners.onLoad);
	}
	catch (e)
	{
		// If parsing failed, create a console error
		if (e.code !== "ENOENT")
		{
			console.error(
				"state.sfile\n\tUnable to load from state file:\n\t" +
				_inlineScripts.state.stateFile.path + "\n\t", e);
		}
		// Make sure we have SOMETHING for the state
		_inlineScripts.state.sessionState ||= {};
		// Trigger a state save to make sure the state data-file is up to date
		expand("state save");
	}
}

// Event callback - inlineScripts.onExpansion - React to each user-triggered shortcut
// expansion by checking for state changes and re-saving if there are any.
confirmObjectPath(
	"_inlineScripts.inlineScripts.listeners.onExpansion.state",
	async (expansionInfo) =>
	{
		// Only re-save if the expansion is user-triggered
		if (expansionInfo.isUserTriggered)
		{
			// Check for state changes and re-save if there are any.
			expand("state save");
		}
	});

// End the block to running this shortcut multiple times concurrently.
_inlineScripts.state.inSetup = false;
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
// State removal
delete _inlineScripts.state;
```
__
Shuts down this shortcut-file


__
```
^state reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>State</b> system")) { return null; }
if (!popups.confirm(
    "This will clear the state for <b>all shortcut files</b>!<br/>Are you sure?"))
{
	return null;
}

// Wipe out all states
_inlineScripts.state.sessionState = {};

// Notify listeners of state.onReset event
_inlineScripts.inlineScripts.HelperFncs.callEventListenerCollection(
	"state.onReset", _inlineScripts.state.listeners.onReset);

return expFormat("All state cleared.");
```
__
state reset - Clears all session state.


__
__
```js
// The part of the "state get" expansion that is not the state data-string.
// Used in "state get" and "state restore" shortcuts.
const GET_MSG_PREFIX = "State:\n";
```
__
Helper script


__
```
^state get$
```
__
```js
// Return the state data-string.
// NOTE: don't use the expansion-format's prefix or line-prefix.  This makes the
// data-string is easier to select and copy in the note.
return expFormat(
	GET_MSG_PREFIX + JSON.stringify(_inlineScripts.state.sessionState), true, true);
```
__
state get - Expands to a state-string - a string containing all data for the current session state.


__
```
^state restore$
```
__
```js
// Get the content of the current note
const content = await app.vault.cachedRead( app.workspace.getActiveFile());

// Find the last expansion of "state get" in the content.  Early out if no expansions
const getMsgIndex = content.lastIndexOf(GET_MSG_PREFIX);
if (getMsgIndex < 0)
{
	return expFormat("State not loaded.  Last state not found.");
}

// Get the end index of the data-string in the content.  Early out if endIndex failed
const endIndex = content.indexOf("\n", getMsgIndex + GET_MSG_PREFIX.length);
if (endIndex < 0)
{
	return expFormat("State not loaded.  Last state has a bad format.");
}

// Get the data-String from the content.
const stateString = content.slice(getMsgIndex + GET_MSG_PREFIX.length, endIndex);

// Load the data-string into the state.
return expand("state set " + stateString);
```
__
state restore - Loads the session state from the last "state get" shortcut expansion in the current note.


__
```
^state set (.+)$
```
__
```js
// Try to parse the given data-string
try
{
	// If data-string parsed, assign to the state
	_inlineScripts.state.sessionState = JSON.parse($1);
}
catch (e)
{
	// Notify the user of the failure to parse
	return expFormat([ "", "State not loaded. Invalid state:\n" + $1 ]);
}

// Call event callbacks for the state.onLoad event
_inlineScripts.inlineScripts.HelperFncs.callEventListenerCollection(
	"state.onLoad", _inlineScripts.state.listeners.onLoad);

return expFormat([ State set." ]);
```
__
state set {state: text} - Loads the session state from {state}, a state-string created with the "state get" shortcut.


__
```
^state save$
```
__
```js
// Get the current state's data-string.
const stateString = JSON.stringify(_inlineScripts.state.sessionState);

// If the data-string hasn't changed, do nothing
if (stateString === _inlineScripts.state.stateFile.priorString)
{
	return expFormat("State unchanged since prior save");
}

// Write the data-string to the state data-file
await app.vault.adapter.write(
	_inlineScripts.state.stateFile.path, stateString);

// Remember the new data-string to check for future changes
_inlineScripts.state.stateFile.priorString = stateString;

return expFormat("State saved.");
```
__
state save - Store the state to file.  This is called automatically after each shortcut expansion that triggers a state change.
