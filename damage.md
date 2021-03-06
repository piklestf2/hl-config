# [User:BoxFigs/Health and Damage][1]
This article is under construction as it lacks elements required to
provide basic coverage of its subject and/or has yet to be fully integrated
into the Combine OverWiki.


## Half-Life


### Player
The player has a maximum of 100 health and 100 suit. If the player has at least
1 suit, the damage to health is divided by 5 and half of the remaining damage
is dealt to the suit. In other words, the health takes 20% of the damage, while
the suit takes 80%/2 of the damage. To calculate this for any amount of damage,
use these formulas:

```
dmgHealth = dmg / 5
dmgSuit = (dmg - dmg / 5) / 2
```

This is evidenced in the source code by lines 485-489 of dlls/player.cpp:

```
float flNew = flDamage * flRatio;

float flArmor;

flArmor = (flDamage - flNew) * flBonus;
```

"flArmor" is the new armor value, "flRatio" is the ratio of health to suit
points, and "flBonus" is the ratio of the damage to health over the damage to
suit. "flRatio" is set to 0.2, which means health takes 1/5 of the damage and
the suit takes 4/5 of the damage. "flBonus" is set to 0.5, which makes the suit
lose half as much points as it would have. In multiplayer, the amount of damage
dealt to the suit is multiplied by 2 when taking damage from explosions.

The suit protects against all damage types except for falling and drowning.

If the player's current suit power is less than the damage that will be dealt
to the suit, the damage to the player's health is subtracted by half of the
suit power, then the suit power is set to 0.


### NPCs
NPCs have the following maximum health values:

|Name                      |Health (Easy)  |Health (Normal)  |Health (Hard)  |
|--------------------------|---------------|-----------------|---------------|
|Alien Controller          |60             |60               |100            |
|Alien Grunt               |60             |90               |120            |
|Apache                    |150            |250              |400            |
|Assassin                  |30             |50               |50             |
|Automatic Turret (Large)  |50             |50               |60             |
|Automatic Turret (Small)  |40             |40               |50             |
|Barnacle                  |25             |25               |25             |
|Bullsquid                 |40             |40               |120            |
|Gargantua                 |800            |800              |1000           |
|Gonarch                   |2100           |3150             |4200           |
|Headcrab                  |10             |10               |20             |
|Houndeye                  |20             |20               |30             |
|Ichthyosaur               |200            |200              |400            |
|Leech                     |2              |2                |2              |
|Marine                    |50             |50               |80             |
|Nihilanth                 |800            |800              |1000           |
|Scientist                 |20             |20               |20             |
|Security Guard            |35             |35               |35             |
|Sentry Gun                |40             |40               |50             |
|Snark                     |2              |2                |2              |
|Vortigaunt                |30             |30               |60             |
|Zombie                    |50             |50               |100            |

Some NPCs have special damage values for inherent attacks. Damage values using
weapons are discussed in the next section.

|Name               |Attack       |Damage (Easy)    |Damage (Normal)    |Damage (Hard)  |
|-------------------|-------------|-----------------|-------------------|---------------|
|Alien Controller   |Orbs         |3                |4                  |5              |
|Alien Grunt        |Punch        |10               |20                 |20             |
|Barnacle           |Bite         |10               |10                 |10             |
|Bullsquid          |Bite         |15               |25                 |25             |
|Bullsquid          |Spit         |10               |10                 |15             |
|Bullsquid          |Tail whip    |25               |35                 |35             |
|Gargantua          |Flames       |3                |5                  |5              |
|Gargantua          |Punch        |10               |30                 |30             |
|Gargantua          |Stomp        |50               |100                |100            |
|Gonarch            |Blast        |100              |120                |160            |
|Gonarch            |Slash        |50               |60                 |70             |
|Headcrab           |Bite         |5                |10                 |10             |
|Houndeye           |Shockwave    |10               |15                 |15             |
|Ichthyosaur        |Bite         |20               |35                 |50             |
|Leech              |Bite         |2                |2                  |2              |
|Marine             |Kick         |5                |10                 |10             |
|Nihilanth          |Orbs         |30               |30                 |50             |
|Snark              |Bite         |10               |10                 |10             |
|Snark              |Explode      |5                |5                  |5              |
|Vortigaunt         |Claw         |8                |10                 |10             |
|Vortigaunt         |Zap          |10               |10                 |15             |
|Zombie             |Claw slash   |10               |20	                |20             |


