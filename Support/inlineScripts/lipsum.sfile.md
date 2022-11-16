---
obsidianUIMode: preview
---

A decent Lorum Ipsum generator.


__
```
^sfile setup$
```
__
```js
// Only do this once
if (window._inlineScripts?.lipsum) { return; }

// Data to make output text from
let l = `

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Vestibulum et sem quam. Proin viverra egestas sem, et finibus mi volutpat eget. Aliquam vel nulla mattis, malesuada lectus sed, sagittis eros. Etiam vulputate, felis nec aliquam tempor, velit ex lobortis nisi, ac aliquet magna arcu id velit. Nam odio ligula, consequat eget elit non, accumsan rutrum est. Mauris vitae ligula nec lacus finibus venenatis at molestie ligula. Ut suscipit ex et finibus gravida.

Phasellus orci neque, dictum at dui vel, ultricies varius sapien. Praesent ultricies ultrices mi eget luctus. Integer porttitor purus sit amet ante luctus mollis. Nulla lorem diam, fringilla in rhoncus quis, vestibulum lacinia dolor. Nam finibus eros ut nisl pellentesque euismod. Nunc nec ipsum in purus dapibus cursus eu eu ex. Ut nec mauris neque. Sed eget enim ut velit laoreet vehicula quis quis sem.

Nullam pulvinar hendrerit ipsum, nec gravida neque aliquam at. Suspendisse justo metus, rutrum ac ligula sed, varius rutrum sapien. Etiam porttitor arcu massa, non laoreet eros sodales a. Integer et est et dolor maximus feugiat. Nam tristique purus a nibh fringilla, sit amet porta libero dictum. Quisque rhoncus pharetra cursus. Pellentesque sed rutrum sapien, nec pretium nisi. Aenean vitae quam sit amet nisl venenatis congue ut non libero. Donec sit amet mattis tellus, et condimentum justo. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia curae; Maecenas tristique est nibh, ac dignissim quam pretium in. Nam semper odio eget tellus tempus, eu vestibulum tortor tempor. Curabitur elit ex, posuere nec diam eget, malesuada blandit arcu. Proin condimentum arcu sed lorem consectetur convallis. Sed facilisis convallis lorem, ut fermentum nulla pretium vel.

Mauris pulvinar lectus eu mi vehicula, ut dapibus velit egestas. Nullam pellentesque a nibh quis sodales. Donec lacinia vulputate est. Vivamus lacinia neque a libero faucibus, sed condimentum dui imperdiet. In dignissim aliquet tellus a finibus. Cras suscipit tempus sagittis. In lobortis arcu quis ante sagittis cursus. Vestibulum vitae lectus sit amet felis placerat pulvinar ac id sapien. Suspendisse congue, sem vitae consectetur pharetra, libero metus congue quam, quis finibus felis eros eu arcu.

Vestibulum sagittis tristique lectus sit amet accumsan. Sed laoreet lectus eu euismod scelerisque. In velit dolor, placerat id rhoncus aliquam, semper in lectus. Curabitur ut ante congue, ullamcorper ante vitae, accumsan quam. Phasellus ante leo, pulvinar eu leo quis, convallis elementum nisi. Praesent et imperdiet metus, sed aliquet arcu. Phasellus efficitur vehicula libero, quis efficitur arcu. Nulla maximus tellus non luctus auctor.

Aliquam gravida condimentum nisl, id aliquam ipsum efficitur id. Quisque et mauris ac purus accumsan porta vel sit amet lectus. Duis viverra accumsan risus, nec fringilla ipsum posuere in. Donec dapibus nisl at congue facilisis. Nam malesuada accumsan dapibus. Vestibulum rhoncus ornare aliquam. Mauris viverra, diam sit amet fermentum venenatis, tortor leo ultrices massa, vel lobortis neque urna eget felis. Aliquam viverra erat ac dolor sagittis, nec aliquet lorem blandit. Orci varius natoque penatibus et magnis dis parturient montes, nascetur ridiculus mus. Etiam molestie pellentesque laoreet. Duis nulla eros, lobortis vitae elit in, maximus facilisis felis. Proin viverra venenatis eros, nec consequat dui euismod eu.

