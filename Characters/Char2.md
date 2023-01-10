---
Name: Mary Sue
Edge: 2
Heart: 1
Iron: 2
Shadow: 3
Wits: 1
Health: 5
Spirit: 5
Supply: 5
Momentum: 4
Wealth: 0
Wounded: ⬡
Shaken: ⬡
Unprepared: ⬡
Harmed: ⬢
Traumatized: ⬡
Doomed: ⬡
Tormented: ⬡
Indebted: ⬡
XPSpent: 0
Bonds_Progress: 53
Bonds_TrackImage: "[[progress-track-13.svg]]"
Bonds_XPEarned: 23
Discoveries_Progress: 0
Discoveries_TrackImage: "[[progress-track-0.svg]]"
Discoveries_XPEarned: 0
Quests_Progress: 0
Quests_TrackImage: "[[progress-track-0.svg]]"
Quests_XPEarned: 0
---
# `=this.Name`
Maybe image here

## Stats
| Edge | Heart | Iron | Shadow | Wits |
| --- | --- | --- | --- | --- |
| `=this.Edge` | `=this.Heart` | `=this.Iron` | `=this.Shadow` | `=this.Wits` |

## Meters
| Health | Spirit | Supply | Wealth | Momentum |
| --- | --- | --- | --- | --- |
| `=this.Health` | `=this.Spirit` | `=this.Supply` | `=this.Wealth` | `=this.Momentum` |

## Impacts
| Misfortunes | Lasting Effects | Burdens |
| --- | --- | --- |
| `=this.Wounded` Wounded | `=this.Harmed` Permanently Harmed | `=this.Doomed` Doomed |
| `=this.Shaken` Shaken | `=this.Traumatized` Traumatized | `=this.Tormented` Tormented |
| `=this.Unprepared` Unprepared |  | `=this.Indebted` Indebted |

## Legacies
| XP Earned | XP Spent |
| --- | --- |
| `=this.Bonds_XPEarned+this.Discoveries_XPEarned+this.Quests_XPEarned` | `=this.XPSpent` |
### Bonds (Rolled Over? `=choice(this.Bonds_Progress > 40, "Yes", "No")`)
```dataview
LIST without id embed(link(meta(Bonds_TrackImage).path, "350"))
WHERE contains(file.path, this.file.path)
```
### Discoveries (Rolled Over? `=choice(this.Discoveries_Progress > 40, "Yes", "No")`)
```dataview
LIST without id embed(link(meta(Discoveries_TrackImage).path, "350"))
WHERE contains(file.path, this.file.path)
```
### Quests (Rolled Over? `=choice(this.Quests_Progress > 40, "Yes", "No")`)
```dataview
LIST without id embed(link(meta(Quests_TrackImage).path, "350"))
WHERE contains(file.path, this.file.path)
```
## Vows / Progress Tracks
```dataview
TABLE Name, embed(link(meta(TrackImage).path, "150")) AS Progress
FROM #incomplete WHERE file.name != "Progress_Template" 
```
## Assets
Embed assets here like this:
![[Sniper]]