---
title: NPC mobility, roaming and searching
draft: false
tags:
  - PoundTown
---
## Initiating Pursuit of Player

```gdscript wrap
#Head to players last know location and look at
func _GoTowardsPlayer():
	var playerposition = PlayerRef.global_transform.origin
	#we commence look at, but adjust it so the y-axis of the player is in line
	#with the enemy, also we put true for the final parameter to use the front
	#of our NPC model as the forward +Z axis
	look_at(Vector3(playerposition.x, global_position.y, playerposition.z), Vector3.UP, true)
	target_position(playerposition)
```

To head towards the player, we first make the NPC look at the player. Originally I was going to use the "looking_at" Transform3D method, and break the transform so the NPC would only rotate horizontally, however in the interest of time I decided to use this solution. We then set the "target_position" to our player.

## Searching for the Player and Roaming

```gdscript wrap
#when aggro is lost
func _on_aggro_timer_timeout() -> void:
	#reset player ref
	PlayerRef = null
	CurrentState = STATE.IDLING
	animation_player.play("idle 2")
	IdleTimer.start()
```

This is initiated when the "Aggro" timer runs out, meaning our player has avoided the NPC's sight for some time. Here, we reset the player reference and set the NPC's current state to "IDLING", simultaneously beginning an "Idle" timer.

```gdscript wrap
#on idle timer end
func _on_idle_timer_timeout() -> void:
	#wander
	var randompos = Vector3(randf_range(-59, 50), position.y, randf_range(-90, 75))
	look_at(Vector3(randompos.x, global_position.y, randompos.z), Vector3.UP, true)
	target_position(randompos)
	CurrentState = STATE.ROAMING
	animation_player.play("walk")
```

Once our idle timer has ended, the NPC will choose another random location within the bounds of our map and turn to face their target.

```gdscript
#when target reached, change based on state
func _on_navigation_agent_3d_target_reached() -> void:
	match CurrentState:
		STATE.ROAMING:
			#when roaming, idle once location reached
			CurrentState = STATE.IDLING
			IdleTimer.start()
			var animarray = ["breakdance", "idle", "salsa"]
			animation_player.play(animarray[randi_range(0, animarray.size() - 1)])
		STATE.SEARCHING:
			#when searching, head to last location and start roaming cycle again
			CurrentState = STATE.IDLING
			IdleTimer.start()
			animation_player.play("sillydance")
```

Here, we use another match and customize our results. These outcomes are mostly the same as our "Aggro" timer timeout, but "ROAMING" will randomly select an animation to play. This is more relevant within the duplicate NPC, used to represent a Police Officer, that will play specific animations upon different states. For instance, seeming dejected when searching and unable to find the player.