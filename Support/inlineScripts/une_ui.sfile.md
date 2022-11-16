---
obsidianUIMode: preview
---

An extension to __une.sfile__ that provides graphical ui versions of shortcuts.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

_inlineScripts.inlineScripts.HelperFncs.addCss("une_ui", ".iscript_une_select { height: unset; padding: .25em; margin-top: .5em; margin-bottom: 1.5em } .iscript_popup { max-height: unset !important; }");

// Custom popop definition for the 3 possible parameters used in uni shortcuts
confirmObjectPath("_inlineScripts.une_ui.uneInputPopup");//,
_inlineScripts.une_ui.uneInputPopup =
{
	// Setup function
	onOpen: async (data, parent, firstButton, SettingType) =>
	{
		// Randomness
		if (data.getRandomness)
		{
			const titleUi = document.createElement("div");
				titleUi.innerText = "Randomness";
				titleUi.classList.add("setting-item-name");
				parent.append(titleUi);
			const descriptionUi = document.createElement("div");
				descriptionUi.innerText =
					"Choose the randomness of the scene (for Power Level)";
				descriptionUi.classList.add("setting-item-description");
				parent.append(descriptionUi);
			data.randomnessUi = document.createElement("select");
				data.randomnessUi.innerHTML =
					"<option value='1'>Order</option>" +
					"<option value='2'>Calm</option>" +
					"<option value='3' selected>Standard</option>" +
					"<option value='4'>Disarray</option>" +
					"<option value='5'>Chaos</option>";
				data.randomnessUi.size = 5;
				data.randomnessUi.classList.add("iscript_une_select");
				data.randomnessUi.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
				    })
				parent.append(data.randomnessUi);
		}

		// Relationship
		if (data.getRelationship)
		{
			const titleUi = document.createElement("div");
				titleUi.innerText = "Relationship";
				titleUi.classList.add("setting-item-name");
				parent.append(titleUi);
			const descriptionUi = document.createElement("div");
				descriptionUi.innerText =
					"Choose the NPC's feelings towards the PC(s) (for mood)";
				descriptionUi.classList.add("setting-item-description");
				parent.append(descriptionUi);
			data.relationshipUi = document.createElement("select");
				data.relationshipUi.innerHTML =
					"<option value='1'>Loved</option>" +
					"<option value='2'>Friendly</option>" +
					"<option value='3'>Peaceful</option>" +
					"<option value='4' selected>Neutral</option>" +
					"<option value='5'>Distrustful</option>" +
					"<option value='6'>Hostile</option>" +
					"<option value='7'>Hated</option>";
				data.relationshipUi.size = 7;
				data.relationshipUi.classList.add("iscript_une_select");
				data.relationshipUi.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
				    })
				parent.append(data.relationshipUi);
		}

		// Demeanor
		if (data.getDemeanor)
		{
			const titleUi = document.createElement("div");
				titleUi.innerText = "Demeanor";
				titleUi.classList.add("setting-item-name");
				parent.append(titleUi);
			const descriptionUi = document.createElement("div");
				descriptionUi.innerText =
					"Choose the NPC's demeanor (for bearing)";
				descriptionUi.classList.add("setting-item-description");
				parent.append(descriptionUi);
			data.demeanorUi = document.createElement("select");
				data.demeanorUi.innerHTML =
					"<option value='0' selected>&lt;Random&gt;</option>" +
					"<option value='1'>Scheming</option>" +
					"<option value='2'>Insane</option>" +
					"<option value='3'>Friendly</option>" +
					"<option value='4'>Hostile</option>" +
					"<option value='5'>Inquisitive</option>" +
					"<option value='6'>Knowing</option>" +
					"<option value='7'>Mysterious</option>" +
					"<option value='8'>Prejudice</option>";
				data.demeanorUi.size = 9;
				data.demeanorUi.classList.add("iscript_une_select");
				data.demeanorUi.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
				    })
				parent.append(data.demeanorUi);
		}
	},

	// Shutown function
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Ok") { return; }

		// Setup and return results
		let result = {};
		if (data.getRandomness)
		{
			result.randomness = Number(data.randomnessUi.value);
		}
		if (data.getRelationship)
		{
			result.relationship = Number(data.relationshipUi.value);
		}
		if (data.getDemeanor)
		{
			result.demeanor = Number(data.demeanorUi.value);
		}
		resolveFnc(result);
	}
};//);
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
delete _inlineScripts.une_ui;

