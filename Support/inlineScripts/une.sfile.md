---
obsidianUIMode: preview
---

Shortcuts for UNE: The Universal NPC Emulator.  UNE is an excellent character generation system for tabletop role playing and general storytelling.  It was designed by Zach Best. 
 You can find more info about UNE at its [drivethrurpg page](https://www.drivethrurpg.com/product/134163/UNE-The-Universal-NPC-Emulator-rev).

This shortcut-file has supplementary shortcut files:
__une_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
__
```js
// Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Pick an item from array a.
function aPick(a) { return a[roll(a.length)-1]; }

// Pick an item from array a, weighted by element wIndex of the item.  If theRoll is passed, use that as the roll.
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
```
__
Helper scripts


__
```
^une ?(|[1-5]) ?(|[1-7]) ?(|[1-8])$
```
__
```js
// Combine 2 combo-results:
//     - each needs separate formatting, since separator mustn't have formatting
//     - The first needs the footer dropped, since it's not at the end anymore
//     - The second needs the header dropped, since it's not at the start anymore
return "" +
	expFormat(expUnformat(expand("une character " + $1)), false, false, true) +
	"\n***\n" +
	expFormat(expUnformat(expand("une interact " + $2 + " " +	$3)), true);
```
__
une {randomness: 1 TO 5 (order to chaos), default: 3 (standard)} {relationship to PC: 1 TO 7 (love to hate), default: 4 (neutral)} {demeanor: 1 TO 8 (scheming to prejudiced), default: random} - Runs "une character" and "une interact" together.  {randomness} is a value for "une character".  {relationship to PC} and {demeanor} are values for "une interact".
***


__
```
^une character ?(|[1-5])$
```
__
```js
// Combine 3 results:
//     - remove their exp formatting and line-separated titles
//     - add new exp formatting and inline titles (except for motives)
return expFormat("Character:" +
	"\nidentity: " + expUnformat(expand("une identity"))[1] +
	"\npower: " + expUnformat(expand("une power " + $1))[1] +
	"\nmotives:\n" + expUnformat(expand("une motive"))[1]
);
```
__
une character {randomness: 1 TO 5 (order to chaos), default: 3 (standard)} - Runs "identity", "power" and "motive" together.  {randomness} is a value for "power".


__
```
^une interact ?(|[1-7]) ?(|[1-8])$
```
__
```js
// Combine 3 results:
//     - remove their exp formatting and line-separated titles
//     - add new exp formatting and inline titles
return expFormat("Interact:" +
	"\nmood: " + expUnformat(expand("une mood " + $1))[1] +
	"\nbearing: " + expUnformat(expand("une bearing " + $2))[1] +
	"\nfocus: " + expUnformat(expand("une focus"))[1]
);
```
__
une interact {relationship to PC: 1 TO 7 (love to hate), default: 4 (neutral)} {demeanor: 1 TO 8 (scheming to prejudiced), default: random} - Runs "mood", "bearing" and "focus" together.  {relationship to PC} is a value for "mood".  {demeanor} is a value for "bearing".
***


__
```
^une identity$
```
__
```js
// Data
const table1 =
["SUPERFLUOUS","ADDICTED","CONFORMIST","NEFARIOUS","SENSIBLE","UNTRAINED","ROMANTIC","UNREASONABLE","SKILLED","NEGLECTFUL","LIVELY","FORTHRIGHT","IDEALISTIC","UNSUPPORTIVE","RATIONAL","COARSE","FOOLISH","CUNNING","DELIGHTFUL","MISERLY","INEPT","BANAL","LOGICAL","SUBTLE","REPUTABLE","WICKED","LAZY","PESSIMISTIC","SOLEMN","HABITUAL","MEEK","HELPFUL","UNCONCERNED","GENEROUS","DOCILE","CHEERY","PRAGMATIC","SERENE","THOUGHTFUL","HOPELESS","PLEASANT","INSENSITIVE","TITLED","INEXPERIENCED","PRYING","OBLIVIOUS","REFINED","INDISPENSABLE","SCHOLARLY","CONSERVATIVE","UNCOUTH","WILLFUL","INDIFFERENT","FICKLE","ELDERLY","SINFUL","NAIVE","PRIVILEGED","GLUM","LIKABLE","LETHARGIC","DEFIANT","OBNOXIOUS","INSIGHTFUL","TACTLESS","FANATIC","PLEBEIAN","CHILDISH","PIOUS","UNEDUCATED","INCONSIDERATE","CULTURED","REVOLTING","CURIOUS","TOUCHY","NEEDY","DIGNIFIED","PUSHY","KIND","CORRUPT","JOVIAL","SHREWD","LIBERAL","COMPLIANT","DESTITUTE","CONNIVING","CAREFUL","ALLURING","DEFECTIVE","OPTIMISTIC","AFFLUENT","DESPONDENT","MINDLESS","PASSIONATE","DEVOTED","ESTABLISHED","UNSEEMLY","DEPENDABLE","RIGHTEOUS","CONFIDENT"];
const table2 =
["GYPSY","WITCH","MERCHANT","EXPERT","COMMONER","JUDGE","RANGER","OCCULTIST","REVEREND","THUG","DRIFTER","JOURNEYMAN","STATESMAN","ASTROLOGER","DUELIST","JACK-OF-ALL-TRADES","ARISTOCRAT","PREACHER","ARTISAN","ROGUE","MISSIONARY","OUTCAST","MERCENARY","CARETAKER","HERMIT","ORATOR","CHIEFTAIN","PIONEER","BURGLAR","VICAR","OFFICER","EXPLORER","WARDEN","OUTLAW","ADEPT","BUM","SORCERER","LABORER","MASTER","ASCENDANT","VILLAGER","MAGUS","CONSCRIPT","WORKER","ACTOR","HERALD","HIGHWAYMAN","FORTUNE-HUNTER","GOVERNOR","SCRAPPER","MONK","HOMEMAKER","RECLUSE","STEWARD","POLYMATH","MAGICIAN","TRAVELER","VAGRANT","APPRENTICE","POLITICIAN","MEDIATOR","CROOK","CIVILIAN","ACTIVIST","HERO","CHAMPION","CLERIC","SLAVE","GUNMAN","CLAIRVOYANT","PATRIARCH","SHOPKEEPER","CRONE","ADVENTURER","SOLDIER","ENTERTAINER","CRAFTSMAN","SCIENTIST","ASCETIC","SUPERIOR","PERFORMER","MAGISTER","SERF","BRUTE","INQUISITOR","LORD","VILLAIN","PROFESSOR","SERVANT","CHARMER","GLOBETROTTER","SNIPER","COURTIER","PRIEST","TRADESMAN","HITMAN","WIZARD","BEGGAR","TRADESMAN","WARRIOR"];

// Simply combine a roll of table 1 and a roll of table 2
return expFormat([ "NPC identity:\n", aPick(table1) + " " + aPick(table2) ]);
```
__
une identity - Generates a 2-word description for an NPC.


__
```
^une power ?(|[1-5])$
```
__
```js
// The parameter defaults to 3
$1 = Number($1) || 3;

// Data
const table3 =
[ ["MUCH WEAKER",2,4,5,8,12],["SLIGHTLY WEAKER",10,15,20,25,30],["COMPARABLE",90,85,80,75,70],["SLIGHTLY STRONGER",98,96,95,92,88],["MUCH STRONGER",100,100,100,100,100] ];

// Result is a weighted roll of table3, which weight being defined by the parameter
return expFormat([ "NPC power level:\n", aPickWeight(table3, $1)[0] ]);
```
__
une power {randomness: 1 TO 5 (order to chaos), default: 3 (standard)} - Generates an NPC's power level relative to PC's power level, based on {randomness}.


__
```
^une motive$
```
__
```js
// Data
const table4 =
["ADVISE","OBTAIN","ATTEMPT","SPOIL","OPPRESS","INTERACT","CREATE","ABDUCT","PROMOTE","CONCEIVE","BLIGHT","PROGRESS","DISTRESS","POSSESS","RECORD","EMBRACE","CONTACT","PURSUE","ASSOCIATE","PREPARE","SHEPHERD","ABUSE","INDULGE","CHRONICLE","FULFILL","DRIVE","REVIEW","AID","FOLLOW","ADVANCE","GUARD","CONQUER","HINDER","PLUNDER","CONSTRUCT","ENCOURAGE","AGONIZE","COMPREHEND","ADMINISTER","RELATE","TAKE","DISCOVER","DETER","ACQUIRE","DAMAGE","PUBLICIZE","BURDEN","ADVOCATE","IMPLEMENT","UNDERSTAND","COLLABORATE","STRIVE","COMPLETE","COMPEL","JOIN","ASSIST","DEFILE","PRODUCE","INSTITUTE","ACCOUNT","WORK","ACCOMPANY","OFFEND","GUIDE","LEARN","PERSECUTE","COMMUNICATE","PROCESS","REPORT","DEVELOP","STEAL","SUGGEST","WEAKEN","ACHIEVE","SECURE","INFORM","PATRONIZE","DEPRESS","DETERMINE","SEEK","MANAGE","SUPPRESS","PROCLAIM","OPERATE","ACCESS","REFINE","COMPOSE","UNDERMINE","EXPLAIN","DISCOURAGE","ATTEND","DETECT","EXECUTE","MAINTAIN","REALIZE","CONVEY","ROB","ESTABLISH","OVERTHROW","SUPPORT"];
const table5 =
["WEALTH","HARDSHIP","AFFLUENCE","RESOURCES","PROSPERITY","POVERTY","OPULENCE","DEPRIVATION","SUCCESS","DISTRESS","CONTRABAND","MUSIC","LITERATURE","TECHNOLOGY","ALCOHOL","MEDICINES","BEAUTY","STRENGTH","INTELLIGENCE","FORCE","THE_WEALTHY","THE_POPULOUS","ENEMIES","THE_PUBLIC","RELIGION","THE_POOR","FAMILY","THE_ELITE","ACADEMIA","THE_FORSAKEN","THE_LAW","THE_GOVERNMENT","THE_OPPRESSED","FRIENDS","CRIMINALS","ALLIES","SECRET_SOCIETIES","THE_WORLD","MILITARY","THE_CHURCH","DREAMS","DISCRETION","LOVE","FREEDOM","PAIN","FAITH","SLAVERY","ENLIGHTENMENT","RACISM","SENSUALITY","DISSONANCE","PEACE","DISCRIMINATION","DISBELIEF","PLEASURE","HATE","HAPPINESS","SERVITUDE","HARMONY","JUSTICE","GLUTTONY","LUST","ENVY","GREED","LAZINESS","WRATH","PRIDE","PURITY","MODERATION","VIGILANCE","ZEAL","COMPOSURE","CHARITY","MODESTY","ATROCITIES","COWARDICE","NARCISSISM","COMPASSION","VALOR","PATIENCE","ADVICE","PROPAGANDA","SCIENCE","KNOWLEDGE","COMMUNICATIONS","LIES","MYTHS","RIDDLES","STORIES","LEGENDS","INDUSTRY","NEW_RELIGIONS","PROGRESS","ANIMALS","GHOSTS","MAGIC","NATURE","OLD_RELIGIONS","EXPERTISE","SPIRITS"];

// Helper function - Get the table column of a table index
function tableColumn(index) { return Math.trunc(index / 20); }

// Result is three motives
//     a motive is: (a) a roll on table1 plus (b) a roll on table2 with unique column
let result = [];

// Track noun rolls to make sure they all have unique columns
let priorNounRolls = [];

// Roll the three motives
for (let i = 0; i < 3; i++)
{
	// Fill this with a noun roll with a unique column
	let nounRoll;

	// Find a noun roll with a unique column
	do
	{
		// Pick a random noun roll
		nounRoll = roll(100);

		// Check if the noun roll shares a previous noun roll's column
		let hasAUniqueColumn = true;
		for (let i = 0; i < priorNounRolls.length; i++)
		{
			if (tableColumn(priorNounRolls[i] - 1) === tableColumn(nounRoll - 1))
			{
				hasAUniqueColumn = false;
				break;
			}
		}

		// If we found a noun roll with a unique column, stop searching
		if (hasAUniqueColumn) { break; }
	}
	while (true);

	// Add a new motive (with the uniquely columned noun roll) to the list of motives
	result.push(aPick(table4) + " " + table5[nounRoll-1]);

	// Add the noun roll to the list of prior nouns rolls
	priorNounRolls.push(nounRoll);
}

return expFormat([ "NPC motives:\n", ". " + result.join("\n. ") ]);
```
__
une motive - Generates three 2-word descriptions for an NPC's motivations.
***


__
```
^une mood ?(|[1-7])$
```
__
```js
// Parameter defaults to 4
$1 = Number($1) || 4;

// Data
const table6 = [
	["WITHDRAWN",1,2,3,5,7,11,15],
	["GUARDED",6,8,11,15,18,24,30],
	["CAUTIOUS",16,20,25,30,46,61,69],
	["NEUTRAL",31,40,55,60,76,81,84],
	["SOCIABLE",70,76,82,85,90,93,94],
	["HELPFUL",85,89,93,95,97,98,99],
	["FORTHCOMING",100,100,100,100,100,100,100] ];

// Result is a weighted roll of table6, which weight being defined by the parameter
return expFormat([ "NPC mood:\n", aPickWeight(table6, $1)[0] ]);
```
__
une mood {relationship to PC: 1 TO 7 (love to hate), default: 4 (neutral)} - Generates an NPC's willingness to socialize for this interaction, based on {relationship to PC}.


__
```
^une bearing ?(|[1-8])$
```
__
```js
// Parameter defaults to a random number from 1 to 8
$1 = Number($1) || roll(8)

// Data
const table7a = ["SCHEMING", "INSANE", "FRIENDLY", "HOSTILE", "INQUISITIVE", "KNOWING", "MYSTERIOUS", "PREJUDICED"];
const table7b = [
["INTENT","BARGAIN","MEANS","PROPOSITION","PLAN","COMPROMISE","AGENDA","ARRANGEMENT","NEGOTIATION","PLOT"],
["MADNESS","FEAR","ACCIDENT","CHAOS","IDIOCY","ILLUSION","TURMOIL","CONFUSION","FACADE","BEWILDERMENT"],
["ALLIANCE","COMFORT","GRATITUDE","SHELTER","HAPPINESS","SUPPORT","PROMISE","DELIGHT","AID","CELEBRATION"],
["DEATH","CAPTURE","JUDGMENT","COMBAT","SURRENDER","RAGE","RESENTMENT","SUBMISSION","INJURY","DESTRUCTION"],
["QUESTIONS","INVESTIGATION","INTEREST","DEMAND","SUSPICION","REQUEST","CURIOSITY","SKEPTICISM","COMMAND","PETITION"],
["REPORT","EFFECTS","EXAMINATION","RECORDS","ACCOUNT","NEWS","HISTORY","TELLING","DISCOURSE","SPEECH"],
["RUMOR","UNCERTAINTY","SECRETS","MISDIRECTION","WHISPERS","LIES","SHADOWS","ENIGMA","OBSCURITY","CONUNDRUM"],
["REPUTATION","DOUBT","BIAS","DISLIKE","PARTIALITY","BELIEF","VIEW","DISCRIMINATION","ASSESSMENT","DIFFERENCE"] ];

// Result is based on demeanor and bearing. Demeanor is parameter 1, bearing is a roll
let demeanor = $1
let bearing = roll(10);

// Result is an element from the 2-d table 7b, chosen by demeanor and bearing.
// Also show the demeanor as a text from table7a
let result = table7a[demeanor-1] + " - " + table7b[demeanor-1][bearing-1];

return expFormat([ "NPC bearing:\n", result ]);
```
__
une bearing {demeanor: 1 TO 8 (scheming to prejudiced), default: random} - Generates an NPC's attitude for this interaction, based on {demeanor}: a number defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced


__
```
^une focus$
```
__
```js
// Data
const table8 = [ ["CURRENT_SCENE", 3],["LAST_STORY",6],["EQUIPMENT",9],["PARENTS",12],["HISTORY",15],["RETAINERS",18],["WEALTH",21],["RELICS",24],["LAST_ACTION",27],["SKILLS",30],["SUPERIORS",33],["FAME",36],["CAMPAIGN",39],["FUTURE_ACTION",42],["FRIENDS",45],["ALLIES",48],["LAST_SCENE",51],["CONTACTS",54],["FLAWS",57],["ANTAGONIST",60],["REWARDS",63],["EXPERIENCE",66],["KNOWLEDGE",69],["RECENT_SCENE",72],["COMMUNITY",75],["TREASURE",78],["THE_CHARACTER",81],["CURRENT_STORY",84],["FAMILY",87],["POWER",90],["WEAPONS",93],["PREVIOUS_SCENE",96],["ENEMY",100] ];

// Result is a simple weighted roll
let result = "THE PC'S " + aPickWeight(table8)[0];

return expFormat([ "NPC focus:\n", result ]);
```
__
une focus - Generates an NPC's primary interest for this interaction.
