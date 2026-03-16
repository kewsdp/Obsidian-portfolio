---
title: Item Widget
draft: false
tags:
  - BitmapBygones
---
## Construction

<iframe src="https://blueprintue.com/render/row4eesh/" scrolling="no" width="800" height="600"  allowfullscreen></iframe>

[[ItemWidgetBlueprint.png | CLICK HERE if the above Blueprint does not load!]]

Upon creation of our Item Widget, we create separate media objects for use in a new Dynamic Material Instance (DMI) , made from a pre-existing Material Blueprint:

![[Bitmap material.png]]

We then assign the DMI as the Brush for our Widget Image, and use the relevant Media Source from the Data Table to match the newly added item. If I were to change this code, I would probably create the necessary resources (Media Player, Media Texture and Material Instance) prior to implementation, rather than at runtime, and assign a Material Instance within the Data Table. I believe this would benefit performance and keep the code more concise, avoiding any issues with forgetting to delete new objects. Furthermore, playing the Media Player after creation is unnecessary as Media Player's are paused when the inventory is closed anyway, in addition the options when opening "Source Latent" can be toggled to "Play on Open" regardless.