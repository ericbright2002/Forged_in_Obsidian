An extension to __cards.sfile__ that provides graphical ui versions of shortcuts.

This shortcut-file has a tutorial video available:
[Using the "cards" shortcut-file to use virtual cards](https://www.youtube.com/watch?v=KkpjTL2UvtQ) (runtime 12:43)


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Helper function - Turn relative path into absolute path based in the vault's root
function getAbsolutePath(path)
{
	if (path.startsWith("data:image")) { return path; }
	path = app.vault.fileMap[path];
	if (!path) { return ""; }
	return app.vault.getResourcePath(path);
}

// Helper function - Get the current back-image, url
function getBackImage()
{
	return _inlineScripts.state.sessionState.cards.backImage ||
	       _inlineScripts.cards.defaultBackImage;
}

// Custom popop definition for various card manipulations
confirmObjectPath("_inlineScripts.cards_ui.manipulateCardsPopup",
{
	// Setup function
	onOpen: async (data, parent, firstButton, SettingType) =>
	{
		data.pileNames = Object.keys(_inlineScripts.state.sessionState.cards.piles);

		// Source pile dropdown
		new SettingType(parent)
			.setName(
				data.defaults?.dst === undefined ? "Card-pile" : "Source card-pile")
			.setDesc(data.descriptions?.src)
			.addDropdown(dropdown =>
			{
				dropdown.addOptions(data.pileNames);
				dropdown.setValue(
					data.defaults?.src ?
					data.pileNames.indexOf(data.defaults?.src) :
					0);
				data.src = dropdown;
				dropdown.selectEl.addEventListener("keypress", e =>
				{
					if (e.key === "Enter") { firstButton.click(); }
				});
				return dropdown;
			});

		// Destination pile dropdown
		if (data.defaults?.dst !== undefined)
		{
			new SettingType(parent)
				.setName("Destination Card-pile")
				.setDesc(data.descriptions?.dst)
				.addDropdown(dropdown =>
				{
					dropdown.addOptions(data.pileNames);
					dropdown.setValue(
						data.defaults?.dst ?
						data.pileNames.indexOf(data.defaults?.dst) :
						0);
					data.dst = dropdown;
					dropdown.selectEl.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
				    });
					return dropdown;
				});
		}

		// Count textbox
		if (data.defaults?.count !== undefined)
		{
			new SettingType(parent)
				.setName("Count")
				.setDesc(data.descriptions?.count)
				.addText(text =>
				{
					text.setValue(data.defaults?.count + "");
					data.count = text;
					text.inputEl.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
						else if (e.keyCode < 48 || e.keyCode > 57)
						{
							window.event.returnValue = null;
						}
				    });
					text.inputEl.addEventListener("blur", e =>
				    {
					    if (Number(e.target.value) < 1)
					    {
						    e.target.value = "1";
					    }
				    });
					return text;
				});
		}

		// Topbottom dropdown
		if (data.defaults?.topBottom !== undefined)
		{
			new SettingType(parent)
				.setName("Top or bottom")
				.setDesc(data.descriptions?.topBottom)
				.addDropdown(dropdown =>
				{
					dropdown.addOptions([ "Top", "Bottom" ]);
					dropdown.setValue(data.defaults?.topBottom);
					data.topBottom = dropdown;
					dropdown.selectEl.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
				    });
					return dropdown;
				});
		}

		// Rotate toggle button
		if (data.defaults?.rotate !== undefined)
		{
			new SettingType(parent)
				.setName("Rotate cards")
				.setDesc(data.descriptions?.rotate)
				.addToggle(toggle =>
				{
					toggle.setValue(data.defaults?.rotate);
					data.rotate = toggle;
					toggle.toggleEl.addEventListener("keypress", e =>
				    {
						if (e.key === "Enter") { firstButton.click(); }
				    });
					return toggle;
				});
		}
	},

	// Shutown function
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Ok") { return; }
		let result = { src: data.pileNames[data.src.getValue()] };
		if (data.dst) { result.dst = data.pileNames[data.dst.getValue()]; }
		if (data.count) { result.count = data.count.getValue(); }
		if (data.topBottom) { result.topBottom = data.topBottom.getValue(); }
		if (data.rotate) { result.rotate = data.rotate.getValue(); }
		resolveFnc(result);
	}
});

