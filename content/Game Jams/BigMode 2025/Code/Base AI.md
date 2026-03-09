---
title: Base AI and AI setup
draft: false
tags:
  - PoundTown
---
## NPC States

```gdscript wrap
#Enumerable used to affect the AI actions
enum STATE { ROAMING , CHASING , IDLING , SEARCHING, DYING, ATTACKING}
#default state is idling
var CurrentState : STATE = STATE.IDLING
```

This enum was used similarly to blackboard values within UE5, serving as a point of reference to determine which functions had to be executed when.

## Tick Physics

``` gdscript wrap
#main tick function
func _physics_process(delta: float) -> void:
	# Add the gravity.
	if not is_on_floor():
		velocity += get_gravity() * delta
	else:
		velocity.y -= 2
	#check next target position
	var next_location = navref.get_next_path_position()
	#get current position
	var current_location = global_transform.origin
	#normalise for consistent speed
	var new_velocity = (next_location - current_location).normalized() * SPEED
	#set velocity
	velocity = new_velocity
	#smoothly adjust velocity
	velocity = velocity.move_toward(new_velocity, 0.25)
	#apply motion
	move_and_slide()
```

I used "physics_process" rather than "process" to ensure collisions functioned at a constant frame rate. Within this: the gravity is applied, the AI's velocity when responding to movements is adjusted correctly (making the AI slow down as it reaches its destination) and motion is applied.

## Base AI Functions

```gdscript wrap
#Func called when timer runs out
func _on_vision_timer_timeout():
	#functionally similar to UE5 switch for enum cases
	match CurrentState:
		#if roaming, check overlap
		STATE.ROAMING:
			check_vision_overlap()
		#if searching also check overlap
		STATE.SEARCHING:
			check_vision_overlap()
		#if attacking, pass to keep anim playing
		STATE.ATTACKING:
			pass
		#if idling, check overlap
		STATE.IDLING:
			check_vision_overlap()
```

Here we replicate a UE5 Behaviour Tree with the Godot equivalent of a switch, giving different responses on a timer "timeout", compared to a tick function, based on the NPC's state. Most of these outcomes are identical, however this proved more readable and easier to adjust for future updates. 

```gdscript wrap
		#if chasing, check overlap but respond to the return appropriately
		STATE.CHASING:
			#if not seeing player, start searching last known location
			if check_vision_overlap() == false:
				#if not aggrovated yet, start timer and save last position
				if AggroTimer.time_left == 0.0:
					AggroTimer.start()
					_GoTowardsPlayer()
					CurrentState = STATE.SEARCHING
			#if seen, check the damage area overlap
			else:
				var damageoverlaps = DamageAreaRef.get_overlapping_bodies()
				if damageoverlaps.size() > 0:
					for overlap in damageoverlaps:
						#check its player
						if overlap.name == "Player":
							#damage player
							Global.player_ref.change_health(20)
							animation_player.play("punch")
							CurrentState = STATE.ATTACKING
							#attacking timer pauses other states to play anim
							attacking_timer.start()
```

However, if the NPC is chasing the player, we create a few different outcomes. These include beginning a searching state upon NOT seen and, alternatively, to attack the player if within range AND seen. The functions 'check_vision_overlap()' and 'TrackPlayerRaycast()' are used to manage [[NPC Visibility]] , whereas 'GoTowardsPlayer()' and the Roaming/Searching timers are used to control [[NPC Mobility]].

## Damage Taken

```gdscript wrap
#Called when the player attacks something
func change_health(damage):
	#change and clamp healh
	health = clamp(health - damage, 0, 100)
	#if dead
	if health <= 0:
		#set next position for nav to self
		target_position(global_position)
		#disable other collisions
		enemy_capsule.disabled = true
		vision_cone_collision.disabled = true
		$DamageArea/CollisionShape3D.disabled = true
		#stop animations
		animation_player.active = false
		#stop movement
		CurrentState = STATE.DYING
		#stop timers
		if AggroTimer.time_left != 0:
			AggroTimer.stop()
		if IdleTimer.time_left != 0:
			IdleTimer.stop()
		if vision_timer.time_left != 0:
			vision_timer.stop()
		#start timer to destroy node
		deathtimer.start()
		#play a random death sound
		audio_stream_player.stream = DeathSounds[randi_range(0, DeathSounds.size() - 1)]
		audio_stream_player.play()
```

Here, we adjust and check the NPC's health upon damage being applied. If health has completely depleted we: disable default collisions/animations, update the state, stop current timers, play appropriate SFX and start a death timer. This death timer is relevant for this next section.

```gdscript wrap
		#get bones
		var bones = skeleton.get_children()
		#activate bones so their collisions function
		for bone in bones:
			if bone.get_child_count() > 0:
				for bonecollision in bone.get_children():
					if bonecollision.get_class() == "CollisionShape3D":
						bonecollision.disabled = false
		#start sim
		skeleton.physical_bones_start_simulation()
		#launch ragdoll
		launchragdoll()
		return
```

We also enable all bones collisions for use in the 'launchragdoll()' function to implement [[Ragdoll Physics]]. The death timer is necessary to control despawning the ragdoll so it does not occupy space permanently.

```gdscript wrap
	else:
		audio_stream_player.stream = HitSounds[randi_range(0, HitSounds.size() - 1)]
	audio_stream_player.play()
		#look at the player to begin chasing on hit
		look_at(Vector3(Global.player_ref.global_transform.origin.x, global_position.y, Global.player_ref.global_transform.origin.z), Vector3.UP, true)
```

If the NPC has remaining health after receiving damage, they will make a sound and look at the player to begin their pursuit.

## NPC interaction

```gdscript wrap
###within the player -> InteractRaycast script
if get_collider() and get_collider().is_in_group("enemy"):
				var enemy = get_collider()
				enemy.audio_stream_player.stream = MiscSounds[randi_range(0, enemy.MiscSounds.size() - 1)]
				enemy.audio_stream_player.play()
```

This code simply plays a random "MiscSound" from the exported array of sounds when the player interacts with the NPC. These groups are also used for managing damage:

![[Godot group example.png]]

## Result

The outcome of my code was an NPC that could: return a voiceline when prompted, freely roam, chase the player when provoked, attack the player and ragdoll when killed. This project has helped me to understand more coding fundamentals through the ease of learning and applying gdscript, which felt more similar to Python than C++. Furthermore, it has allowed me to see the benefits of using the engine for Game Jams, where the ease of use is especially applicable for short term projects.