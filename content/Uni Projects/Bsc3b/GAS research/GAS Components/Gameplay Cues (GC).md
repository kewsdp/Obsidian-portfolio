---
title: Gameplay Tags
draft: false
tags:
---
- Used for cosmetic tasks, such as sound effects and particle effects.
- Come in two classes "Static" and "Actor":
	- "Static" is typically used with "Instant" or "Periodic" [[Gameplay Effects (GE)]], to *"Execute"* a non-instanced, one-off particle effect, e.g. a bullet-hit scuff 
	- "Actor" is usually paired with "Duration" or "Infinite" Effects, these are instanced and thus must use the *"Add"* or *"Remove"* events e.g. sprinting particles around the legs that is looped while the sprinting  Effect is present