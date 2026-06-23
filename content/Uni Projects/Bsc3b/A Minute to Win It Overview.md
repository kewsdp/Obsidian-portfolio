---
title: A Minute to Win It Overview
draft: false
tags:
  - A-Minute-To-Win-It
  - Gameplay-Ability-System
  - Data-Driven-Gameplay
  - UI
  - Replication
---
## Overview

My initial idea for this project was a 1v1 multiplayer shooter, set within an arena with several pickup-able weapons scattered around. The player could only hold one weapon at a time, and the main concept involved weapon stats **changing every minute**.

The aim with this project was to explore the, at the time, currently relevant, deteriorating developer communication with their respective playerbases. Within my research I listed a few examples, such as the 'review bombing' of Helldivers 2 in response to the implementation of a required PlayStation Network account to play:

![[SteamDB Helldivers.png]]
(Helldivers 'review bombing' represented through SteamDB within an [IGN article](https://www.ign.com/articles/arrowhead-ceo-apologizes-amid-helldivers-2-review-bombing))

Thus, the random weapon changes within my game were designed to represent a developer manipulating the game's "meta" (the most successful ways to play), with minimal regard for the active players. This would also allow me to observe how player's responded to these changes in "meta", depicting the power some ignorant developers hold over their communities. 

I also had several stretch goals relating to the concept, such as an item shop meant to emulate the effect of 'Pay To Win' items (without the in-game transactions), examples being, a skeleton outfit with an accurate hitbox, providing an unfair advantage to players with more points.

## MoSCoW

I began planning by breaking down my goals with a 'MoSCoW' diagram:

| Must Have                        | Should Have                                           | Could Have                                              | Won't Have        |
| -------------------------------- | ----------------------------------------------------- | ------------------------------------------------------- | ----------------- |
| Client-hosted server             | Easy to read stat change descriptions ("Patch Notes") | Event foreshadowing (with mis-leading descriptions)     | Dedicated servers |
| Synchronized gameplay            | Randomised weapon spawns                              | Point-based, cosmetics shop                             | Multiple Modes    |
| Point-based win condition        | Smart level design                                    | Narrator                                                |                   |
| Updating item stats every minute |                                                       | Updating Game Mechanics every minute (e.g. low gravity) |                   |
| Reasonable variety of weapons    |                                                       |                                                         |                   |
| Weapon Spawn Points              |                                                       |                                                         |                   |
## Initial coding research

During my previous mid-unit review, when I originally pitched this idea to a lecturer, I was suggested to [[Lyra Research|research Lyra]] as a point of reference for a widely renowned UE5 shooter project. Within my Lyra analysis, I also discovered that the project uses the [[tags/Gameplay-Ability-System]] (GAS) plugin. This plugin seemed to simplify some aspects of the Third Person Shooter development cycle, as well as being a suitable fit for future projects I planned on making, therefore I decided to use it to create my game.

## Development process

By far the biggest hurdle within this project was configuring the [[Stat Randomisation Walkthrough|randomisation of weapon stats]], and I probably wouldn't have completed it without [Tranek's unofficial "GASDocumentation"](https://github.com/tranek/GASDocumentation). This resource helped with several other parts of the development, such as managing the [[Attribute Change UI|UI updates for Attribute changes]] and I would encourage you to read it if you plan on using the GAS plugin. Setup of the Multiplayer matches and establishing the win condition came towards the end of development.

## Final product

The final result was a LAN, free-for-all, Third Person Shooter, that supports up to 4 players. Featuring randomly generated stat changes every minute, with customizable parameters for the host. Fixed weapon spawns are located around the map and players can only hold one weapon at a time. Players must reach a score goal, also customizable, by eliminating each other, under which circumstances they can spectate until they respawn.

## Reflection on the project

My shift in focus towards learning about GAS and Multiplayer systems, rather than commentating on poor developer-player relations, was regrettable but necessary for the complex nature of the project. Furthermore, the prospect of using this system in future, anticipated, games personally outweighed my desire to speak on these topics.

Here are some of the changes that arose during development:

- (+) Added customizable amount of: changes per weapon, weapons to be changed and score to win
- (+) Increased player count from original 1v1 style to a 4 player maximum
- (+) Added spectator cam when waiting to respawn
- (-) Less priority on researching good level design
- (-) Fixed weapon spawns rather than randomized
- (-) Minimal testing due to time constraints

In addition, all "Could Have" entries to the MoSCoW diagram were left out of the final build.

In the future I would like to revisit this project, with an advanced knowledge of these systems, and shape it towards my original goal, so it can serve as an example of my technical prowess and personal opinions regarding the Industry.