### Weapons
Weapons can be used by the player and NPCs. However, the damage they deal is
different depending on if a player is using it or an NPC is using it.


##### Player:
|Weapon          |Damage (Primary fire)  |Damage (Alt fire)      |
|----------------|-----------------------|-----------------------|
|Crossbow        |50                     |                       |
|Crowbar         |5                      |                       |
|Gluon Gun       |14                     |                       |
|Grenade         |100                    |                       |
|Hivehand        |7                      |                       |
|Magnum          |40                     |                       |
|Pistol          |8                      |                       |
|RPG             |100                    |                       |
|Satchel Charge  |150                    |                       |
|Shotgun         |30 (5 per pellet x6)   |60 (5 per pellet x12)  |
|SMG             |5                      |100                    |
|Tau Cannon      |20 (2 ammo)            |25-200 (1-13 ammo)     |
|Tripmine        |150                    |                       |


###### Tau Cannon Charge
Tau Cannon charge damage is calculated as follows:

```
flDamage = 200 * (( gpGlobals->time - m_pPlayer->m_flStartCharge) / GetFullChargeTime() );
```

where `GetFullChargeTime()` is 4 seconds for single player. Additionally, the
charge has 0.5 seconds of idle time and consumes one unit of ammo every 0.3
seconds after starting the charge. [(source)][2]

Therefore, the damage scales linearly from 0 to 200 over 4 seconds, with
a minimum of 25 damage. Each additional unit of ammo corresponds to 15 more
points of damage. Essentially, the damage formula simplifies to

```
flDamage = 50 * chargeTime
```

The following table shows the damage intervals for each unit of ammo:

|Ammo  |Damage      |
|------|------------|
|1     |[25, 30)    |
|2     |[30, 45)    |
|3     |[45, 60)    |
|4     |[60, 75)    |
|5     |[75, 90)    |
|6     |[90, 105)   |
|7     |[105, 120)  |
|8     |[120, 135)  |
|9     |[135, 150)  |
|10    |[150, 165)  |
|11    |[165, 180)  |
|12    |[180, 195)  |
|13    |[195, 200]  |

In terms of resource value, that last unit of charge is a true scam...
Conversely, with good timing you can repeatedly hit at least 29 damage for
1 ammo.


##### NPCs:
|Weapon    |Damage (Easy)  |Damage (Normal)  |Damage (Hard)  |Used By                               |
|----------|---------------|-----------------|---------------|--------------------------------------|
|Hivehand  |4              |5                |8              |Alien Grunts                          |
|Pistol    |5              |5                |8              |Security Guards, Black Ops Assassins  |
|Shotgun   |5              |5                |8              |Marines                               |
|SMG       |3              |4                |5              |Marines                               |


### Taking damage
Players and NPCs take damage in the same way, including headshots. Headshots
multiply the normal damage by 3. Headshots can be performed only with guns on
humanoid NPCs or the player.

The following tables show how many body hits/shots with each weapon are
required to kill specific NPCs (for NPCs with multiple rows, easy is above and
normal below)

