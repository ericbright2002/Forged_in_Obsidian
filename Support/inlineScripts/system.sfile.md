Shortcuts to work with the system: Obsidian, JS, InlineScripts backend, etc.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Setup session state
confirmObjectPath("_inlineScripts.system.lastError", "NONE");

// Event callback - inlineScripts.onError
confirmObjectPath(
	"_inlineScripts.inlineScripts.listeners.onError.system",
	(errorMessage) =>
	{
		_inlineScripts.system.lastError = errorMessage;
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
delete _inlineScripts.inlineScripts?.listeners?.onError?.system;

// Session state removal
delete _inlineScripts.system;
```
__
Shuts down this shortcut-file


__
```
^sys lasterror$
```
__
```js
return expFormat(
	"Last error:\n> " + _inlineScripts.system.lastError.replaceAll("\n", "\n> "));
```
__
sys lasterror - Expands to the last expansion error that was posted to the console.  This does not include errors that were created by a shortcut, only those created by __Inline Scripts__ itself, i.e. those the trigger the message: `ERROR: Shortcut expansion issues. (see console for details)`.


__
```
^sys runjs (.*)$
```
__
```js
return eval($1);
```
__
sys runjs {code: text} - Run the javascript in {code}.  Can run multiple statements separated with newline characters (`\\n`).


__
```
^sys triggererror$
```
__
```js
bad code here!
```
__
sys triggererror - Create an expansion error.  Useful for testing the `sys lasterror` shortcut.
