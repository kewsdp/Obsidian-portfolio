---
title: Inventory Widget
draft: false
tags:
  - BitmapBygones
---
## Add Item

<iframe src="https://blueprintue.com/render/uu8yga0t/" width="800" height="600" scrolling="no" allowfullscreen></iframe>
Link: [BitMapBygones: Inventory AddItem posted by Kewsdp | blueprintUE | PasteBin For Unreal Engine](https://blueprintue.com/blueprint/uu8yga0t/)

This function is called upon pickup of items, and will loop through the game instance's inventory array to confirm which [[Item Widget]] has been added, and add them if they are not present. I would probably replace this function with one that either adds only the last item of the array, or passes the new item enum through the interface if I were to improve it, as looping is rather unnecessary in hindsight.

## Remove Item

<iframe src="https://blueprintue.com/render/1ukmzxek/" scrolling="no" width="800" height="600" allowfullscreen></iframe>

The above function is called by the NPCs to receive items from the player to complete quests. Initially, the item border and additional inventory Visual Data are toggled off. The player then unequips the item, which is followed by the removal of the item from the Game Instance inventory. Then, the associated Item Widget is also removed from the Inventory Widget and the current Item Slot being hovered over is reset. Finally, the size of the Inventory array is checked and the newly hovered slot's border and visual data are toggled back on, unless the inventory is otherwise empty.

To improve, I would collapse the Adding and Removing of Inventory items to a single function, possibly using a Boolean input similarly to how my teammate created the "Equip/unequip Item" function. And since every item within the final game could be equipped anyway, the currently equipped item could have been passed as the item to be removed, assuming it was equipped when talking to the corresponding quest NPC. This would remove the need for the integer input.
## Inventory Navigation

<iframe src="https://blueprintue.com/render/2yx8-_v6/" width="800" height="600" scrolling="no" allowfullscreen></iframe>

This code navigates between inventory slots and updates the value of "Currently Hovering" so it can be used for item equipping. The item slot borders and visual information are updated according to the newly selected slot. To improve, I would change this to an interface function that is sent through the player blueprint's input but only when the inventory is open, rather than also checking inputs in the widget.

## Setting Item Visual Data

<iframe src="https://blueprintue.com/render/bh5bycvg/" width="800" height="600" scrolling="no" allowfullscreen></iframe>

The above function first sets the visibility of the widget components accordingly. Then it uses a similar process to how the [[Pickup-able Items]] sets it's Mesh, but instead for setting the visual data for the item, like the Close-up image and the description. For improvement, I would separate the toggling of visibility from this function to make the code more concise.
## Open/Close Inventory

<iframe src="https://blueprintue.com/render/n540v2e6/" width="800" height="600" scrolling="no" allowfullscreen></iframe>

Here, I managed toggling the Inventory visibility, since the Inventory is always active but is hidden when not explicitly toggled. I also implemented a method to prevent constantly rendering the rotating item's media files, by pausing and unpausing their media source upon opening and closing the inventory. However, this feature caused bugs in the final build, such as the media becoming permanently paused.

If I were to improve, I would also add a Boolean input to toggle visibility. This would most likely reduce bugs and increase readability.
## Interaction

<iframe src="https://blueprintue.com/render/uzbsnxf5/" width="800" height="600" scrolling="no" allowfullscreen></iframe>

This admittedly confusing function is activated upon using the interact button. It checks the Inventory Widget's visibility and executes accordingly. Through a series of branches, it then determines if the inventory item being interacted with is: being **equipped with no other currently equipped**, being **unequipped** or being **equipped with another currently equipped**. Then the equipped data, the border indicating equipped and the actual equipping functionality of the items is managed accordingly.

Similarly to the "Inventory Navigation", in hindsight, I would make this an interface and execute it through input in the player blueprint, to avoid checking input twice. I would also switch the "Currently Equipped" struct to just an enum (adding a "none" enum value to avoid the additional "Equipped?" boolean), and collapse "Set Border" and "Deselect Equipped Borders" to a single function.
## Result

Although the final version encountered some glitches, causing the videos of the rotating inventory items to not render, here is what the result was before packaging:

![[Inventory example better.mp4]]