|NPC                       |Crowbar  |Pistol  |Magnum  |SMG  |SMG Grenade  |Shotgun  |Shotgun (double shot)  |Crossbow  |Hivehand  |RPG  |Tau Cannon  |Tau Cannon (charged)  |Gluon Gun  |Grenade  |Satchel Charge  |Tripmine  |
|--------------------------|---------|--------|--------|-----|-------------|---------|-----------------------|----------|----------|-----|------------|----------------------|-----------|---------|----------------|----------|
|Alien Grunt               |12       |8       |2       |12   |1            |2        |1                      |2         |9         |1    |3           |3-1                   |5          |1        |1               |1         |
|Alien Grunt               |18       |12      |3       |18   |1            |2        |1                      |2         |13        |1    |5           |4-1                   |7          |1        |1               |1         |
|Apache                    |30       |19      |4       |30   |2            |5        |3                      |3         |22        |2    |8           |6-1                   |11         |2        |1               |1         |
|Apache                    |50       |32      |7       |50   |3            |9        |5                      |5         |36        |3    |13          |10-2                  |18         |3        |2               |2         |
|Barnacle                  |5        |4       |1       |5    |1            |1        |1                      |1         |4         |1    |2           |1                     |2          |1        |1               |1         |
|Security Guard            |8        |5       |1       |8    |1            |2        |1                      |1         |5         |1    |2           |2-1                   |3          |1        |1               |1         |
|Bullsquid                 |8        |5       |1       |8    |1            |2        |1                      |1         |6         |1    |2           |2-1                   |3          |1        |1               |1         |
|Gargantua                 |160      |100     |20      |160  |8            |27       |14                     |16        |115       |8    |40          |32-4                  |58         |8        |6               |6         |
|Assassin                  |6        |4       |2       |6    |1            |1        |1                      |1         |5         |1    |2           |2-1                   |3          |1        |1               |1         |
|Assassin                  |10       |7       |2       |10   |1            |2        |1                      |1         |8         |1    |3           |2-1                   |4          |1        |1               |1         |
|Headcrab                  |2        |2       |1       |2    |1            |1        |1                      |1         |2         |1    |1           |1                     |1          |1        |1               |1         |
|Marine                    |10       |7       |2       |10   |1            |2        |1                      |1         |8         |1    |3           |2-1                   |4          |1        |1               |1         |
|Houndeye                  |4        |3       |1       |4    |1            |1        |1                      |1         |3         |1    |1           |1                     |2          |1        |1               |1         |
|Vortigaunt                |6        |4       |1       |6    |1            |1        |1                      |1         |5         |1    |2           |2-1                   |3          |1        |1               |1         |
|Ichthyosuar               |40       |25      |5       |40   |2            |7        |4                      |4         |29        |2    |10          |8-1                   |15         |2        |2               |2         |
|Leech                     |1        |1       |1       |1    |1            |1        |1                      |1         |1         |1    |1           |1                     |1          |1        |1               |1         |
|Alien Controller          |12       |8       |2       |12   |1            |2        |1                      |2         |9         |1    |3           |3-1                   |5          |1        |1               |1         |
|Nihilanth                 |160      |100     |20      |160  |8            |27       |14                     |16        |115       |8    |40          |32-4                  |58         |8        |6               |6         |
|Scientist                 |4        |3       |1       |4    |1            |1        |1                      |1         |3         |1    |1           |1                     |2          |1        |1               |1         |
|Snark                     |1        |1       |1       |1    |1            |1        |1                      |1         |1         |1    |1           |1                     |1          |1        |1               |1         |
|Zombie                    |10       |7       |2       |10   |1            |2        |1                      |1         |8         |1    |3           |2-1                   |4          |1        |1               |1         |
|Automatic Turret (Large)  |10       |7       |2       |10   |1            |2        |1                      |1         |8         |1    |3           |2-1                   |4          |1        |1               |1         |
|Automatic Turret (Small)  |8        |5       |1       |8    |1            |2        |1                      |1         |6         |1    |2           |2-1                   |3          |1        |1               |1         |
|Sentry Gun                |8        |5       |1       |8    |1            |2        |1                      |1         |6         |1    |2           |2-1                   |3          |1        |1               |1         |


## Half-Life 2


### Player
The player has a maximum of 100 health and 100 suit. In the chapter Our
Benefactors, the player has a maximum of 200 suit. Note that this is
technically a property of the suit chargers in the chapter's levels rather than
the chapter itself.

