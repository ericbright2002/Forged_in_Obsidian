# Bill Clayson - Cooter
![[Baseball-by-Choonhachat.jpg|200]]
Maybe put an image here or something.

## Backstory
Awesome stuff about my character.

## Stats and Meters
```dataview
TABLE Edge, Heart, Iron, Shadow, Wits
FROM #stats
```
```dataview
TABLE Health, Spirit, Supply, Wealth, Momentum
FROM #meters
```
## CONDITIONS
### Misfortunes
```dataview
TABLE WITHOUT ID Wounded, Shaken, Unprepared
FROM #conditions 
```
### Lasting Effects
```dataview
TABLE WITHOUT ID Harmed, Traumatized
FROM #conditions 
```
### Burdens
```dataview
TABLE WITHOUT ID Doomed, Tormented, Indebted
FROM #conditions 
```

## Legacies
```dataview
TABLE embed(link(meta(TrackImage).path, "350")) AS Progress
FROM #legacy
```
```dataview
TABLE XPEarned, XPSpent
FROM #legacy 
```
## Vows IN-PROGRESS
```dataview
TABLE Name, embed(link(meta(TrackImage).path, "350")) AS Progress
FROM #incomplete WHERE file.name != "Progress_Template" 
```

## Assets
![[Sniper]]

![[Scoundrel]]

![[Scavenger]]



