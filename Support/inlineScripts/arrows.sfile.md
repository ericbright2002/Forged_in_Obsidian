---
obsidianUIMode: preview
---

Shortcuts for writing various arrows:
- →←↑↓
- ▶◀▲▼
- ⇒⇐⇑⇓
- ↔↕⇔⇕⇄⇅
- ↗↘↖↙
- ↪↩↻↺↝↜


__
```
^(?:arrow right|\-\>)$
```
__
```js
return "→";
```
__
-> - → (RIGHT arrow).
	- Alternative: __arrow right__


__
```
^(?:arrow left|\<\-)$
```
__
```js
return "←";
```
__
<- - ← (LEFT arrow).
	- Alternative: __arrow left__


__
```
^(?:arrow up|\-\^)$
```
__
```js
return "↑";
```
__
-^ - ↑ (UP arrow).
	- Alternative: __arrow up__


__
```
^(?:arrow down|\-v)$
```
__
```js
return "↓";
```
__
-v - ↓ (DOWN arrow).
	- Alternative: __arrow down__
***


__
```
^(?:tri right|\|\>)$
```
__
```js
return "▶";
```
__
 |> - ▶ (RIGHT triangle).
	- Alternative: __tri right__


__
```
^(?:tri left|\<\|)$
```
__
```js
return "◀";
```
__
<| - ◀ (LEFT triangle).
	- Alternative: __tri left__


__
```
^(?:tri up|\|\^)$
```
__
```js
return "▲";
```
__
|^ - ▲ (UP triangle).
	- Alternative: __tri up__


__
```
^(?:tri down|\|v)$
```
__
```js
return "▼";
```
__
|v - ▼ (DOWN triangle).
	- Alternative: __tri down__
***


__
```
^(?:arrow dbl right|\=\>)$
```
__
```js
return "⇒";
```
__
=> - ⇒ (RIGHT double-arrow).
	- Alternative: __arrow dbl right__


__
```
^(?:arrow dbl left|\<\=)$
```
__
```js
return "⇐";
```
__
<= - ⇐ (LEFT double-arrow).
	- Alternative: __arrow dbl left__


__
```
^(?:arrow dbl up|\=\^)$
```
__
```js
return "⇑";
```
__
=^ - ⇑ (UP double-arrow).
	- Alternative: __arrow dbl up__


__
```
^(?:arrow dbl down|\=v)$
```
__
```js
return "⇓";
```
__
=v - ⇓ (DOWN double-arrow).
	- Alternative: __arrow dbl down__
***


__
```
^(?:arrow leftright|\<\-\>)$
```
__
```js
return "↔";
```
__
<-> - ↔ (LEFT / RIGHT arrow).
	- Alternative: __arrow leftright__


__
```
^(?:arrow updown|\^\-v)$
```
__
```js
return "↕";
```
__
^-v - ↕ (UP / DOWN arrow).
	- Alternative: __arrow updown__


__
```
^(?:arrow dbl leftright|\<\=\>)$
```
__
```js
return "⇔";
```
__
<=> - ⇔ (LEFT / RIGHT double arrow).
	- Alternative: __arrow dbl leftright__


__
```
^(?:arrow dbl updown|\^\=v)$
```
__
```js
return "⇕";
```
__
^=v - ⇕ (UP / DOWN double arrow).
	- Alternative: __arrow dbtl updown__


__
```
^(?:arrow left right|\<\/\/\>)$
```
__
```js
return "⇄";
```
__
<//> - ⇄ (LEFT arrow & RIGHT arrow).
	- Alternative: __arrow left right__


__
```
^(?:arrow up down|\^\/\/v)$
```
__
```js
return "⇅";
```
__
^//v - ⇅ (UP arrow & DOWN arrow).
	- Alternative: __arrow up down__
***


__
```
^(?:arrow rightup|\-\^\>)$
```
__
```js
return "↗";
```
__
 -^> - ↗ (RIGHT / UP arrow).
	- Alternative: __arrow rightup__


__
```
^(?:arrow rightdown|\-v\>)$
```
__
```js
return "↘";
```
__
-v> - ↘ (RIGHT / DOWN arrow).
	- Alternative: __arrow rightdown__


__
```
^(?:arrow leftup|\<\^\-)$
```
__
```js
return "↖";
```
__
<^- - ↖ (LEFT / UP arrow).
	- Alternative: __arrow leftup__


__
```
^(?:arrow leftdown|\<v\-)$
```
__
```js
return "↙";
```
__
<v- - ↙ (LEFT / DOWN arrow).
	- Alternative: __arrow leftdown__
***


__
```
^(?:arrow curve right|u\>)$
```
__
```js
return "↪";
```
__
 u> - ↪ (RIGHT curve arrow).
	- Alternative: __arrow curve right__


__
```
^(?:arrow curve left|\<u)$
```
__
```js
return "↩";
```
__
<u - ↩ (LEFT curve arrow).
	- Alternative: __arrow curve left__


__
```
^(?:arrow clock|c\>)$
```
__
```js
return "↻";
```
__
c> - ↻ (CLOCKWISE arrow).
	- Alternative: __arrow clock__


__
```
^(?:arrow cclock|\<c)$
```
__
```js
return "↺";
```
__
<c - ↺ (COUNTER-CLOCKWISE arrow).
	- Alternative: __arrow cclock__


__
```
^(?:arrow wavy right|\~\>)$
```
__
```js
return "↝";
```
__
\~> - ↝ (RIGHT wavy arrow).
	- Alternative: __arrow wavy right__


__
```
^(?:arrow wavy left|\<\~)$
```
__
```js
return "↜";
```
__
<\~ - ↜ (LEFT wavy arrow).
	- Alternative: __arrow wavy left__
