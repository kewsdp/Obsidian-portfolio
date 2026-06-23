---
title: Creating Weapon Stat Effects
draft: false
tags:
  - A-Minute-To-Win-It
---
Within the Game State:

```cpp wrap
void AGA_GameState::CreateEffect_Implementation(EWeaponTypes Weapon)
{
	//Instantiate default Gameplay Effect
	UGameplayEffect* GEStatChange = NewObject<UGameplayEffect>(GetTransientPackage());
	//Modify the duration policy to instant (immediate permanent change)
	GEStatChange->DurationPolicy = EGameplayEffectDurationType::Instant;
	//Add to our Effect Map, using the passed in weapon as the key
	EffectMap.Add(Weapon, GEStatChange);
}
```

The code above instantiates a Gameplay Effect, adjusts it and adds it to the 'EffectMap', however this was not my original plan. 

Gameplay Effects are rarely instantiated, instead a [[Gameplay Effect Spec]] is created from the effect. My initial approach used this conventional process, where I aimed to create an Effect Blueprint that contained all possible stats (Attributes) that could be randomised, and used the Spec's 'Set-by-Caller' to uniquely set stat changes when a Spec was constructed. Although, this would need to be updated every time I introduced a new stat, and the other unchanged stats would still be part of the effect, which didn't seem very efficient.

Therefore, to allow my system to be easily adjustable, I opted to create the effects at runtime, a method mentioned in [Tranek's unofficial "GASDocumentation"](https://github.com/tranek/GASDocumentation).  I experienced a few issues with using this approach, and after closer inspection of the Documentation I determined the problem. When creating effects at runtime, the ASC would create a Spec from the Effect's Class Default Object (CDO). Since I was making a new object from the Default Effect Class, this would reset my attribute modifiers and yield an error of "no modifiers". 

Fortunately, the documentation suggested making the effect a UProperty, which would prevent the Effect object from being garbage collected and preserving the modifiers. The result is a Gameplay Effect object, with modifiers that can be manually updated at runtime. While I believe this solution is not as effective as using Specs, I think it was necessary due to the nature of the stat randomisation and suitable for the project's time frame.

[[Stat Randomisation Walkthrough#Main Randomisation|<Back to Stat Randomisation Walkthrough]]