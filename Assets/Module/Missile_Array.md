---
Ammo: 5
Max: 5
---
# Missile Array
### Module


## Abilities:
- [ ] Your missile array is armed with 5 ammo. When you [Strike](Moves/combat/strike) or [Clash](Moves/combat/clash) with a missile attack, suffer -1 ammo and mark progress on a hit. If you [Resupply](Moves/recover/resupply) in a place where your missiles can be replenished, you may exchange any earned +supply for +ammo.

- [ ] When you have at least 1 ammo and [Gain Ground](Moves/combat/gain_ground) by locking a missile on target, add +1 and take +1 momentum on a hit.

- [ ] When you have at least 3 ammo and [Take Decisive Action](Moves/combat/take_decisive_action) by unleashing all of your missiles, roll an action die before making the progress roll. If your action die is equal to or less than ammo, you may reroll any challenge dice. Then, set ammo to 0.

## ammo: (5) 
```dataview
TABLE Ammo, Max
FROM "Assets/Module/Missile_Array"
```