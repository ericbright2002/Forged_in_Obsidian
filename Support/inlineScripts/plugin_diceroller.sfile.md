A shortcut to run commands through the Dice Roller plugin.

__
```
^diceroller (.+)$
```
__
```js
// Get the diceroller plugin (or exit if not available)
const diceRollerPlugin = app.plugins.getPlugin("obsidian-dice-roller");
if (!diceRollerPlugin)
{
	return expText("Command not run.  Dice Roller plugin not available.");
}

// Roll the command parameter with the plugin
const diceRoller = await diceRollerPlugin.getRoller($1);
return await diceRoller.roll();
```
__
diceroller {command: text} - Runs {command} through the Dice Roller plugin and expands to the result.
