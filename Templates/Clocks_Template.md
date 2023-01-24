---
Name: New Name Here
Segments: 6
Progress: 0
tags: incomplete
ClockImage: "[[progress-clock-6-0.svg]]"
---

# `=this.Name`
**Progress:** `=this.Progress` out of `=this.Segments` segments filled

```dataview
LIST without id embed(link(meta(ClockImage).path, "100"))
WHERE contains(file.path, this.file.path)
```

## Notes
