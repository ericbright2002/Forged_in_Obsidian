# Readme
Welcome to my Starforged Starter Vault!  I set this up to play Starforged in a way that fits me best.

## Fonts
For the headings, I'm using [Xolonium font](https://www.fontspace.com/xolonium-font-f17644) and [Noto Sans](https://fonts.google.com/noto/specimen/Noto+Sans) as the interface font.

## Plugins
This vault only works with the [Dataview](https://github.com/blacksmithgu/obsidian-dataview), [Templater](https://github.com/SilentVoid13/Templater), and most importantly: [Inline Scripts](https://github.com/jon-heard/obsidian-inline-scripts) plugins.

## CSS Snippet
The `snippets.css` file contains all the folder icons, color coding of new callout boxes, title and header fonts, fixing the tasks so they don't strike through when marked, and an inversion code so that the SVG files will invert black/white based on light/dark mode.

## Design Goals
Here were some specific design goals:

1. **Never Leave the Journal:** I usually play solo-rpgs by writing long-form like in a story.  For that reason, I wanted to minimize the number of times I'd have to leave the journal entry of the current play session.  Inline Scripts allows me that freedom in all but a few cases (like creating a new vow).
   
2. **Minimize the Number of Reference Windows:** Starforged has a lot more fiddly bits than Ironsworn which has really slowed down my play.  I wanted to eliminate the friction by not having to sort through tons of reference windows or menus.  To help with that, you can call a move reference directly into the journal and can access the oracles from an inline script.  My workspace is a journal on the left, with the character sheet on the top-right and other reference note (like a move or oracles) on the bottom-right.
   
3. **Make Mechanics Distinct from Story:** One of the things I've always done playing in a word processor is to have the mechanics as separate callouts so that you can see clearly what is fiction and what is game play.  Therefore, nearly any inline script you call here will give results in a callout which can be unfolded for more details.

## Setting up Your Campaign
To get started playing, you'll first want to set up your own character.  

1. **Oracles:** Not sure if this is necessary, but just in case, start by resetting the oracle files so that Inline Scripts gets all the updated oracles.

   >[!NOTE] Reset the Oracles
	  >***Type :: at the end of both commands below.***

>[!TIP] ;;tbl reset

>[!TIP] ;;tbl add Oracles

2. **Stats:** Under the `Character` folder, got to the `Stats` note and set your stat values (Edge, Heart, Iron, Shadow, Wits) up in the frontmatter.  The frontmatter is the section at the top of a note enclosed by `---` marks.  These act like variables for many of the Inline Scripts, so make sure these are accurate.
   
3. **Character Sheet:** In the `Character_Sheet` note, fill in your name, a picture if you want, and your backstory.  Then, scroll down to the bottom of the note and embed whichever assets you want your character to have.  Use the command `![[Ace]]` to embed the Ace asset for example.  This note pulls in everything else from the other notes in the `Character` folder.
   
4. **Background Vow and Inciting Incident:** To set your first vow, you can either right click on the `Progress` folder, create a new note, and manually fill out the frontmatter variables OR use the Inline Script in the Note box below.  First, let me tell you about all the variables at the top of each file that will have a progress track (like vows, combat, etc.).  
	- **Filename:** At the very top of the note is the file name which should be a single word using only letters or numbers or underscore.  For example, `Back_Vow` or `CombatGoal1` or `AlienFriends` are all fine where `Background Vow` or `Find the Sword` will cause the scripts to fail.  I suggest short file names such as `Combat1` or `Prog2` only because you'll need to type out the full filename for Inline Scripts to access it later on.
	  
	- **Name:** The name of your vow which can be any word or phrase such as `Survive the Meteor Shower` or `Escape the Black Hole's Gravity`.
	  
	- **Difficulty:** The difficulty is Troublesome, Dangerous, Formidable, Extreme, or Epic.  If typing manually, make sure to capitalize the first letter and spell the word correct for Inline Scripts to use.
	  
	- **Other Frontmatter:** You don't need to worry about the other frontmatter variables as they are all automated.
	  
   >[!NOTE] Setting Your Background Vow
	  >***To use Inline Scripts to set a new file with a progress track, type :: at the end of the command below.  Let's create a file for your background vow now.***

>[!TIP] ;;createprogress

5. **Create a New Journal Note and Play!:** Add a new note to the `Journals` folder, and start playing!

### No Map?
I didn't do anything to set up a sector map because I never play with a map.  There are other Obsidian vaults out there for Ironsworn and Starforged which you can use as a template for maps if you'd like.

## Folder Structure
1. **Character:** All the Inline Scripts shortcut commands require that the Character folder structure remain as it is now.  You can add to any currently existing file as long as the frontmatter variables remain.  Any new files you add to this folder won't interact with the current Inline Scripts shortcuts.

2. **Images:** All images are pulled from the Images folder.  You can add additional folders and images, but don't change the structure of the images currently there.

3. **Progress:** Any thing that will have a progress track must go in the Progress folder.  This includes combat tracks, vows, bonds with NPCs, etc.

## Inline Script Commands
While you are free to use any and all of the commands the Inline Scripts offers out of the box, below are explainations of the specific scripts I set up in four different files.  Remember that every Inline Scripts command starts with `;;` and ends with `::` by default.  I won't be able to type the full commands as examples below because it would trigger the scripts.

### Summary of Commands
1. <u>toggle {condition}</u> - Toggle a condition on/off.
   
2. <u>burnmom</u> - Burns and resets momentum.
   
3. <u>gain {meter/asset} {amount}</u> - Adds to given meter or asset track.
   
4. <u>lose {meter/asset} {amount}</u> - Subtracts from given meter or asset track.
   
5. <u>move {initials} {stat} {add}</u> - Makes a challenge roll using the given stat and add for the specified move.
   
6. <u>moveref {initals}</u> - Embed the text of a move.
   
7. <u>createprogress</u> - Creates a new progress file for vows, combat tracks, bonds, etc. with popup inputs.
   
8. <u>markprogress {filename} {amount}</u> - Marks progress the given number of times on the track in the given file.
   
9. <u>setprogress {filename} {amount}</u> - Set progress on the track in the given file to the amount of ticks, meaning the amount is out of 40.
   
10. <u>resetlegacies</u> - Brings up popup menus for either resetting all legacies and XP (for starting a new game or character) or resetting individual legacy tracks for when you rollover.
   
11. <u>endprogress {fiilename}</u> - Makes the progress move and Fulfill Your Vow or Take Decisive Action on an objective.
   
12. <u>oracle {question}</u> - Ask a question and then select the oracle to roll from the popup menu.

13. <u>yesno {odds} {question}</u> - Ask the oracle a yes/no question with the given odds.

14. <u>at {question}</u> - Ask a question and get an Action/Theme result as an answer.

15. <u>df {question}</u> - Ask a question and get a Descriptor/Focus result as an answer.

16. <u>ptp</u> - Rolls on the oracle from the Pay the Price move.

17. <u>character</u> - Rolls a full NPC with all the character oracles.

18. <u>faction</u> - Rolls a full faction with all the faction oracles.

19. <u>sector</u> - Rolls a full sector including planets and settlements.

20. <u>settlement</u> - Rolls a full settlement using the settlement oracles.

21. <u>starship {region}</u> - Rolls a full starship using the starship oracles in the given region.

22. <u>creature {environment}</u> - Rolls a full creature using the creature oracles of the given environment.

23. <u>planet {class} {region}</u> - Rolls a full planet of the given class in the given region.

24. <u>derelict</u> - Rolls what you initially see at a derelict.

25. <u>vault</u> - Rolls what you initially see at a precursor vault.

### Details of Individual Commands

1. **starforged_stats.sfile:** This file contains several helper functions that you don't need to worry about as well as the following Inline Scripts commands.
	* **toggle {condition}:** This command will turn on or off a condition such as `Wounded` or `Traumatized`.  I was going to have it be simple checkboxes, but then there was no way to check it off with an Inline Script.  Therefore, they are actually images that live in the frontmatter of the `Conditions` page, and this script flips the image back and forth.
	  
	  <u>Example use:</u> `;;toggle wounded` 
	  
	  <hr>
	* **burnmom:** This command burns your momentum and gives you a callout box to write out what this mechanically changed.
	  
	  <u>Example use:</u> `;;burnmom`
	  
	  <hr>
	* **gain {meter/asset} {amount}:** This command adds to a specific meter like `Health` or an asset's track like a service module's `Integrity` or the `Ammo` or the `Archer` asset.  It will make sure you don't go over the max amount.  You use either the meter name (Health, Spirit, Supply, Wealth, or Momentum) or the asset name (Starship, Glowcat, Archer, etc.).
	  
	  <u>Example use:</u> `;;gain Archer 2` will add 2 Ammo to the Archer asset while `;;gain Momentum 2` will add 2 Momentum.
	  
	  <hr>
	* **lose {meter/asset} {amount}:** The opposite command from above which will make sure you don't go below the minimum amount.  If you need to lose more `Health` than you have, it will give you a message saying that so you can decide if you need to lose more of something else (like `Momentum`) to compensate.
	  
	  <u>Example use:</u> `;;lose momentum 2` will subtract 2 Momentum.

2. **starforged_moves.sfile:** This file sets up shortcuts to use with moves.  The commands are as follows:
	* **getmovename {initials}:** This is a helper function that you will never use directly, but it does explain how you need to reference moves.  To use a move, use it's initials in the command unless it's only a single word move in which case you use the full name.  This shortcut then expands it to the full name.
	  
	  For example, `AYA` will give "Aid Your Ally" while `BATTLE` is necessary for the "Battle" move.  There are a few exceptions which are:
		  - All the "Face Something" moves have the same initials of `FD`.  Therefore, `FD` = Face Danger, `FDEATH` = Face Death, `FDEFEAT` = Face Defeat, and `FDESOLATION` = Face Desolation.
		  - Fulfill Your Vow and Forsake Your Vow are both FYV, so they can be referenced with `FULFILL` and `FORSAKE`.
	  
	  <hr>
	* **move {initials} {stat} {add}:** This command will be used frequently because it's how you perform moves.  The {initials} were explained previously, but now you'll need to also give the {stat} you're using to make the challenge roll and an optional amount to {add} to the move defaulting to 0 if you leave the amount off.  This returns a green callout box for a strong hit, orange for a weak hit, and red for a miss. 
	  
	  <u>Example use:</u> `;;move saa wits 1` will make a challenge roll with Wits and an add of +1 on the Secure An Advantage move.  `;;move compel iron` makes a challenge roll with Iron and no add on the Compel move.
	  
	  <hr>
	* **moveref {initials}:** Embeds a callout box with the text of the referenced move.  This is incase you want to read the text of the move but not open a different note to do so.
	  
	  <u>Example use:</u> `;;moveref fd` will embed the Face Danger move.
	  
	  <hr>
	* **mech:** Embeds a generic mechanics callout box in case you want to detail some mechanical consequence or action.
	  
	  <u>Example use:</u> `;;mech` creates a blue callout box.

3. **starforged_progress.sfile:** This file contains the commands necessary to interact with your vows, bonds, combat tracks, etc.  Anything that uses a progress track should run through these commands.
	* **createprogress:** This command will create a new note in the Progress folder, apply the template, and use your inputs for the filename, name of the vow (or bond or combat), and the difficulty level.
	  
	  <u>Example use:</u> `;;createprogress` will create a new progress track file in the Progress folder.
	  
	  <hr>
	* **markprogress {filename} {amount}:** This command marks progress the given amount of times on the progress track contained within the given file.  You give it the number of times to mark progress, not the number of tick marks.  For example, if you want to mark progress twice on a Dangerous vow, use 2 (progress) and not 16 (the # of ticks marked with two progress of two boxes each).
	  
	  <u>Example use:</u> `;;markprogress PirateFight1 1` will mark progress once filling in the appropriate number of tick marks depending on the Difficulty level of the vow.
	  
	  <hr>
	* **clearprogress {filename} {amount of TICKS}:** This command clears progress the given amount of TICKS on the progress track contained within the given file.  Some moves says clear progress boxes and others say units of progress, so I had to use the number of ticks.  That means if it says to clear four boxes, you would use 16 ticks.  If it's a dangerous vow that says to clear three units of progress, that would be six boxes meaning you would use 24 ticks.
	  
	  <u>Example use:</u> `;;clearprogress PirateFight1 16` will clear 4 boxes of progress on the vow.
	  
	  <hr>
	* **setprogress {filename} {amount}:** With this command you can set any progress track to any value you need.  The amount should be given in the number of ticks.  That means for regular progress tracks it should be out of 40, and for Legacy tracks it should be out of 80.
	  
	  <u>Example use:</u> `;;setprogress Bonds 53` will set the Bonds track to 13 ticks and mark it as rolled over for calculating XP Earned.
	  
	  <hr>
	* **endprogress {filename}:** This command makes the final progress move to Fulfill Your Vow or Take Decisive Action on an objective and it marks the file as a completed vow.
	  
	  <u>Example use:</u> `;;endprogress PirateFight1` will roll the challenge dice and compare them to your current progress.

4. **starforged_oracles.sfile:** This file contains all the shortcuts for rolling oracles.
	* **getoracle {file path}:** This is a helper function that you don't every need to use.  It is used by the other commands but needed to be an actual shortcut rather than a true JS function.  The specific purpose of this function is to check for Action/Theme, Descriptor, or Roll Twice results and then substitute those new oracle results in.
	  
	  <hr>
	* **oracle {question}:** This command allows you to ask a question and then calls up the oracle selection menu where you can select the appropriate oracle.
	  
	  <u>Example use:</u> `;;oracle What is the next zone in the derelict?` will bring of the oracle selection menu where you can select the appropriate Derelict oracle.
	  
	  <hr>
	* **yesno {odds} {question}:** This command is the basic Ask the Oracle move giving a yes/no answer based on the given odds.  Use initials for the odds like AC = Almost Certain, L = Likely, FF = Fifty/Fifty, U - Unlikely, and SC - Small Chance.  This does note results with a match and returns the number value rolled.
	  
	  <u>Example use:</u> `;;yesno FF Will there be hotdogs?` answers the question with a 50/50 likelihood.
	  
	  <hr>
	* **at {question}:** Ask a question and get an Action/Theme response.
	  
	  <u>Example use:</u> `;;at What is the hidden agenda of the faction?` will answer the question by rolling an Action/Theme.
	  
	  <hr>
	* **df {question}:** Ask a question and get a Descriptor/Focus response.
	  
	  <u>Example use:</u> `;;df What is the nature of the advanced technology?` will answer the question by rolling a Descriptor/Focus.
	  
	  <hr>
	* **ptp:** Rolls on the Pay the Price oracle.
	  
	  <u>Example use:</u> `;;ptp` gives a result such as "You are harmed" from the Pay the Price move.
	  
	  <hr>
	* **sector:** Gives a random sector including planets and settlements.
	  
	  <u>Example use:</u> `;;sector` gives a full sector
	  
	  <hr>
	* **character:** Gives a new NPC including the Name, Callsign, First Look, Initial Disposition, Revealed Aspect, Role, and Goal.
	  
	  <u>Example use:</u> `;;character` gives a full NPC
	  
	  <hr>
	* **settlement:** Rolls all the settlement oracles at once.
	  
	  <u>Example use:</u> `;;settlement` gives a full settlement
	  
	  <hr>
	* **faction:** Rolls all the faction oracles at once.
	  
	  <u>Example use:</u> `;;faction` gives a full faction
	  
	  <hr>
	* **starship {region}:** Rolls all the starship oracles using results from the tables within the specific region (Terminus, Outlands, or Expanse).  If you don't type in a region, it defaults to Terminus.
	  
	  <u>Example use:</u> `;;starship` gives a starship in Terminus while the command `;;starship expanse` gives a starship in the Expanse.
	  
	  <hr>
	* **creature {environment}:** Rolls all the creature oracles for the given environment.  If environment is left blank, it randomly rolls the environment first before rolling the rest of the oracles.
	  
	  <u>Example use:</u> `;;creature air` gives a creature using Air as its environment while `;;creature` gives a creature from a random environment.
	  
	  <hr>
	* **derelict:** Rolls the Location/Type, Outer First Look, Condition, and starting Zone of a derelict.
	  
	  <u>Example use:</u> `;;derelict` gives the above listed oracle results.
	  
	  <hr>
	* **vault:** Rolls the Location, Outer First Look, Form, Scale, Material, and Shape of a precursor vault.
	  
	  <u>Example use:</u> `;;vault` gives the above listed oracle results.
	  
	  <hr>
	* **planet {class} {region}:** Rolls the oracles for a planet of the given class in the given region.  Leaving the region blank will default to Terminus.  If you want a random class of planet, type `Random` in the {class} section.
	  
	  <u>Example use:</u> `;;planet vital outlands` gives the oracle results from a Vital planet in the Outlands while `;;planet random expanse` gives a planet of a random class in the Expanse while `;;planet random` gives a planet of a random class in Terminus sine no region is listed at the end of the command.
	  
## Support Folder
This folder contains all the Inline Script shortcut files.  If you are making your own shortcut files, add them here.  The Vault_Info file is also here and should not be moved.

## Templates Folder
The Templates folder is necessary for creating new files with progress tracks.  However, the Oracle, Move, and Asset templates were only used for importing JSON data and can safely be removed.  You can also add any other templates to this file that you would like the Templater plugin to have access to.

## Credits
All Assets, Moves, and Oracles are from [Dataforged](https://github.com/rsek/dataforged), the official Starforged repository. 

Image, Map, and Stethoscope icons from [Lucide](https://lucide.dev/)

All other icons from [game-icons.net](https://game-icons.net) by Delapouite, et. al.

This set-up only works with the [Dataview](https://github.com/blacksmithgu/obsidian-dataview), [Templater](https://github.com/SilentVoid13/Templater), and most importantly: [Inline Scripts](https://github.com/jon-heard/obsidian-inline-scripts) plugins.