// Custom popop definition for altering the settings of the cards system
confirmObjectPath("_inlineScripts.cards_ui.settingsPopup",
{
	// Setup function
	onOpen: async (data, parent, firstButton, SettingType) =>
	{
		// The current card size (before we change it)
		const currentSize = _inlineScripts.state.sessionState.cards.size;
		const currentBackImage = getBackImage();

		// A parent div to center the card visualization
		let sampleContainerUi = document.createElement("div");
		sampleContainerUi.style["text-align"] = "center";
		parent.append(sampleContainerUi);

		// The card visualization
		data.sampleUi = document.createElement("img");
		data.sampleUi.src = getAbsolutePath(currentBackImage);
		data.sampleUi.style.width = currentSize + "px";
		sampleContainerUi.append(data.sampleUi);

		// A slider to change the card size
		let sizeUi = new SettingType(parent)
			.setName("Size (" + currentSize + ")")
			.setDesc("Pick the size for all cards (in pixels).")
			.addSlider((slider) =>
			{
				slider
					.setLimits(10, 500, 10)
					.setValue(currentSize)
					.onChange(value =>
					{
						data.sampleUi.style.width = value + "px";
						data.titleUi.innerText = "Size (" + value + ")";
					});
				data.sliderUi = slider;
				slider.sliderEl.addEventListener("keypress", e =>
				{
					if (e.key === "Enter") { firstButton.click(); }
				});
				return slider;
			});
		// Keep track of the slider label - to change its text when the size changes
		data.titleUi = sizeUi.nameEl;

		// A dropdown to select the card back
		let backImageUi = new SettingType(parent)
			.setName("Back image")
			.setDesc("Choose the image to represent the back of all cards.")
			.addDropdown(dropdown =>
			{
				data.backImageOptions =
					Object.keys(app.vault.fileMap).filter(
						v => { return v.match(/.(?:png|bmp|gif|jpg|jpeg)$/); }
					);
				data.backImageOptions.unshift("");
				dropdown.addOptions(data.backImageOptions);
				dropdown.setValue(data.backImageOptions.indexOf(currentBackImage));
				dropdown.onChange(value =>
				{
					data.sampleUi.src = getAbsolutePath(
						data.backImageOptions[value] ||
						_inlineScripts.cards.defaultBackImage);
				});
				data.backImageUi = dropdown;
				dropdown.selectEl.addEventListener("keypress", e =>
				{
					if (e.key === "Enter") { firstButton.click(); }
				});
				return dropdown;
			});
	},

	// Shutown function
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Ok") { return; }
		resolveFnc({
			size: data.sliderUi.getValue(),
			backImage: data.backImageOptions[data.backImageUi.getValue()] || "default"
		});
	}
});
```
__
Sets up this shortcut-file


__
__
```js
// User chooses an existing pile
userChoosesPileId = function(requestMessage, failMessage, pileToBlock)
{
	// Get names of the piles
	const pileNames =
		Object.keys(_inlineScripts.state.sessionState.cards.piles).
		filter(v => v !== pileToBlock);
	if (!pileNames.length)
	{
		return [ null, expFormat(failMessage + "No card-piles available.") ];
	}

	// Choose a pile
	const pileIndex =
		popups.pick("Choose a card-pile " + requestMessage, pileNames, 0, "adaptive");
	if (pileIndex === null) { return [ null, null ]; }

	// Return the chosen pile name
	return [ pileNames[pileIndex], null ];
}

// Return true if at least one pile exists, return false otherwise
function doPilesExist()
{
	return !!( Object.keys(_inlineScripts.state.sessionState.cards.piles).length );
}
```
__
Helper scripts


__
```
^ui cards? reset$
```
__
```js
return expand("cards reset");
```
__
ui cards reset - Clears all card-piles.


__
```
^ui cards? settings$
```
__
```js
// Choose settings
const newSettings = popups.custom(
	"Choose settings for all cards", _inlineScripts.cards_ui.settingsPopup);
if (newSettings === null) { return null; }

return expand("cards settings " + newSettings.size + " " + newSettings.backImage);
```
__
ui cards settings - User chooses card size and card back image.
***


__
```
^ui cards? pile$
```
__
```js
// Get the data-string to import
const toImport = popups.input("Enter a name for the new pile");
if (toImport === null) { return null; }
if (_inlineScripts.state.sessionState.cards.piles[toImport])
{
	return expFormat("Card-pile not created.  __" + toImport + "__ already exists.");
}

// User decides whether to rotate cards
const isFaceUp = popups.pick(
	"How should cards face in the card-pile?", [ "Face-down", "Face-up" ], 0, 2);
if (isFaceUp === null) { return null; }

// User decides whether to rotate cards
const hideMoved = popups.pick(
	"Should cards be printed when drawn or picked into the card-pile",
	[ "Yes", "No" ], _inlineScripts.state.sessionState.cards.priorShowMoved ? 0 : 1,
	2);
