# r5-shadowfall

[Trello Board](https://trello.com/c/4kAuuOTs/38-shadowfall)

## Tasks
- [ ] Revenant Announcer, kill, revenge and other voicelines
- [ ] Shadowfall Special UI elements (A legend died, a shadow killed, revenge legend location etc)
- [X] Give Shadowhands after player dies
- [X] Player setskin to shadow texture (index is mostly 9, but different for some legends)
- [X] Set Melee damage for shadows (150 Damage)
- [ ] Shadow Passives (Legend highlight, high climb)
- [X] Shadows shouldn't take damage from ring
- [X] Set shadow health to 30, reset health when a shadow kills legend
- [ ] Don't allow shadows to use doors, weapons, items or abilities such as lifeline drone heal
- [ ] Shadow dive trail, shadows should spawn from a random position at sky
- [X] Disable ping system and legend voice lines for shadow players
- [ ] Last 10 players should be in same team, there is a special ui for it
- [ ] Evac dropship, ship location should be showed to players 30 seconds before round 1 ends, "Evac ship inbound" counter (120 seconds)
- [ ] Screen fx for shadows, "shadows" and "legends" player counter
- [ ] Loot bin spiders and zombie grunt (they are already works, but loot is broken

## Give Shadowhands after player dies

I don't know yet how to implement LTM game loop, but this is how to give player Shadowhands, it changes damage values, but does not damage to doors (bug).
``` 
player.TakeNormalWeaponByIndexNow( WEAPON_INVENTORY_SLOT_PRIMARY_2 )
player.TakeOffhandWeapon( OFFHAND_MELEE )
    
player.GiveWeapon( "mp_weapon_shadow_squad_hands_primary", WEAPON_INVENTORY_SLOT_PRIMARY_2, [] )
player.GiveOffhandWeapon( "melee_shadowsquad_hands", OFFHAND_MELEE, [] )
```

## Player setskin to shadow texture (index is mostly 9, but different for some legends)
```
player.SetSkin( player.GetSkinIndexByName( "ShadowSqaud" ) )
```

## Set Melee damage for shadows (150 Damage)
Look at `Give Shadowhands after player dies`

## Shadows shouldn't take damage from ring
`sh_survival_deathfield.gnut -> void function DeathFieldDamage`<br>
```
if ( player.IsPhaseShifted() ) ==> if ( player.IsPhaseShifted() || IsPlayerShadowSquad( player ) )
```

## Set shadow health to 30, reset health when a shadow kills legend
```

in Init -> AddCallback_OnPlayerKilled( OnPlayerKilled )

void function OnPlayerKilled( entity victim, entity attacker, var damageInfo )
{
    ...
    ...
    if ( victim != attacker && IsPlayerShadowSquad( attacker ) ) {
		attacker.SetHealth( attacker.GetMaxHealth() )
	}
    ...
    ...
}
	
To set after respawn:

int shadowMaxHealth = 30
player.SetMaxHealth( shadowMaxHealth )    
player.SetHealth( shadowMaxHealth )
```

## Disable ping system and legend voice lines for shadow players
So ping system is not entirely disabled for shadows, you can ping but voicelines should be disabled [Proof](https://youtu.be/WCKOt_pnBFE?t=367).<br>
Solution to that is in `sh_quickchat.gnut -> void function PlaySoundForCommsAction` add<br>
```
if ( IsPlayerShadowSquad( player ) )
	return
```
This mutes voicelines for any waypoints pings, but keeps voicelines such as "Grenade Out", "Setting up a zipline" but shadows do not have access to abilities and grenades so I think we're fine?

If we want to disable CommsMenu for them `sh_comms_menu.nut` -> `CommsMenu_OpenMenuTo` add the same check after `CommsMenu_Shutdown( true )`.



