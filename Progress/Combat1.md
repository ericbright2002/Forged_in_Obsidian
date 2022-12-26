---
Name: Space Pirates Attack!
Difficulty: Formidable
Progress: 24
tags: incomplete
TrackImage: "[[progress-track-24.svg]]"
---

# `=this.Name`
**Difficulty:** `=this.Difficulty`

```dataview
LIST without id embed(link(meta(TrackImage).path, "350"))
WHERE contains(file.path, this.file.path)
```


## Notes

These space pirates might be from a faction, so I've used an Inline Script command to roll the oracles for a faction as follows:

;;faction

> [!oracle]- Faction: Exalted Republic
> **Dominion:** Diplomacy
> **Influence:** Dominant
> **Leadership:** Disputed leadership
> **Quirk:** Conceals individual identity
> **Project:** Disrupt the operations of a rival
> **Rumor:** Suffering a shortage of key workers or personnel