If the player has at least 1 suit, the damage to health is divided by 5 and the
rest of the damage is dealt to the suit. In other words, the health takes 20%
of the damage, while the suit takes 80% of the damage. To calculate this for
any amount of damage, use these formulas:

```
dmgHealth = dmg/5
dmgSuit = dmg - dmg/5
```

This is evidenced in the source code by lines 1160-1164 of
game/server/player.cpp:

```
float flNew = info.GetDamage() * flRatio;

float flArmor;

flArmor = (info.GetDamage() - flNew) * flBonus;
```

"flArmor" is the new armor value, "flRatio" is the ratio of health to suit
points, and "flBonus" is the ratio of the damage to health over the damage to
suit. "flRatio" is set to 0.2, which means health takes 1/5 of the damage and
the suit takes 4/5 of the damage. "flBonus" is set to 1.0, which makes it
effectively ignored, as it is a 1-1 ratio.

The suit protects against all damage types except for falling, drowning,
poison, and radiation.

If the player's current suit power is less than the damage that will be dealt
to the suit, the damage to the player's health is subtracted by the suit power,
then the suit power is set to 0.


### NPCs
NPCs have the following maximum health values:

|Name                       |Health  |
|---------------------------|--------|
|Antlion                    |30      |
|Antlion Grub               |5       |
|Antlion Guard              |500     |
|Barnacle                   |35      |
|Citizen                    |40      |
|City Scanner               |30      |
|Civil Protection           |40      |
|Civil Protection (simple)  |26      |
|Fast Headcrab              |10      |
|Fast Zombie                |50      |
|Headcrab                   |10      |
|Hunter-Chopper             |5600    |
|Manhack                    |25      |
|Overwatch Elite            |70      |
|Overwatch Soldier          |50      |
|Poison Headcrab            |35      |
|Poison Zombie              |175     |
|Stalker                    |50      |
|Strider                    |350     |
|Vortiguant                 |100     |
|Zombie                     |50      |

NPCs that typically require an RPG to kill have special values for the number
of rockets needed to kill them.

|Name     |RPG Count (Easy)  |RPG Count (Normal)  |RPG Count (Hard)  |
|---------|------------------|--------------------|------------------|
|Gunship  |3                 |5                   |7                 |
|Strider  |5                 |7                   |7                 |

Some NPCs have special damage values for inherent melee attacks. Damage values
using weapons are discussed in the next section.

|Name               |Attack        |Damage  |
|-------------------|--------------|--------|
|Antlion            |Claw gouge    |5       |
|Antlion            |Dive          |5       |
|Antlion Guard      |Charge        |20      |
|Antlion Guard      |Headbutt      |10      |
|Barnacle           |Bite          |10      |
|City Scanner       |Divebomb      |25      |
|Fast Headcrab      |Bite          |5       |
|Fast Zombie        |Claw slash    |10      |
|Headcrab           |Bite          |5       |
|Manhack            |Slice         |20      |
|Overwatch Elite    |Weapon stock  |15      ]
|Overwatch Soldier  |Weapon stock  |10      |
|Poison Headcrab    |Bite          |10      |
|Poison Zombie      |Claw slash    |20      |
|Rollermine         |Shock         |10      |
|Zombie             |Claw slash    |10      |


### Weapons
Weapons can be used by the player and NPCs. However, the damage they deal is
different depending on if a player is using it or an NPC is using it.

|Weapon       |Damage (Player)  |Damage (NPC)  |Used by NPCs                                  |
|-------------|-----------------|--------------|----------------------------------------------|
|Airboat      |3                |3             |                                              |
|Alyx's Gun   |5                |3             |Alyx                                          |
|Annabelle    |?                |40            |Grigori                                       |
|Crossbow     |100              |10            |                                              |
|Crowbar      |10               |5             |                                              |
|Grenade      |150              |75            |Overwatch Soldier                             |
|Magnum       |40               |30            |                                              |
|Pistol       |5                |3             |Civil Protection                              |
|Pulse Rifle  |8                |3             |Overwatch Soldier, Citizen, Barney            |
|RPG          |100              |50            |Citizen                                       |
|Shotgun      |8/12: 84         |3/7: 21       |Overwatch Soldier, Citizen, Alyx (Episode 1)  |
|Shotgun      |8/7: 56          |3/7:21        |Overwatch Soldier, Citizen, Alyx (Episode 1)  |
|SMG          |4                |3             |Civil Protection, Overwatch Soldier, Citizen  |
|SMG Grenade  |100              |50            |                                              |
|Stun Baton   |10               |40            |Civil Protection                              |