Nam vestibulum turpis ac lorem luctus, ut faucibus leo vehicula. Praesent laoreet nibh turpis, id tincidunt mauris ultricies ut. Integer euismod turpis sit amet nulla iaculis consequat. Vivamus cursus arcu ac sem luctus egestas. Integer varius lorem augue, ac consequat neque vehicula sit amet. Aliquam eu orci congue, porttitor est vitae, tincidunt quam. Phasellus vehicula, quam id ultricies aliquet, dolor risus tincidunt leo, sit amet eleifend nisi ex ut erat. Praesent porta vehicula nisl, ac feugiat dui viverra in. Nunc vehicula sagittis tortor, eget commodo erat hendrerit ac. Praesent accumsan laoreet nisi vitae faucibus. Proin in turpis id ante suscipit sodales.

Vestibulum accumsan dignissim metus non cursus. Sed pulvinar imperdiet arcu, a condimentum ipsum sodales sit amet. Integer eu dapibus lacus. Duis id ligula et nunc scelerisque sagittis eget sed libero. Nulla id justo varius, pellentesque augue et, egestas sem. Donec cursus ligula in hendrerit suscipit. Nullam laoreet justo bibendum, mollis nibh sed, egestas ipsum. Aenean volutpat erat velit, vel placerat mauris efficitur eu. Aenean quam arcu, vehicula non commodo id, blandit a magna. Cras ac sem dolor. Nam tempor odio dictum, consequat augue in, finibus risus. Nunc elementum leo at maximus scelerisque. Nullam at feugiat ipsum, quis facilisis dolor.

Aliquam nec sapien urna. Aliquam bibendum, ligula id lobortis elementum, odio nulla pretium justo, vitae maximus dolor elit vitae risus. Pellentesque suscipit mi eget augue hendrerit tincidunt. Phasellus ut pulvinar mi. Pellentesque euismod lobortis fermentum. Aliquam vitae lacus sit amet sapien malesuada tempus. Proin iaculis nunc sed ullamcorper rutrum. Nullam non interdum elit. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis ac metus at tortor luctus vehicula id at dolor. Nulla pulvinar ornare venenatis. Vivamus a ultricies mauris, et maximus enim. Mauris blandit tellus id lacus placerat, ut blandit purus ultrices.

Ut pharetra semper leo at convallis. Vestibulum ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia curae; Aenean vulputate, ex in imperdiet pharetra, leo libero mattis nunc, at tristique turpis libero ac lectus. Suspendisse potenti. Integer sed dolor at urna ultricies ornare. Ut auctor vulputate diam, sit amet condimentum lectus mattis at. Aenean placerat molestie arcu at pretium. Donec consequat nibh nulla, vel mollis lacus tincidunt et.

`

// Break data into pieces to recombine for output text
let pSizes = [];
let sentences = [];
l = l.trim().split("\n\n");
for (let i = 0; i < l.length; i++)
{
	l[i] = l[i].slice(0,-1).split(". ");
	pSizes.push(l[i].length);
	sentences = sentences.concat(l[i]);
}
let first = sentences.shift();
window._inlineScripts ||= {};
window._inlineScripts.lipsum =
	{ first: first, pSizes: pSizes, sentences: sentences };
```
__
Takes a source Lorem Ipsum text, generated at https://lipsum.com/feed/html, and breaks it down into individual sentences and counts of sentences per paragraph.  On user request, builds a Lorem Ipsum text of random sentences from the source, combined into paragraphs of random size based on the source's paragraphs.


__
```
^lipsum ?(|[1-9][0-9]*)$
```
__
```js
// Count parameter defaults to 1
$1 = Number($1) || 1;

// Helper function - Make a roll from 1 to max.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

// Helper function - Pick an item from array a.
function aPick(a) { return a[roll(a.length)-1]; }

// Generate and return text from the data created on setup
let result = "";
for (let i = 0; i < $1; i++)
{
	let pSize = aPick(window._inlineScripts.lipsum.pSizes);
	if (i === 0)
	{
		pSize--;
		result += window._inlineScripts.lipsum.first + ". ";
	}
	else
	{
		result += "\n\n";
	}
	for (let k = 0; k < pSize; k++)
	{
		result += aPick(window._inlineScripts.lipsum.sentences) + ". ";
	}
	result = result.slice(0,-1);
}
return result;
```
__
lipsum {paragraph count: >0, default: 1} - Generates a lorem ipsum text with {paragraph count} paragraphs.
