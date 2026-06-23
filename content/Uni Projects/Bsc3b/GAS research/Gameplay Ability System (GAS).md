---
title: Gameplay Ability System(GAS)
draft: true
tags:
---
## What is GAS?

"A framework for building attributes, abilities and interactions that an Actor can own and trigger"
([Official GAS documentation by Epic Games](https://dev.epicgames.com/documentation/en-us/unreal-engine/gameplay-ability-system-for-unreal-engine?application_version=5.6))

## Important Considerations

### ASC Replication Mode

Relies on the project type:

- **FULL** : Should be used for Single Player experiences, as the Effects are replicated to every client
- **MIXED** : Should be used for Multiplayer, player controlled actors, as Effects are only replicated to the owning client. Other Clients are replicated Tags and Cues (Used for [[A Minute to Win It Overview]])
- **MINIMAL** : Should be used for Multiplayer, AI controlled actors, as Effects are never replicated, but Tags and Cues are replicated to everyone

### Where to store the [[Ability System Component (ASC)]]?

Also dependent on the project, typically apply via the following conventions:

**Player State**: If data needs to be **retained** by the ASC after the character dies.
**Player Character**: If data needs to be **lost** by the ASC after the character dies.

### Where to store individual item Attributes?

- As **Plain Floats** on the item:
	- This solution avoids problems that may arise when storing Attribute Sets on the item, but cannot use the GAS workflow as effectively and requires workarounds to be calibrated for systems like 'Cost Effects' to work.
- As an **Attribute Set** on the item:
	- With this solution, the Set is added to the player's ASC upon pickup. This can use the GAS workflow optimally, however each item type needs its own Attribute Set Class, as changes to Attributes look for the initial instance of a Set on an ASC, and only apply to that.
	- This also means you can only have one of each item type in the player's inventory.
	- Removing an Attribute Set can have negative repercussions.
- ~~ASC on the item~~:
	- An untested approach that has an unknown engineering cost, making it unviable for smaller and quicker projects.

### Attribute BaseValue vs CurrentValue

Attributes have two individuals components:
	- **BaseValue**: refers to the value without any effects, that can only be permanently changed by Instant Gameplay Effects.
	- **CurrentValue**: refers to the value with effects, that can be changed by Duration and Infinite Gameplay Effects

BaseValue should not be treated as a Maximum or Minimum value, these should instead be separate attributes themselves. 