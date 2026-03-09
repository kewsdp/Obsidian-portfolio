---
title: NPC Visibility
draft: false
tags:
  - PoundTown
---
## NPC Vision

```gdscript wrap
#check overlapping bodies on vision
func check_vision_overlap():
	var overlaps = VisionRef.get_overlapping_bodies()
	if overlaps.size() > 0:
		for overlap in overlaps:
			#confirm its the player
			if overlap.name == "Player":
				#set playerref for following after lostsight
				PlayerRef = overlap
				#update raycast with player location
				return _TrackPlayerRaycast()
	return false
```

This function simply checks overlaps with the NPCs vision cone:

![[Godot pawn sensing 1.png]]

This works similarly to the UE5 "pawn sensing" component, but is a little more complicated to setup in Godot. We use the information gathered here for this next function:

```gdscript wrap
#manage enemy line of sight
func _TrackPlayerRaycast():
	#raycast locks to them
	RayCast.look_at(PlayerRef.global_transform.origin, Vector3.UP)
	#force raycast to update
	RayCast.force_raycast_update()
	#use forced raycast to check collider
	if RayCast.is_colliding():
		var collider = RayCast.get_collider()
		#if raycast can see player e.g. not hidden AND the NPC is hurt
		if collider.name == "Player" and health != 100:
			RayCast.debug_shape_custom_color = Color.RED
			#stop timers if active
			IdleTimer.stop()
			AggroTimer.stop()
			_GoTowardsPlayer()
			CurrentState = STATE.CHASING
			#play correct animation    
			if health <= 50:
				animation_player.play("injuredrun")  
			else:
				animation_player.play("narutorun") 
			#returns are used to specify if the raycast was successful or not
			return	true                               
			
		else:
			RayCast.debug_shape_custom_color = Color.GREEN
			return false
```

Here, we can detect if the enemy can see the player with a raycast. If the player is seen and the enemy has been damaged (they only respond aggressively when the player has hurt them), then: any currently elapsing timers are stopped, the AI chases the player and we return true. Otherwise we simply return false, and use this boolean return to communicate to our "vision_timer_timeout" function if the player is seen or not.