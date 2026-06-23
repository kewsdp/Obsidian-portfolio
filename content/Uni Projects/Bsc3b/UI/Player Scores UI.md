---
title: Player Scores UI
draft: true
tags:
  - A-Minute-To-Win-It
---
UI updates regarding player scores are handled by the Game State. I considered several systems for making these updates more efficient:

Initially, I used replication callbacks (OnRep), that fire on the client when the associated variable is changed server-side. This particular instance would run a loop that checked the "Player Array" and called a function on the player with a local controller, which would only be valid on that client. However, this would require an additional call on the server and the loop would run on every client.

Then I tried using a Remote Procedure Call (RPC), locally called functions that execute remotely for connected clients, of type 'NetMulticast'. This type is executed on the server and all connected clients, which reduced the code for the sever, but was inefficient as well.

Finally, I settled on using 'Client' RPCs, only firing on client devices. This involved one loop through the "Player Array" on the server to call RPCs on the Client's Player Controller. However I still needed an additional duplicate function for each of these UI updates, as the host wouldn't be able to call the Client RPC and would need a manual call instead.

```cpp wrap
//Update game scores on Player UIs
void AGA_GameState::RelayPlayerScores(const TArray<int32>& PlayerScores)
{
	//For each player within the "PlayerArray"
	for (int32 p = 0; p < PlayerArray.Num(); p++)
	{
		//Get Player State
		AGA_PlayerState* PS = Cast<AGA_PlayerState>(PlayerArray[p]);
		//Get Player Controller from State
		APlayerController* PC = PS->GetPlayerController();

		//Only for Host
		if (PC->IsLocalController())
		{
			PS->UpdatePlayerScore(PlayerScores);
			continue;
		}

		//Only for that connected Client as it activates a Client RPC
		PS->UpdatePlayerScoreClient(PlayerScores);
	}
}
```

After this, UI updates are managed through the Player Controller, as it owns the UI Widget BP and can easily check for a Local Controller when necessary. This solution reduces the load on Client's systems, a key goal for this project.