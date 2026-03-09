---
title: Pickup-able Items
draft: false
tags:
  - BitmapBygones
---
## Picking Up

<iframe src="https://blueprintue.com/render/zra7pwbu/" width="800" height="600" scrolling="no" allowfullscreen></iframe>

This code is executed when interacting with "Pickup-able items", it adds items to the Game Instance and executes corresponding UI functions to visually update the inventory. To improve, I would also check if the Inventory is full, prior to adding the item. This could be achieved with a "MaxInventorySize" integer within the Game Instance. Furthermore, I would remove the "Empty" Boolean branch as the loop is only initiated if the Inventory Array is populated anyway, therefore it should never return true.

## Set Mesh
<iframe src="https://blueprintue.com/render/8e_6zamp/" width="800" height="600" scrolling="no" allowfullscreen></iframe>

Here, we use an instance editable enum to set the correct static mesh on "BeginPlay", searching the relevant data table for the assigned mesh. However, I would probably change the method of row identification used here. Since the enum is being converted to a "Literal Byte" the integer assigned to the enum value is being used to search the data table. Instead, I would make each Row Name match the Object Enum specified.

## Pickup-ables Data Table

![[Bitmap Data table.png]]

Above, we can see the table I used to store item data, allowing for easy front-end changes. We've seen the implementation of the Mesh value within the Pickup-ables code, and the other columns are used within the [[Inventory Widget]]. The "Interactable" variable however, ended up going un-used, but would specify what items could be used through the inventory, e.g. playing the flute. This was scrapped in the final build, since all items could be selected regardless.

## Outcome

Within the final build, no objects are available to be picked up directly, instead they are received from NPCs or fishing. Therefore, the above code was instead moved into the relevant task Blueprints, by my teammates. However, they still function similarly:

![[Pickup example 1.mp4]]

![[Pickup example 2.mp4]]