if (hideMoved === null) { return null; }

// Pile creation shortcut is called
return expand(
	"cards pile " + toImport + " " + (isFaceUp ? "up" : "down") + " " +
	(hideMoved ? "n" : "y"));
```
__
ui cards pile - User enters a name, card-facing and show-moved flag for the new card-pile.  Card-pile is created from the choices.


__
```
^ui cards? remove$
```
__
```js
// User chooses the pile to remove
const pile = await userChoosesPileId("to remove", "Card-pile not removed.");
if (pile[0] == null) { return pile[1]; }

// User decides whether to rotate cards
const doRecall = popups.pick(
	"Try recalling card-pile's cards before removing it?", [ "Yes", "No" ], 0, 2);
if (doRecall === null) { return null; }

// Shuffle shortcut is called
return expand("cards remove " + pile[0] + " " + (doRecall ? "n" : "y"));
```
__
ui cards remove - User choses a card-pile to remove, then removes that card-pile.


__
```
^ui cards? pilesettings$
```
__
```js
// User chooses the pile to change settings for
const pileName =
	await userChoosesPileId("to change settings for", "Card-pile not changed.");
if (pileName[0] == null) { return pileName[1]; }

// Get the pile to use it's current settings for defaults
const pile = _inlineScripts.state.sessionState.cards.piles[pileName[0]];

// User decides whether to rotate cards
const isFaceUp = popups.pick(
	"How should cards face in the card-pile?", [ "Face-down", "Face-up" ],
	pile.isFaceUp ? 1 : 0, 2);
if (isFaceUp === null) { return null; }

// User decides whether to rotate cards
const hideMoved = popups.pick(
	"Should cards be printed when drawn or picked into the card-pile",
	[ "Yes", "No" ], pile.showMoved ? 0 : 1, 2);
if (hideMoved === null) { return null; }

// Pile creation shortcut is called
return expand(
	"cards pilesettings " + pileName[0] + " " + (isFaceUp ? "up" : "down") + " " +
	(hideMoved ? "n" : "y"));
```
__
ui cards pilesettings - User chooses a card-pile, then choses its card-facing and show-moved flag.
***


__
```
^ui cards? fromfolder$
```
__
```js
// Make a list of viable folders to choose from
const folders = Object.keys(app.vault.fileMap)
	.filter(v => app.vault.fileMap[v].children?.length)
	// Choose folders with non-markdown files
	.filter(v =>
	{
		for (const child of app.vault.fileMap[v].children)
		{
			if (child.extension && child.extension !== "md")
			{
				return true;
			}
		}
		return false;
	});

// Choose a folder to create cards from
let folder =
	popups.pick("Choose a folder to take card images from", folders, 0, "adaptive");
if (!folder) { return null; }
folder = folders[folder];

// Choose a pile to put the cards into
const pileId =
	await userChoosesPileId("to hold new cards", "Card images not loaded.");
if (pileId === null) { return pile[1]; }

return expand("cards fromfolder " + pileId[0] + " " + folder);
```
__
ui cards fromfolder - User choses a folder to create new cards from, then choses a card-pile to put them into.  Cards are created and added to the chosen card-pile.
***


__
```
^ui cards? list$
```
__
```js
return expand("cards list");
```
__
ui cards list - Lists all card-piles.


__
```
^ui cards? peek$
```
__
```js
// Show a custom popup 
const data =
{
	defaults:
	{
		src: _inlineScripts.state.sessionState.cards.priorPeekPile || "",
		count: 1,
		topBottom: 0
	},
	descriptions:
	{
		src: "Card-pile to peek into",
		count: "How many cards to peek",
		topBottom: "Peek at top or bottom of card-pile"
	}
};
const result = popups.custom(
	"Choose peek options", _inlineScripts.cards_ui.manipulateCardsPopup, data);
if (result === null) { return null; }

// Run the non-ui shortcut
return expand(
	"cards peek " + result.count + " " + result.src + " " +
	(result.topBottom === "1" ? "y" : "n"));
```
__
ui cards peek - User choses a card-pile, how many cards to peek and what side to peek from (top or bottom).  The peeked cards are printed to the note.
***


__
```
^ui cards? draw$
```
__
```js
// Show a custom popup 
const data =
{
	defaults:
	{
		src: _inlineScripts.state.sessionState.cards.priorDrawSrc || "",
		dst: _inlineScripts.state.sessionState.cards.priorDrawDst || "",
		count: 1,
	},
	descriptions:
	{
		src: "Card-pile to draw from",
		dst: "Card-pile to add to",
		count: "How many cards to draw"
	}
};
const result = popups.custom(
	"Choose draw options", _inlineScripts.cards_ui.manipulateCardsPopup, data);
