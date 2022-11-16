---
obsidianUIMode: preview
---

Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the clips.

This shortcut-file has supplementary shortcut files:
__clips_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Make sure state and "pirorExpansion" value are setup
confirmObjectPath("_inlineScripts.state.sessionState.clips");
confirmObjectPath("_inlineScripts.clips.priorExpansion", "");

// Event callback - state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.clips",
	function()
	{
		expand("clips reset noconfirm");
	});

// Event callback - inlineScripts.onExapsion
confirmObjectPath(
	"_inlineScripts.inlineScripts.listeners.onExpansion.clips",
	(expansionInfo) =>
	{
		_inlineScripts.clips.priorExpansion =
			expansionInfo.expansionText;
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
delete _inlineScripts.inlineScripts?.listeners?.onExpansion?.clips;

// State removal
delete _inlineScripts.state?.listeners?.onReset?.clips;
```
__
Shuts down this shortcut-file


__
```
^clips reset?$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Clips</b> system")) { return null; }

// Reset
expand("clips reset noconfirm");

return expFormat("All clips cleared.");
```
__
clips reset - Removes all clips.
***


__
```
^clips? reset noconfirm$
```
__
```js
// Reset state object
_inlineScripts.state.sessionState.clips = {};
```
__
hidden - No-confirm reset


__
```
^clips?$
```
__
```js
// Expand to a list of clip names
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips);
const result = "Clips:\n. " + (clipNames.length ? clipNames.join("\n. ") : "NONE");

return expFormat(result);
```
__
clips - Lists all stored clips.


__
```
^clips? set ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
// Make clip titles case-insensitive
$1 = $1.toLowerCase();

// Assign the clip contents to the clip name
_inlineScripts.state.sessionState.clips[$1] = $2;

return expFormat("Clip __" + $1 + "__ set to:\n" + $2);
```
__
clips set {name: name text} {value: text} - Creates / Sets a clip named {name} to the string {value}.


__
```
^(?:clips? get|cg) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Make clip titles case-insensitive
$1 = $1.toLowerCase();

// Expand to the text associated with the clip name (or empty string for invalid name)
return _inlineScripts.state.sessionState.clips[$1] || "";
```
__
clips get {name: name text} - Expands to the value stored in clip {name}.
	- Alternative: __cg {name: name text}__


__
```
^clips? expansion ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Make clip titles case-insensitive
$1 = $1.toLowerCase();

// Assign prior expansion text to the clip name
_inlineScripts.state.sessionState.clips[$1] = _inlineScripts.clips.priorExpansion;

return expFormat(
	"Clip __" + $1 + "__ set to:\n" + _inlineScripts.clips.priorExpansion);
```
__
clips expansion {name: name text} - Creates a clip named {name} that stores the previous expansion.


__
```
^clips? remove ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Make clip titles case-insensitive
$1 = $1.toLowerCase();

// If clip exists, remove it and notify user
if (_inlineScripts.state.sessionState.clips[$1])
{
	delete _inlineScripts.state.sessionState.clips[$1];
	return expFormat("Clip __" + $1 + "__ removed.");
}

// If clip doesn't exist, just notify user
else
{
	return expFormat("Clip __" + $1 + "__ not removed.  Does not exist.");
}
```
__
clips remove {name: name text} - Removes the clip named {name}.
