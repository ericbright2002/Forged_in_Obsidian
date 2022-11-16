---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts made for Starforged.

__
^test
__
```js
let options = ["Troublesome", "Dangerous", "Formidable", "Extreme", "Epic"];
var fileName = popups.input("What is the filename of the file to create?", "Progress1");
_inlineScripts.inlineScripts.HelperFncs.fileWrite("Progress/" + fileName + ".md", "");
var name = popups.input("What is the name of the vow, bond, or progress track?", "Find the lost relic");
var difficulty = popups.pick("What is the difficulty of the vow, bond, or progress track?", options, 1);
expand("notevars set Progress/" + fileName + " Name " + name);
expand("notevars set Progress/" + fileName + " Difficulty " + options[difficulty]);
return "You create a progress track for " + name + " of difficulty level " + options[difficulty];
```
__
test - tests