### Taking damage
Players and NPCs take damage in the same way, including headshots. Headshots
multiply the normal damage by 3. Headshots can be performed only with guns on
humanoid NPCs or the player.

This table shows how many body hits/shots with each weapon are required to kill
specific NPCs:

|NPC                        |Crowbar  |Pistol  |Magnum  |SMG  |SMG Grenade  |Pulse Rifle  |Shotgun  |Shotgun (double shot)  |Crossbow  |Grenade  |RPG  |
|---------------------------|---------|--------|--------|-----|-------------|-------------|---------|-----------------------|----------|---------|-----|
|Antlion                    |3        |6       |1       |8    |1            |4            |1        |1                      |1         |1        |1    |
|Antlion Guard              |50       |100     |13      |125  |5            |63           |9        |6                      |5         |4        |5    |
|Barnacle                   |4        |7       |1       |9    |1            |5            |1        |1                      |1         |1        |1    |
|City Scanner               |3        |6       |1       |8    |1            |4            |1        |1                      |1         |1        |1    |
|Civil Protection           |4        |8       |1       |10   |1            |5            |1        |1                      |1         |1        |1    |
|Civil Protection (simple)  |3        |6       |1       |7    |1            |4            |1        |1                      |1         |1        |1    |
|Fast Headcrab              |1        |2       |1       |3    |1            |2            |1        |1                      |1         |1        |1    |
|Fast Zombie                |5        |10      |2       |13   |1            |7            |1        |1                      |1         |1        |1    |
|Headcrab                   |1        |2       |1       |3    |1            |2            |1        |1                      |1         |1        |1    |
|Manhack                    |3        |5       |1       |7    |1            |4            |1        |1                      |1         |1        |1    |
|Overwatch Elite            |7        |14      |2       |18   |1            |9            |2        |1                      |1         |1        |1    |
|Overwatch Soldier          |5        |10      |2       |13   |1            |7            |1        |1                      |1         |1        |1    |
|Poison Headcrab            |4        |7       |1       |9    |1            |5            |1        |1                      |1         |1        |1    |
|Poison Zombie              |18       |35      |5       |44   |2            |22           |4        |3                      |2         |2        |2    |
|Zombie                     |5        |10      |2       |13   |1            |7            |1        |1                      |1         |1        |1    |

This table shows how many headshots with each gun are required to kill specific
NPCs:

|NPC                        |Pistol  |Magnum  |SMG  |Pulse Rifle  |Shotgun  |Shotgun (double shot)  |
|---------------------------|--------|--------|-----|-------------|---------|-----------------------|
|Civil Protection           |3       |1       |4    |2            |1        |1                      |
|Civil Protection (simple)  |2       |1       |3    |2            |1        |1                      |
|Fast Zombie                |4       |1       |5    |3            |1        |1                      |
|Overwatch Elite            |5       |1       |6    |3            |1        |1                      |
|Overwatch Soldier          |4       |1       |5    |3            |1        |1                      |
|Poison Zombie              |12      |2       |15   |8            |2        |1                      |
|Zombie                     |4       |1       |5    |3            |1        |1                      |

Note that in both tables, shotgun hit counts are calculated as if all the
pellets hit and explosive weapons hit counts are calculated as if it was
a direct hit.


[1]: https://combineoverwiki.net/wiki/User:BoxFigs/Health_and_Damage
[2]: https://github.com/ValveSoftware/halflife/blob/master/dlls/gauss.cpp