if (result === null) { return null; }

// Run the non-ui shortcut
return expand("cards draw " + result.count + " " + result.dst + " " + result.src);
```
__
ui cards draw - User choses one card-pile to draw from, one to add to and how many cards to draw.  Cards are moved from the top of one card-pile to the top of the other.


__
```
^ui cards? pick$
```
__
```js
// Show a custom popup 
const data =
{
	defaults:
	{
		src: _inlineScripts.state.sessionState.cards.priorPickSrc || "",
		dst: _inlineScripts.state.sessionState.cards.priorPickDst || ""
	},
	descriptions:
	{
		src: "Card-pile to pick from",
		dst: "Card-pile to add to"
	}
};
const result = popups.custom(
	"Choose pick options", _inlineScripts.cards_ui.manipulateCardsPopup, data);
if (result === null) { return null; }

// Run the non-ui shortcut
return expand("cards pick " + result.dst + " " + result.src);
```
__
ui cards pick - User choses one card-pile to pick from and one to add to, then the user picks the cards to move from the top of one to the top of the other.
***


__
```
^ui cards? shuffle$
```
__
```js
// Show a custom popup 
const data =
{
	defaults:
	{
		src: 0,
		rotate: false
	},
	descriptions:
	{
		src: "Card-pile to shuffle",
		rotate: "Rotate cards while shuffling?"
	}
};
const result = popups.custom(
	"Choose pick options", _inlineScripts.cards_ui.manipulateCardsPopup, data);
if (result === null) { return null; }

// Run the non-ui shortcut
return expand("cards shuffle " + result.src + " " + (result.rotate ? "y" : "n"));
```
__
ui cards shuffle - User choses a card-pile, and whether to rotate the cards while shuffling.  The card-pile is then shuffled.


__
```
^ui cards? unrotate$
```
__
```js
const pile = await userChoosesPileId("to un-rotate", "Cards not un-rotated.");
if (pile[0] == null) { return pile[1]; }
return expand("cards unrotate " + pile[0]);
```
__
ui cards unrotate - User choses a card-pile, then all the card-pile's cards are turned right-side-up.


__
```
^ui cards? reverse$
```
__
```js
const pile = await userChoosesPileId("to reverse", "Card-pile not reversed.");
if (pile[0] == null) { return pile[1]; }
return expand("cards reverse " + pile[0]);
```
__
ui cards reverse - User choses a card-pile, then the card-pile's order is reversed.


__
```
^ui cards? recall$
```
__
```js
const pile = await userChoosesPileId("to recall", "Cards not recalled.");
if (pile[0] == null) { return pile[1]; }
return expand("cards recall " + pile[0]);
```
__
ui cards recall -  User choses a card-pile, then the card-pile is recalled.  Recalling means finding all cards with the card-pile as their origin, then moving them to the card-pile.


__
```
^ui cards? reorigin$
```
__
```js
const pile = await userChoosesPileId("to re-origin", "Cards not re-origined.");
if (pile[0] == null) { return pile[1]; }
return expand("cards reorigin " + pile[0]);
```
__
ui cards reorigin - Asks the user to choose a card-pile, then the card-pile is re-origined..  Re-origining means setting the origin of all cards in a card-pile to that card-pile.
***


__
```
^ui cards? import$
```
__
```js
// Get the pile to import into
let pile =
	await userChoosesPileId("to import into", "Card-pile not imported.");
if (pile[0] == null) { return pile[1]; }
pile = pile[0];

// Get the pile's data-string for the default value
let defaultDataString =
	JSON.stringify(_inlineScripts.state.sessionState.cards.piles[pile]);

// Get the data-string to import
const toImport = popups.input("Enter the data-string to import", defaultDataString);
if (toImport === null) { return null; }
if (toImport === defaultDataString) { return null; }

// Run the import shortcut
return expand("cards reorigin " + pile + " " + toImport);
```
__
ui cards import - User chooses a card-pile and enters a data-string representation of a card-pile. 
 The data-string is turned into a card-pile and assigned to the card-pile the user chose.


__
```
^ui cards? export$
```
__
```js
const pile = await userChoosesPileId("to export", "Card-pile not exported.");
if (pile[0] == null) { return pile[1]; }
return expand("cards export " + pile[0]);
```
__
ui cards export - User chooses a card-pile, then the card-pile is exported to a data-string, which is printed to the note.
