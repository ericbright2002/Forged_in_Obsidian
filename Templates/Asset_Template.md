# {{Name}}
### {{Category}}
{{#if Input.length}}{{#each Input}}### {{Label}}:<hr>
{{/each}}{{/if}}

## Abilities:
{{#each Abilities}}
<input type="checkbox" />{{Text}}

{{/each}}
{{#with [Condition meter]}}## {{Label}}: ({{Max}}) {{{table Max 1 '<input type="checkbox" />' 2 '<input type="checkbox" /><input type="checkbox" />' 3 '<input type="checkbox" /><input type="checkbox" /><input type="checkbox" />' 4 '<input type="checkbox" /><input type="checkbox" /><input type="checkbox" /><input type="checkbox" />' 5 '<input type="checkbox" /><input type="checkbox" /><input type="checkbox" /><input type="checkbox" /><input type="checkbox" />' 6 '<input type="checkbox" /><input type="checkbox" /><input type="checkbox" /><input type="checkbox" /><input type="checkbox" /><input type="checkbox" />'}}}{{#if Conditions.length}}

## Conditions:
<ul>{{#each Conditions}}<li><input type="checkbox" /> {{ this }}</li>{{/each}}</ul>{{/if}}
{{/with}}