// Custom CSS removal
_inlineScripts.inlineScripts.HelperFncs.removeCss("une_ui");
```
__
Shuts down this shortcut-file


__
```
^ui une$
```
__
```js
// Get input from the custom popup for une parameters
const input = popups.custom(
	"UNE generator - Full",
	_inlineScripts.une_ui.uneInputPopup,
	{ getRandomness: true, getRelationship: true, getDemeanor: true });
if (!input) { return null; }

return expand(
	"une " + input.randomness + " " + input.relationship +
	(input.demeanor ? (" " + input.demeanor) : ""));
```
__
ui une - Asks the user to choose the scene's randomness (for picking the NPC's power).
Asks the user to choose the NPC's felings towards the PC(s) (for the NPC's mood).
Asks the user to choose the NPC's demeanor, or pick it randomly (for the NPC's bearing).
Displays the NPC's character (identity, power, motive) and the NPC's interaction for this scene (mood, bearing, focus).
***


__
```
^ui une character$
```
__
```js
// Get input from the custom popup for une parameters
const input = popups.custom(
	"UNE generator - Character",
	_inlineScripts.une_ui.uneInputPopup,
	{ getRandomness: true, getRelationship: false, getDemeanor: false });
if (!input) { return null; }

return expand("une character " + input.randomness);
```
__
ui une character - Asks the user to choose the scene's randomness (for picking the NPC's power).
Displays the NPC's character (identity, power, motive).


__
```
^ui une interact$
```
__
```js
// Get input from the custom popup for une parameters
const input = popups.custom(
	"UNE generator - Iteraction",
	_inlineScripts.une_ui.uneInputPopup,
	{ getRandomness: false, getRelationship: true, getDemeanor: true });
if (!input) { return null; }

return expand(
	"une interact " + input.relationship +
	(input.demeanor ? (" " + input.demeanor) : ""));
```
__
ui une interact - Asks the user to choose the NPC's feelings towards the PC(s) (for the NPC's mood).
Asks the user to choose the NPC's demeanor, or pick it randomly (for the NPC's bearing).
Displays the NPC's interaction for this scene (mood, bearing, focus).
***


__
```
^ui une identity$
```
__
```js
// Just wraps "une identity"
return expand("une identity");
```
__
ui une identity - Generates a 2-word description for an NPC.


__
```
^ui une power$
```
__
```js
// Get input from the custom popup for une parameters
const input = popups.custom(
	"UNE generator - Power",
	_inlineScripts.une_ui.uneInputPopup,
	{ getRandomness: true, getRelationship: false, getDemeanor: false });
if (!input) { return null; }

return expand("une power " + input.randomness);
```
__
ui une power - Asks the user to choose the scene's randomness.
Displays the NPC's power relative to the PC power(s).


__
```
^ui une motive$
```
__
```js
// Just wraps "une motive"
return expand("une motive");
```
__
ui une motive - Generates three 2-word descriptions for an NPC's motivations.
***


__
```
^ui une mood$
```
__
```js
// Get input from the custom popup for une parameters
const input = popups.custom(
	"UNE generator - Mood",
	_inlineScripts.une_ui.uneInputPopup,
	{ getRandomness: false, getRelationship: true, getDemeanor: false });
if (!input) { return null; }

return expand("une mood " + input.relationship);
```
__
ui une mood - Asks the user to choose the NPC's felings towards the PC(s).
Displays the NPC's mood for this scene.


__
```
^ui une bearing$
```
__
```js
// Get input from the custom popup for une parameters
const input = popups.custom(
	"UNE generator - Bearing",
	_inlineScripts.une_ui.uneInputPopup,
	{ getRandomness: false, getRelationship: false, getDemeanor: true });
if (!input) { return null; }

return expand("une bearing" + (input.demeanor ? (" " + input.demeanor) : ""));
```
__
ui une bearing - Asks the user to choose the NPC's demeanor, or pick it randomly.
Displays a the NPC's bearing.


__
```
^ui une focus$
```
__
```js
// Just wraps "une focus"
return expand("une focus");
```
__
ui une focus - Generates an NPC's primary interest for this interaction.