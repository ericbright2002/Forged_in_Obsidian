Shortcuts for working with virtual cards.

This shortcut-file has a tutorial video available:
[Using the "cards" shortcut-file to use virtual cards](https://www.youtube.com/watch?v=KkpjTL2UvtQ) (runtime 12:43)

Here are some good card images you can download to try this out:
[Playing cards](https://opengameart.org/sites/default/files/Playing%20Cards.zip) - Standard playing cards.  Nice quality, though rather large file-size.
[Tarot cards](https://luciellaes.itch.io/rider-waite-smith-tarot-cards-cc0) - Good quality tarot cards under public domain.  I used these in the video tutorial.

Thanks to [luciellaes](https://luciellaes.itch.io) on itch.io for cleaning up and providing the tarot images.

This shortcut-file has supplementary shortcut files:
__cards_pileviewer.sfile__ - Adds an Obsidian panel to view card-decks.
__cards_ui.sfile__ - Graphical UI versions of all of these shortcuts.


__
__
```js
// Trigger the event callback calls for a change in what piles are available
function onPileListChanged()
{
	_inlineScripts.inlineScripts.HelperFncs.
		callEventListenerCollection(
			"cards.onPileListChanged",
			_inlineScripts.cards.listeners.onPileListChanged);
}

// Trigger the event callback calls for a change in a pile
function onPileChanged(pileName)
{
	_inlineScripts.cards.listeners.changedPile = pileName;
	_inlineScripts.inlineScripts.HelperFncs.
		callEventListenerCollection(
			"cards.onPileChanged",
			_inlineScripts.cards.listeners.onPileChanged);
}

// Get the current back-image, url
function getBackImage()
{
	return _inlineScripts.state.sessionState.cards.backImage ||
	       _inlineScripts.cards.defaultBackImage;
}

// Turn a relative path url into an absolute path url based in the vault's root
function getAbsolutePath(path)
{
	if (path.startsWith("data:image")) { return path; }
	path = app.vault.fileMap[path];
	if (!path) { return ""; }
	return app.vault.getResourcePath(path);
}

// Create a block of html to represent a specific card
function createCardUi(isFaceUp, card, id, scale, includeDataSrc)
{
	const result = document.createElement("img");
	result.classList.add("cardUi");
	result.src = getAbsolutePath(isFaceUp ? card.path : getBackImage());
	const size = _inlineScripts.state.sessionState.cards.size * (scale || 1.0);
	result.style.width = size + "px";
	result.style.height = (size * card.aspect) + "px";
	result.dataset.id = id;
	if (includeDataSrc)
	{
		result.dataset.src = getAbsolutePath(card.path);
	}
	if (isFaceUp)
	{
		switch (card.rotation)
		{
			case 1: result.classList.add("rotated1"); break;
			case 2: result.classList.add("rotated2"); break;
			case 3: result.classList.add("rotated3"); break;
		}
	}
	return result;
}

// Create a block of markdown to represent a specific card
function createCardUi_inNote(isFaceUp, card)
{
	// Figure out what image to use
	let path = isFaceUp ? card.path : getBackImage();

	// Generate and return the text for the markdown card ui
	const alt = (!card.rotation || !isFaceUp) ? "" : "rotated" + card.rotation;
	const width = _inlineScripts.state.sessionState.cards.size;
	const height = Math.trunc(width * card.aspect);
	return "![" + alt + "|" + width + "x" + height + "](<" + path + ">) ";
}
```
__
Helper scripts


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Confirm state exists.  If not setup default state.
confirmObjectPath(
	"_inlineScripts.state.sessionState.cards",
	{ piles: {}, size: 150, priorShowMoved: true, backImage: null });

// Event callbacks for state system events
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.cards",
	function()
	{
		expand("cards reset noconfirm");
	});
confirmObjectPath(
	"_inlineScripts.state.listeners.onLoad.cards",
	function()
	{
		onPileListChanged();
		onPileChanged(null);
	});

// Confirm that cards system events are ready
confirmObjectPath("_inlineScripts.cards.listeners.onPileListChanged");
confirmObjectPath("_inlineScripts.cards.listeners.onPileChanged");

// Custom CSS
_inlineScripts.inlineScripts.HelperFncs.addCss("cards", ".rotated1, img[alt='rotated1'] { transform: rotate(90deg); } .rotated2, img[alt='rotated2'] { transform: rotate(180deg); } .rotated3, img[alt='rotated3'] { transform: rotate(270deg); } .rotated, img[alt='rotated'] { transform: rotate(180deg); } .iscript_cardChoice { filter: brightness(75%); cursor: pointer; border-width: 4px; border-style: solid;border-color: black; } .iscript_cardChoice:hover { filter:brightness(100%); } .iscript_cardSelected { filter:brightness(100%); border-color: yellow; }");

// Default card back image (hardcoded as base64)
confirmObjectPath("_inlineScripts.cards.defaultBackImage",
`data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABkCAYAAAAlg3YKAAAACXBIWXMAAA3XAAAN1wFCKJt4AAAEvElEQVR42u3dP2wTZxjH8e9diD2UYFdWKwuQXGpTEjxYSFkSIcuDJYTqZrmweSrZzixlZ2BPh+J3M52ytMILtVQhPFiQhCUS8uCiCKzUA8hSdaoP6OBE5Dr4znFCYiiY5o+f33TRvXlP/uTx+zqPLL0aPWk2nS9HR7kGZICzwGcMR/4BngIln4+fxsa0v7wbmndhWc4s8DMwxnDnJfB9KKQVu0CW5VwBfgG01dVNlpY2ef58k42N4RAZHYXTp3WmpnTGx3UAB7gSCmlFzX1bPQPG7t9/w8OHb4a6fJLJEdLpEYCXPh8x3V1zxlZXN4ceB+DBgzesrm4CnFhf55oOzAAsLW0i6WR5uWvxnQ58DfDihQB56bGI6cBxgPV1gfHSbncvj+vC0T8CJEACJEACJEACJEACJBEgARIgARIgARIgAZIIkAAJkAAJkAAJkABJBOjAAtnVFEr5USpF1d5lQMNEVe7tO9CxwU11j4qaodb9eQ7TVHuODiQqmIk61eLV3QdEFGaEowR0iZTZ5kI1RZnbGInokXiLHfvkT2iYqDWIWwVqzSnimdukIv3w6lSL51lsAvG7mKlL75zHbpiUSwWaAOE5MoYicqjWIGuCC0YbMzuLtfI7dt/BURJGGzMz937z2HnKKxNMZtuYZptsDFaq9UNUQQChswQAAmcJ8XSw87Se0GwWKC1c3xoXnwGihwioT7UEQ49Ya0HnlX9AghOEw/OkjdwHT7EPu9h51GL/Xay7WV2YZ2XBj2IK06yAnae4cL2znlBA1SB88Q+M4F7bYY70pElZ+d3fcccPaJPQLMtxAG7ckC8p9ubmTZ98kpZ/NQRIgARIgARIgARIIkACNBxAB6QPPVCgRjVFUflRyk+xkqdhf8RkEbXVQXxn6lSLJo1PDHTsY//ipWezZM0KAcC28/zZqhMJRI/MW2wg/aCWXYdAlEAgR8LtWm3rEzNHxnT7xNt6y7h959h79KEhHJ8nncoRaJioUqEzRhXA7SVVi/7OHOyYZ9+AIooseR6Xr2I1H9EMz5M1cgTsPOUSTGbbRHZr89XgTLZNqudewmiTcEG2xYIz6c5Yu5qiXL2MkVCY5g9Uiz8S7GnQL4bc5x+kCgpEcqQiuU7VeC8g+IRmfGZ3HIB+93YmtDU2EIzTXHvGXv3mDHcoF+8AcWJp1a3m/VukG3kqjXp30Wz97V4GJwjX7n7cgt2ttq157FaN8OexbaXY6nlGJFXBMCoYk7D4+N4BqKDIZc5UrqJKjwB3jUhEgRzpjEl5wU9p5xq0W/r1oeOwVvZT6q5BXvVE+SoGCwt+Ft01SCm/e2+Ki1k1EKCD3ZNumKi1mYEstv810pP+P7f5T5YD8AUGqSABEiABEiABEiABEiCJAAmQAAmQAAmQAAmQRIAESIAESIAESIAESCJAAjQAoNcAPp9gePH7u5cvdaAOcPKkFJMXz8JxqOvAbwDT0wLkxbPQNEr6xga3gFfj4zrJ5MjQ4ySTI5w7p0Pn8KNb3vFZs8CvuMdnLS93js8altNafD44dUpnelr3cLaOz/IGWZZj0DmA7cSQF9HbB7B5efXK+WJ9nWuaxreOwze45/4MQV47Dk81jZLPx63eI/z+BcWKtwx4PjyWAAAAAElFTkSuQmCC`);

// Custom popop definition for picking cards
confirmObjectPath("_inlineScripts.cards.cardPickerPopup",
{
	// Setup function
	onOpen: async (data, parent, firstButton, settingType) =>
	{
		// Parent ui element
		data.ui = parent;
		parent.style["overflow-y"] = "scroll";
		parent.parentNode.style.display = "flex";
		parent.parentNode.style["flex-direction"] = "column";
		parent.nextElementSibling.style["margin-top"] = ".5em"

		// Card visualization elements
		const pile = _inlineScripts.state.sessionState.cards.piles[data.pileId];
		for (let i = pile.cards.length - 1; i >= 0; i--)
		{
			const img = createCardUi(true, pile.cards[i], i, 1.0, true)
			parent.append(img);
			img.classList.add("iscript_cardChoice");
			img.addEventListener("pointerdown", function()
			{
				this.classList.toggle("iscript_cardSelected");
			});
		}
	},

	// Shutown function
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Ok") { return; }

		// Create a list of the selected cards
		let result = [];
		const children = data.ui.children;
		for (var i = 0; i < children.length; i++)
		{
		  if (children[i].classList.contains("iscript_cardSelected"))
		  {
			  result.push(children[i].dataset.id)
		  }
		}

		// Return the list of selected cards
		resolveFnc(result);
	}
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
delete _inlineScripts.state?.listeners?.onReset?.cards;
delete _inlineScripts.state?.listeners?.onLoad?.cards;

// Custom CSS removal
_inlineScripts.inlineScripts.HelperFncs.removeCss("cards");

// State removal
delete _inlineScripts.state?.sessionState?.cards;

// Session state removal
delete _inlineScripts.cards;
```
__
Shuts down this shortcut-file


__
```
^cards? reset$
```
__
```js
// Confirm
if (!popups.confirm("Confirm resetting the <b>Cards</b> system")) { return null; }

// Reset
expand("cards reset noconfirm");

return expFormat("All card-piles cleared.");
```
__
cards reset - Clears all card-piles.


__
```
^cards? reset noconfirm$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Make sure state container is ready
confirmObjectPath("_inlineScripts.state.sessionState");

// Recreate default state
_inlineScripts.state.sessionState.cards =
	{ piles: {}, size: 150, priorShowMoved: true, backImage: null };

// Trigger the event of the list of piles changing
onPileListChanged();
```
__
hidden - No-confirm reset


__
```
^cards? settings ?([1-9][0-9]*|) ?("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+|default|)$
```
__
```js
// Remove quotes around the path
$2 = $2.replaceAll(/^\"|\"$/g, "");

// Start expansion result
let result = "The card system's settings are updated:\n";

// Size
let sizeChanged = false;
if ($1 && $1 != _inlineScripts.state.sessionState.cards.size)
{
	_inlineScripts.state.sessionState.cards.size = $1;
	result += ". __Card size__ is changed to __" + $1 + "__ _(pixels)_.\n";
	sizeChanged = true;
}
else
{
	$1 = _inlineScripts.state.sessionState.cards.size;
	result += ". __Card size__ remains __" + $1 + "__ _(pixels)_.\n";
}

// Back image
let backImageFailedToChange = false;
let backImageChanged = false;
if ($2)
{
	if ($2.toLowerCase() === "default")
	{
		if (_inlineScripts.state.sessionState.cards.backImage)
		{
			_inlineScripts.state.sessionState.cards.backImage = null;
			result += ". __Back-image__ reset to default.";
			backImageChanged = true;
		}
	}
	else if ($1 !== _inlineScripts.state.sessionState.cards.backImage)
	{
		const file = app.vault.fileMap[$2];
		if (!file)
		{
			result +=
				". __Back-image__ not changed.  File __" + $2 + "__ was not found.";
			backImageFailedToChange = true;
		}
		else if (file.children)
		{
			result += ". __Back-image__ not changed.  __" + $2 + "__ is not a file.";
			backImageFailedToChange = true;
		}
		else
		{
			_inlineScripts.state.sessionState.cards.backImage = $2;
			result += ". __Back-image__ changed to __" + $2 + "__.";
			backImageChanged = true;
		}
	}
}
if (!backImageChanged && !backImageFailedToChange)
{
	$2 = _inlineScripts.state.sessionState.cards.backImage;
	$2 = $2 ? ("__" + $2 + "__") : "at default";
	result += ". __Back-image__ remains " + $2 + ".";
}

// React to changes
if (sizeChanged || backImageChanged) { onPileChanged(); }

return expFormat(result);
```
__
cards settings {size: >0, default: ""} {back-image: path text, default: ""} - Updates the settings for the cards system.
    - __{size}__ - The width for all cards, in pixels.  Card height scales to match.
    - __{back-image}__ - The path to an image file to represent face-down cards, or "default" to reset to the default back-image.
***


__
```
^cards? pile ([_a-zA-Z][_a-zA-Z0-9]*) ?(up|down|) ?(y|n|)$
```
__
```js
// Early out if name is already used: prevent overwriting existing pile with new one
if (_inlineScripts.state.sessionState.cards.piles[$1])
{
	return expFormat(
		"Card-Pile not created.  " + "The __" + $1 + "__ card-pile already exists.");
}

// If "show moved" parameter is passed, remember it.
if ($3)
{
	_inlineScripts.state.sessionState.cards.priorShowMoved = ($3 === "y");
}

// Create the new pile
const showMoved = _inlineScripts.state.sessionState.cards.priorShowMoved;
_inlineScripts.state.sessionState.cards.piles[$1] =
	{ cards: [], isFaceUp: ($2 === "up"), showMoved };

// Trigger the event of the list of piles changing
onPileListChanged();

return expFormat("The __" + $1 + "__ card-pile is created.");
```
__
cards pile {pile id: name text} {facing: up OR down, default: down} {show moved: y OR n, default: prior} - Creates an empty card-pile {pile id} with all cards facing {facing} (face-up or face-down).  If {show moved}, cards that are drawn or picked into the {pile id} card-pile are also printed to the note.


__
```
^cards? remove ([_a-zA-Z][_a-zA-Z0-9]*) ?(y|n|)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Card-pile not removed.  The __" + $1 + "__ card-pile was not found.");
}

// Confirm with user
if (!popups.confirm("Confirm destroying the <b>" + $1 + "</b> card-pile."))
{
	return null;
}

// Start expansion result
let result = "";

// Recall all cards (if parameter says to do so)
if ($2 === "y")
{
	const cardCount = pile.cards.length;
	let recallCount = 0;
	for (let i = cardCount - 1; i >= 0; i--)
	{
		const card = pile.cards[i];

		// Don't recall a card that originates from THIS pile
		if (card.origin === $1) { continue; }

		// Get the origin pile
		const originPile =
			_inlineScripts.state.sessionState.cards.piles[card.origin];
		if (!originPile) { continue; }

		// Move the card to the origin pile
		originPile.cards.push(card);
		pile.cards.splice(i, 1);

		// Track how many cards are recalled
		recallCount++;
	}

	// Add a notification of this recall to the expansion
	result +=
		"__" + recallCount + " / " + cardCount + "__ cards in the __" + $1 +
		"__ card-pile are recalled to other card-piles.\n";

	// Notify of all piles (potentially) changing
	if (recallCount > 0)
	{
		onPileChanged(null);
	}
}

// Add notification about removing this pile
const cardRemoveCount =
	_inlineScripts.state.sessionState.cards.piles[$1].cards.length;
result += "The __" +
	$1 + "__ card-pile is removed along with __" + cardRemoveCount +
	"__ cards.";

// Remove the pile
delete _inlineScripts.state.sessionState.cards.piles[$1];

// Re-origin any orphaned cards - cards that have the removed pile as their origin.
let reOriginCount = 0;
for (const key in _inlineScripts.state.sessionState.cards.piles)
{
	const pileToSearch = _inlineScripts.state.sessionState.cards.piles[key];
	for (const card of pileToSearch.cards)
	{
		if (card.origin === $1)
		{
			card.origin = key;
			reOriginCount++;
		}
	}
}

// Add notification of re-origining orphaned cards.
if (reOriginCount)
{
	result += "\n__" + reoriginCount + "__ orphaned cards were re-origined.";
}

// Trigger the event of the list of piles changing
onPileListChanged();

return expFormat(result);
```
__
cards remove {pile id: name text} {recall: y OR n, default: n} - Removes the {pile id} card-pile, including all cards within it.  If {recall}, all cards in {pile id} are recalled before the {pile id} card-pile is removed.  If the {pile id} card-pile is the origin for any cards, then they are orphaned, and immediately re-origined to their current card-pile.


__
```
^cards? pilesettings ([_a-zA-Z][_a-zA-Z0-9]*) ?(up|down|) ?(y|n|)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Card-Pile not changed.  The __" + $1 + "__ card-pile was not found.");
}

// Start expansion notification
let result = "The __" + $1 + "__ card-pile's settings are updated:\n";

// Update facing
if ($2 && pile.isFaceUp !== ($2 === "up"))
{
	pile.isFaceUp = !pile.isFaceUp;
	result += ". __Facing__ is changed to __" + $2 + "__.\n";

	// Trigger the event of the pile changing
	onPileChanged($1);
}
else
{
	result += ". __Facing__ remains __" + (pile.isFaceUp ? "up": "down") + "__.\n";
}

// Update "Show moved" flag
if ($3 && pile.showMoved !== ($3 === "y"))
{
	pile.showMoved = !pile.showMoved;
	result += ". __ShowMoved__ is set to __" + pile.showMoved + "__."; 
}
else
{
	result += ". __ShowMoved__ remains __" + pile.showMoved + "__.";
}

return expFormat(result);
```
__
cards pilesettings {pile id: name text} {facing: up OR down, default: current} {show moved: y OR n, default: current} - Updates the settings for the {pile id} card-pile.
    - __{facing}__ - Are the {pile id} card-pile's cards shown face-up or face-down?
    - __{show moved}__ - Are cards that are drawn or picked into the {pile id} card-pile also printed to the note?
***


__
```
^cards? fromfolder ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
// Remove quotes around the path
$2 = $2.replaceAll(/^\"|\"$/g, "");

// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Cards not created.  The __" + $1 + "__ card-pile was not found.");
}

const folder = app.vault.fileMap[$2];

// Early out if specified folder (to get cards from) doesn't exist or isn't a folder
if (!folder)
{
	return expFormat("Cards not created.  Folder __" + $2 + "__ was not found.");
}
if (!folder.children)
{
	return expFormat("Cards not created.  __" + $2 + "__ is not a folder.");
}

// Helper function - Get an image size by loading and reading its binary content
// Note - has a reliable, but slow backup function "getImageSize_reliable"
async function getImageSize_fast(file)
{
	// Testing line
	//file = app.vault.fileMap[file];

	// Helper function - turn 4 bytes into an integer
	function bytesToInt(v1, v2, v3, v4)
	{
		return (v1 << 24) + (v2 << 16) + (v3 << 8) + v4;
	}

	// Load the image's entire binary data (could optimize by not loading all)
	const b = new Uint8Array(await app.vault.readBinary(file));

	// Return value initialization
	let result = null;

	// If PNG file, read size
	if (b[1] === 80 && b[2] === 78 && b[3] === 71)
	{
		result = [ bytesToInt(b[16], b[17], b[18], b[19]),
		           bytesToInt(b[20], b[21], b[22], b[23]) ];
	}

	// If GIF file, read size
	else if (b[0] === 71 && b[1] === 73 && b[2] === 70)
	{
		result = [ bytesToInt(0, 0, b[7], b[6]),
		           bytesToInt(0, 0, b[9], b[8]) ];
	}

	// If BMP file, read size
	else if (b[0] === 66 && b[1] === 77)
	{
		result = [ bytesToInt(b[21], b[20], b[19], b[18]),
		           bytesToInt(b[25], b[24], b[23], b[22]) ];
	}

	// If JPG file, read size
	// Note - (JFIF) - https://stackoverflow.com/a/48488655 & further answers
	else if (b[0] === 255 && b[1] === 216 && b[2] === 255 && b[3] === 224 &&
	         b[6] === 74 && b[7] === 70 && b[8] === 73 && b[9] === 70 && b[10]===0)
	{
		let i = 0;
		while (i < b.length)
		{
			if (b[i] !== 255) { break; }
			while (b[i] === 255) { i++; }
			const mrk = b[i];
			i++;

	        if(mrk === 1) { continue; }   // TEM
	        if(208 <= mrk && mrk <= 215) continue;
	        if(mrk === 216) { continue; } // SOI
	        if(mrk === 217) { break; }    // EOI

	        const len = bytesToInt(0, 0, b[i], b[i+1]);
	        if (192 <= mrk && mrk <= 207) // C0 to CF
	        {
		        i += 3;
				result = [ bytesToInt(0, 0, b[i+2], b[i+3]),
				           bytesToInt(0, 0, b[i+0], b[i+1]) ];
				break;
	        }
	        i += len;
		}
	}

	// Return size, nicely formatted
	return { w: result[0], h: result[1] };
}

// Helper function - Get image size by running image through html system
// NOTE - is a backup for the fast method
async function getImageSize_reliable(file)
{
	let result = null;
	try
	{
		// Read in the binary
		const buf = await app.vault.readBinary(file);

		// Convert binary to base64
		let binary = '';
		const bytes = new Uint8Array(buf);
		bytes.forEach(b => binary += String.fromCharCode(b));
		const base64Data = window.btoa(binary);

		// Create image element, assign base-64 image to it, then read the size
		result = await new Promise((onDone) =>
		{
			const i = new Image(1,1);
			i.src = `data:${i.type};base64,${base64Data}`;
			i.onload = () =>
			{
				onDone({ w: i.naturalWidth, h: i.naturalHeight });
			}
			i.onerror = () =>
			{
				onDone(null);
			}
		});
	}
	catch(e) {}

	// Return the found image size
	return result;
}

// Start expansion result
let result = "";

// Iterate over all children of the given folder
let createCount = 0;
for (let i = folder.children.length - 1; i >= 0; i--)
{
	const childFile = folder.children[i];
	// Ignore sub-folders
	if (childFile.children) { continue; }

	// Get image size
	let size = await getImageSize_fast(childFile);
	if (!size)
	{
		size = await getImageSize_reliable(childFile);
	}

	// If unable to get a card's size, it's not an image.
	// Add notification to the expansion.
	if (size == null)
	{
		result += "Unable to create card from file __" + childFile.name + "__.\n";
		continue;
	}

	// Create and add the card object based on the current image
	pile.cards.push(
	{
		path: childFile.path,
		rotation: 0,
		aspect: size.h / size.w,
		origin: $1
	});
	createCount++;
}

// Trigger the event of the pile changing, if any cards were actually added
if (createCount)
{
	onPileChanged($1);
}

return expFormat(
	"__" + createCount + "__ cards added to the __" + $1 + "__ card-pile.");
```
__
cards fromfolder {pile id: name text} {folder: path text} - Creates cards based on images in {folder} and puts them into the {pile id} card-pile.
	- Note that this does not randomize the new cards.  Call the "cards shuffle" shortcut to do that.
***


__
```
^cards? list?$
```
__
```js
// Initialize expansion notification
let result = "Card-piles:\n";

// The collection of all piles
const piles = _inlineScripts.state.sessionState.cards.piles;

// List of all pile names
const names = Object.keys(piles);

// If there are piles, generate a list and add it to the expansion
if (names.length)
{
	const list = names.map(v => v + " _(" + piles[v].cards.length + " cards)_");
	result += ". " + list.join("\n. ");
}

// If there are NO piles, say so
else
{
	result += "NONE";
}

return expFormat(result);
```
__
cards list - Lists all card-piles.


__
```
^cards? peek ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(y|n|)$
```
__
```js
// Early out if the pile to peek is not provided or remembered from a prior peek.
if (!$2 && !_inlineScripts.state.sessionState.cards.priorPeekPile)
{
	return expFormat("Cards not peeked.  The card-pile to peek into is undefined.");
}

// If peek-from-bottom isn't specified & not remembered from prior peek, use default.
if (!$3 && !_inlineScripts.state.sessionState.cards.priorPeekIsBottom)
{
	$3 = "n";
}

// Set unspecified parameters to those from prior peek
if (!$2) { $2 = _inlineScripts.state.sessionState.cards.priorPeekPile; }
if (!$3) { $3 = _inlineScripts.state.sessionState.cards.priorPeekIsBottom; }

// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$2];
if (!pile)
{
	return expFormat(
		"Cards not peeked.  The card-pile __" + $1 + "__ was not found.");
}

// Remember the parameters for the next peek
_inlineScripts.state.sessionState.cards.priorPeekPile = $2;
_inlineScripts.state.sessionState.cards.priorPeekIsBottom = $3;

// Calculate actual values from the parameters
const fromBottom = ($3 === "y");
const count = Math.min(Number($1) || 1, pile.cards.length);

// Create view of peeked cards
let result = "";
for (let i = 0; i < count; i++)
{
	const cardIndex = (fromBottom ? i : (pile.cards.length - i - 1));
	result += createCardUi_inNote(true, pile.cards[cardIndex]);
}

return expFormat(
	"Cards peeked from the " + (fromBottom ? "bottom" : "top") + " of the __" + $2 +
	"__ card pile:\n" + result);
```
__
cards peek {count: >0, default: 1} {pile id: name text, default: prior} {from the bottom: y OR n, default: prior OR n} - Displays the first {count} cards in the {pile id} card-pile.  If {from the bottom}, displays the LAST {count} cards instead.
***


__
```
^cards? draw ?([1-9][0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
// Check if we should be using the OTHER "cards draw" shortcut
const $2Lowered = $2.toLowerCase();
if ($2Lowered === "from" || $2Lowered === "into" || $2Lowered === "to" &&
    !_inlineScripts.state.sessionState.cards.piles[$2])
{
	let otherWord = ($2Lowered === "from") ? "to" : "from";
	return expand("cards draw " + $1 + " " + $2 + " " + $3 + " " + otherWord);
}

// Early out if source & destination piles not provided or recalled from prior draw.
if (!$2 && !_inlineScripts.state.sessionState.cards.priorDrawDst)
{
	return expFormat("Cards not drawn.  The destination card-pile is not defined.");
}
if (!$3 && !_inlineScripts.state.sessionState.cards.priorDrawSrc)
{
	return expFormat("Cards not drawn.  The source card-pile is not defined.");
}

// Set unspecified parameters to those from prior draw
if (!$2) { $2 = _inlineScripts.state.sessionState.cards.priorDrawDst; }
if (!$3) { $3 = _inlineScripts.state.sessionState.cards.priorDrawSrc; }

// Get the the source and destination piles
const dstPile = _inlineScripts.state.sessionState.cards.piles[$2];
const srcPile = _inlineScripts.state.sessionState.cards.piles[$3];

// Early out if the source or destination piles don't exist
if (!dstPile)
{
	return expFormat(
		"Cards not drawn.  The destination card-pile __" + $2 + "__ was not found.");
}
if (!srcPile)
{
	return expFormat(
		"Cards not drawn.  The source card-pile __" + $3 + "__ was not found.");
}

// Remember the parameters for the next draw
_inlineScripts.state.sessionState.cards.priorDrawDst = $2;
_inlineScripts.state.sessionState.cards.priorDrawSrc = $3;

// Calculate actual values from the parameters and settings
const count = Math.min(Number($1) || 1, srcPile.cards.length);

// Calculate whether to show the drawn cards in the expansion
const showCards = (dstPile.showMoved && dstPile.isFaceUp);

// Pull each card to draw (and make a display of them if "showCards" is true)
let drawnCount = 0;
let cardView = showCards ? "\n" : "";
let transfer = [];
for (let i = 0; i < count; i++)
{
	if (!srcPile.cards.length) { break; }
	const drawnCard = srcPile.cards.pop();
	transfer.push(drawnCard);
	drawnCount++;
	cardView += showCards ? createCardUi_inNote(dstPile.isFaceUp, drawnCard) : "";
}

// Add each drawn card to the destination pile
transfer.reverse();
dstPile.cards.push(...transfer);

// Make the expansion string
const faceDownMsg = dstPile.isFaceUp ? "" : " _(face-down)_";;
const result =
	"__" + drawnCount + "__ card(s) drawn from the __" + $3 +
	"__ card-pile to the __" + $2 + "__ card-pile" + faceDownMsg + "." + cardView;

// Trigger the events of the source and destination piles changing
onPileChanged($2);
onPileChanged($3);

return expFormat(result);
```
__
cards draw {count: >0, default: 1} {destination pile id: name text, default: prior} {source pile id: name text, default: prior} - Removes {count} cards from the {source pile id} card-pile and adds them to the {destination pile id} card-pile.


__
```
^cards draw (.*)$
```
__
```js
// Split the details parameter into detail words to parse
const detailWords = $1.split(" ").filter(v => v);

// Setup the "from" and "to" to pull from the details
let fromPileName = "";
let toPileName = "";
let count = "";

// Track the expected next detail word's type based on the prior detail word
let expectation = 0;

// Iterate over all detail words
for (const detailWord of detailWords)
{
	// Use the lowercase version of the word for case insensitivity
	const detailWordLowered = detailWord.toLowerCase();

	// Handle logic for the current detail word based on expectation
	switch (expectation)
	{
		case 0:
			if (detailWordLowered === "from")
			{
				expectation = 1;
			}
			else if (detailWordLowered === "into" ||
			         detailWordLowered === "to")
			{
				expectation = 2;
			}
			else if (Number(detailWord) && Number(detailWord) > 0)
			{
				count = detailWord;
			}
			else
			{
				return expFormat(
					[ "", "Cards not picked.  Expected 'from', 'into', 'to' or a " +
					"positive number, but found __" + detailWord + "__." ]);
			}
			break;
		case 1:
			fromPileName = detailWord;
			expectation = 0;
			break;
		case 2:
			toPileName = detailWord;
			expectation = 0;
			break;
	}
}

return expand("cards draw " + count + " " + toPileName + " " + fromPileName);
```
__
cards draw {details: text} - This version of "draw" lets you define the 3 parameters in your own order.
  - __Parameters:__
    - __source card-pile__ - prefix with the word "from".  If skipped, defaults to what it was on the prior draw.
    - __destination card-pile__ - prefix with the word "into" or "to".  If skipped, defaults to what it was on the prior draw.
    - __count__ - don't prefix with anything.  It'll be recognized as a number of 1 or more.  If skipped, defaults to 1.
  - __Examples:__
    - `;;cards draw 3 from deck into hand::`
    - `;;cards draw to hand 5 from deck::`
    - `;;cards draw into discard::`
    - `;;cards draw 7 from deck::`
    - `;;cards draw::`


__
```
^cards? pick ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
// Check if we should be using the OTHER "cards pick" shortcut
const $1Lowered = $1.toLowerCase();
if ($1Lowered === "from" || $1Lowered === "into" || $1Lowered === "to" &&
    !_inlineScripts.state.sessionState.cards.piles[$1])
{
	let otherWord = ($1Lowered === "from") ? "to" : "from";
	return expand("cards pick " + $1 + " " + $2 + " " + otherWord);
}

// Early out if source & destination piles not provided or recalled from prior pick.
if (!$1 && !_inlineScripts.state.sessionState.cards.priorPickDst)
{
	return expFormat("Cards not picked.  The destination card-pile is not defined.");
}
if (!$2 && !_inlineScripts.state.sessionState.cards.priorPickSrc)
{
	return expFormat("Cards not picked.  The source card-pile is not defined.");
}

// Set unspecified parameters to those from prior pick
if (!$1) { $1 = _inlineScripts.state.sessionState.cards.priorPickDst; }
if (!$2) { $2 = _inlineScripts.state.sessionState.cards.priorPickSrc; }

// Get the the source and destination piles, early out if either doesn't exist
const dstPile = _inlineScripts.state.sessionState.cards.piles[$1];
const srcPile = _inlineScripts.state.sessionState.cards.piles[$2];
if (!dstPile)
{
	return expFormat(
		"Cards not picked.  The destination card-pile __" + $1 +
		"__ was not found.");
}
if (!srcPile)
{
	return expFormat(
		"Cards not picked.  The source card-pile __" + $2 + "__ was not found.");
}

// Remember the parameters for the next pick
_inlineScripts.state.sessionState.cards.priorPickDst = $1;
_inlineScripts.state.sessionState.cards.priorPickSrc = $2;

// Pick cards
let picks = popups.custom(
	"Pick cards to move from the <b>" + $2 + "</b> to the <b>" + $1 + "</b>.",
	_inlineScripts.cards.cardPickerPopup, { pileId: $2 });
if (!picks) { return; }

// Calculate whether to show the picked cards in the expansion
const showCards = (dstPile.showMoved && dstPile.isFaceUp);

// Pull each picked card (and make a display of them if "showCards" is true)
let cardView = showCards ? "\n" : "";
let transfer = [];
for (let i = 0; i < picks.length; i++)
{
	if (!srcPile.cards.length) { break; }
	const drawnCard = srcPile.cards.splice(picks[i], 1)[0];
	transfer.push(drawnCard);
	cardView += showCards ? createCardUi_inNote(dstPile.isFaceUp, drawnCard) : "";
}

// Add each picked card to the destination pile
transfer.reverse();
dstPile.cards.push(...transfer);

// Make the expansion string
const faceDownMsg = dstPile.isFaceUp ? "" : " _(face-down)_";
const result =
	"__" + picks.length + "__ card(s) picked from the __" + $2 +
	"__ card-pile to the __" + $1 + "__ card-pile" + faceDownMsg + "." + cardView;

// Trigger the events of the source and destination piles changing
onPileChanged($1);
onPileChanged($2);

return expFormat(result);
```
__
cards pick {destination pile id: name text, default: prior} {source pile id: name text, default: prior} - Has the user choose cards from the {source pile id} card-pile.  Moves the chosen cards into the {destination pile id} card-pile.


__
```
^cards pick (.*)$
```
__
```js
// Split the details parameter into detail words to parse
const detailWords = $1.split(" ").filter(v => v);

// Setup the "from" and "to" to pull from the details
let fromPileName = "";
let toPileName = "";

// Track the expected next detail word's type based on the prior detail word
let expectation = 0;

// Iterate over all detail words
for (const detailWord of detailWords)
{
	// Use the lowercase version of the word for case insensitivity
	const detailWordLowered = detailWord.toLowerCase();

	// Handle logic for the current detail word based on expectation
	switch (expectation)
	{
		case 0:
			if (detailWordLowered === "from")
			{
				expectation = 1;
			}
			else if (detailWordLowered === "into" ||
			         detailWordLowered === "to")
			{
				expectation = 2;
			}
			else
			{
				return expFormat(
					[ "", "Cards not picked.  Expected 'from', 'into' or 'to', " +
					"but found __" + detailWord + "__." ]);
			}
			break;
		case 1:
			fromPileName = detailWord;
			expectation = 0;
			break;
		case 2:
			toPileName = detailWord;
			expectation = 0;
			break;
	}
}

return expand("cards pick " + toPileName + " " + fromPileName);
```
__
cards pick {details: text} - This version of "pick" lets you define the 2 parameters in your own order.
  - __Parameters:__
    - __source card-pile__ - prefix with the word "from".  If skipped, defaults to what it was on the prior pick.
    - __destination card-pile__ - prefix with the word "into" or "to".  If skipped, defaults to what it was on the prior pick.
  - __Examples:__
    - `;;cards pick from deck into hand::`
    - `;;cards pick to hand from deck::`
    - `;;cards pick into discard::`
    - `;;cards pick from deck::`
    - `;;cards pick::`
***


__
```
^cards? shuffle ([_a-zA-Z][_a-zA-Z0-9]*) ?(y|n|)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Cards not shuffled.  The __" + $1 + "__ card-pile was not found.");
}

// Randomize order of cards in pile
for (let i = pile.cards.length - 1; i > 0; i--)
{
	const j = Math.floor(Math.random() * (i + 1));
	[pile.cards[i], pile.cards[j]] = [pile.cards[j], pile.cards[i]];
}

// Randomize rotation of cards in pile
if ($2 === "y")
{
	for (let card of pile.cards)
	{
		card.rotation =
			(card.aspect == 1) ?
			Math.trunc(Math.random() * 4) :
			Math.trunc(Math.random() * 2) * 2;
	}
}

// Trigger the event of the pile changing
onPileChanged($1);

return expFormat("The __" + $1 + "__ card-pile is shuffled.");
```
__
cards shuffle {pile id: name text} {rotate: y OR n, default: n} - Randomizes the card order for the {pile id}.  If {rotate}, then card rotations are also randomized.
	- Rotation typically means 0 or 180 degrees (right-side-up or up-side-down), but can also mean 90 or 270 degrees if the card is square.


__
```
^cards? unrotate ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Cards not unrotated.  The __" + $1 + "__ card-pile was not found.");
}

// Unrotate all cards in the pile
let unrotatedCount = 0;
for (let card of pile.cards)
{
	if (card.rotation !== 0)
	{
		card.rotation = 0;
		unrotatedCount++;
	}
}

// Trigger the event of the pile changing, if any of the cards were unrotated
if (unrotatedCount)
{
	onPileChanged($1);
}

return expFormat(
	"The __" + $1 + "__ card-pile's cards are all rotated to 0 degrees " +
	"(right-side-up).");
```
__
cards unrotate {pile id: name text} - Sets the rotation for all cards in the {pile id} card-pile to 0 (right-side-up).


__
```
^cards? reverse ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Cards not reversed.  The __" + $1 + "__ card-pile was not found.");
}

// Reverse the order of the cards
pile.cards.reverse();

// Trigger the event of the pile changing
onPileChanged($1);

return expFormat("Cards in the __" + $1 + "__ card-pile have been reversed.");
```
__
cards reverse {pile id: name text} - Reverses the order of the cards in the {pile id} card-pile.


__
```
^cards? recall ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Get the list of piles and the specified pile, early out if it doesn't exist
const piles = _inlineScripts.state.sessionState.cards.piles;
const pile = piles[$1];
if (!pile)
{
	return expFormat(
		"Cards not recalled.  The __" + $1 + "__ card-pile was not found.");
}

// Iterate over all piles, pulling all cards with an origin of the specified pile
let recallCount = 0;
let transfer = [];
for (const key in piles)
{
	if (piles[key] === pile) { continue; }

	// Iterate over cards in this pile, checking their origin and pulling if a match
	for (let k = piles[key].cards.length - 1; k >= 0; k--)
	{
		if (piles[key].cards[k].origin === $1)
		{
			transfer.push( piles[key].cards.splice(k, 1)[0] );
			recallCount++;
		}
	}
}

// Put all pulled cards into the specified pile
transfer.reverse();
pile.cards.push(...transfer);

// Trigger the event of all piles changing, if any cards were recalled
if (recallCount)
{
	onPileChanged(null);
}

return expFormat(
	"__" + recallCount + "__ cards returned to the __" + $1 + "__ card-pile.");
```
__
cards recall {pile id: name text} - Moves all cards that have the {pile id} card-pile as their origin, from their current card-piles back into the {pile id} card-pile.  If {facing} is specified, all cards in {pile id} are then put to face {facing}.


__
```
^cards? reorigin ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Cards not re-origined.  The __" + $1 + "__ card-pile was not found.");
}

// Iterate over all cards in the pile and set their origin to the pile
for (let card of pile.cards)
{
	card.origin = $1;
}

return expFormat("Cards in the __" + $1 + "__ card-pile have been re-origined.");
```
__
cards reorigin {pile id: name text} - Sets the origin of all cards in the {pile id} card-pile to {pile id}.
***


__
```
^cards? import ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
// Early out if the specified pile doesn't exist
if (!_inlineScripts.state.sessionState.cards.piles[$1])
{
	return expFormat(
		"Card-pile not imported.  The __" + $1 + "__ card-pile was not found.");
}

// Try and parse the given data.  Early out if failed
let data = null;
try
{
	data = JSON.parse($2);
	if (!Array.isArray(data.cards))
	{
		throw null;
	}
}
catch (e)
{
	return expFormat("Card-pile not imported.  Failed to parse data-string.");
}

// Set the specified pile to the given data
_inlineScripts.state.sessionState.cards.piles[$1] = data;

// Trigger the event of the specified pile changing
onPileChanged($1);

return expFormat("The __" + $1 + "__ card-pile was imported");
```
__
cards import {pile id: name text, default: table} {data: text} - Imports the {data} into the {pile id} card pile.


__
```
^cards? export ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// Get the pile, early out if it doesnt exist
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return expFormat(
		"Card-pile not exported.  The __" + $1 + "__ card-pile was not found.");
}

// Make a data-string from the specified pile
let result = JSON.stringify(pile);

// Return the data-string
// NOTE: don't use the expansion-format's prefix or line-prefix.  This makes the
// data-string is easier to select and copy in the note.
return expFormat("Card-pile __" + $1 + "__ export:\n" + result + "", true, true);
```
__
cards export {pile id: name text} - Expands to a data-string containing all date for the {pile id} card-pile.
