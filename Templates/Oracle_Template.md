---
startLine: 4
itemFormat: \| [0-9]+-(?<range>[0-9]+) \| (?<item>.*) \|
---
# {{Name}}
### Category: {{Category}}

| dice: 1d100 | Result |
|:----:|:-------|
{{#each Table}}| {{Floor}}-{{Ceiling}} | {{Result}} |
{{/each}}