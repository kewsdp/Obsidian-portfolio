---
title: Attribute Change UI
draft: false
tags:
  - A-Minute-To-Win-It
---
[Tranek's unofficial "GASDocumentation"](https://github.com/tranek/GASDocumentation) suggested using an Asynchronous (Async) task to effectively update the UI to reflect Player Attributes, these tasks run on a different thread from the central 'Game Thread' and avoid performance issues when compared to running Synchronously. 

The suggested C++ class creates custom Blueprint nodes that make class instances and allows Blueprint code to be executed asynchronously when Attribute Set changes occur. This works by attaching a weak reference of the object's "AttributeChanged" delegate, to the Attribute Set's built-in "GameplayAttributeValueChangeDelegate".

Since these objects remain in memory if not manually destroyed, we also store a reference to the object in Blueprints, so we can call the "EndTask" function to delete it.

<iframe src="https://blueprintue.com/render/jxavu0r6/" scrolling="no" width="800" height="600" allowfullscreen></iframe>

[[UI Async Task BP.png | CLICK HERE if the above Blueprint does not load!]]

The code below is used to setup our custom "Listen for Attribute Changes" BP node: 
```cpp wrap
//listen for multiple attributes changing
UGA_AsyncTaskAttributeChange* UGA_AsyncTaskAttributeChange::ListenForAttributesChange(UAbilitySystemComponent* ASC, TArray<FGameplayAttribute> Attributes)
{
	//create a new instance
	UGA_AsyncTaskAttributeChange* WaitForAttributeChangedTask = NewObject<UGA_AsyncTaskAttributeChange>();
	//set our appropriate values
	WaitForAttributeChangedTask->ASC = ASC;
	WaitForAttributeChangedTask->AttributesToListenFor = Attributes;

	//If ASC isnt valid OR amount of attributes is 0
	if (!IsValid(ASC) || Attributes.Num() < 1)
	{
		//delete object
		WaitForAttributeChangedTask->RemoveFromRoot();
		return nullptr;
	}

	//for attributes in attribute array
	for (FGameplayAttribute Attribute : Attributes)
	{
		//add task to change delegate
		ASC->GetGameplayAttributeValueChangeDelegate(Attribute).AddUObject(WaitForAttributeChangedTask, &UGA_AsyncTaskAttributeChange::AttributeChanged);
	}
	//return object
	return WaitForAttributeChangedTask;
}
```

[[A Minute to Win It Overview#Development process|<Back to Project Overview]]