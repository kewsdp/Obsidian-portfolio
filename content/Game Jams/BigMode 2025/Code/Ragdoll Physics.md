---
title: NPC Ragdoll Physics
draft: false
tags:
  - PoundTown
---
## Managing Ragdolls

I ran into issues where the "change health" function would only call if the enemy had a capsule and not when hitting only their bones collision boxes, however with the capsule, the bones would not be hit. This was a problem as I wanted to specify which bone was hit to add impulse on that bone when becoming a ragdoll. To quickly combat this I disabled the capsule and enabled the bone collisions on hit and used this function:

```gdscript wrap
#function to launch ragdoll on hit
func launchragdoll():
	#retrieve the GunSystem component from the player
	for child in Global.player_ref.get_children():
		if child.name == "GunSystem":
			#force a sequential raycast after the bullet hits
			var raycast = child.get_bullet_raycasts()
			#bool to check bones have been hit
			var hitbones = false
			#if raycast hits bone
			for cast in raycast:
				if cast["hit_target"].get_class() == "PhysicalBone3D":
					#apply impulse on the bones location
					cast["hit_target"].apply_impulse(-Global.player_ref.basis.z * 50.0, cast["hit_target"].global_position)
					hitbones = true
			#if no bone impulse, add random impulse 
			if hitbones == false:
				randomlaunch()
				return
```

This function forces another raycast that will hit the bones and apply an impulse onto them. If no bones are hit, we use the 'randomlaunch()' function:

```gdscript wrap
#pick a random bone and add impulse (executes when no bone is hit)
func randomlaunch():
	var launch = false
	#while a force hasn't been applied, as there are other child nodes apart from bones
	while launch == false:
		var bones = skeleton.get_children()
		var randombone = skeleton.get_child(randi_range(0, bones.size() - 1))
		if randombone.get_class() == "PhysicalBone3D":
			randombone.apply_impulse(-Global.player_ref.basis.z * 50.0, randombone.global_position)
			#only true once a bone has been hit
			launch = true
```

The above function applies an impulse on a random bone. We use a while loop to eject once a bone has been found, as the skeleton also contains other child nodes like the NPC mesh.