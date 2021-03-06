# XpM
Adds an XP ranking system like the one found in GTA:O. Work in progress.

This is a framework agnostic version of my [esx_xp](https://github.com/Mobius1/esx_xp) package.

## Features
* Designed to emulate the native GTA:O system
* Saves and loads players XP / rank
* Add / remove XP from your own script / job
* Allows you listen for rank changes to reward players
* Fully customisable UI
* Integrated leaderboard

## TOC
* [Demos](#demos)
* [Requirements](#requirements)
* [Download & Installation](#download---installation)
* [Configuration](#configuration)
* [Functions](#functions)
    + [Setters](#setters)
    + [Getters](#getters)
* [Client Event Listeners](#client-event-listeners)
* [Client Triggers](#client-triggers)
* [Server Triggers](#server-triggers)
* [UI](#ui)
* [Commands](#commands)
* [Demo Commands](#demo-commands)
* [FAQ](#faq)
    - [Does this use  VenomXNL's XNLRankBar?](#does-this-use--venomxnls-xnlrankbar)
    - [How do I change the look of the UI?](#how-do-i-change-the-look-of-the-ui-)
    - [How do I lock a weapon / vehicle / unlockable to a rank?](#how-do-i-lock-a-weapon---vehicle---unlockable-to-a-rank-)
* [Contributing](#contributing)
* [Legal](#legal)
    + [License](#license)


## Demos
You can find an interactive demo [here](https://codepen.io/Mobius1/full/yLeMwzO).

##### Increasing XP

![Demo Image 1](https://i.imgur.com/CpACt9s.gif)

##### Rank Up

![Demo Image 2](https://i.imgur.com/uNPRGo5.gif)

##### Leaderboard
![Demo Image 3](https://i.imgur.com/vOY7xpI.png)

## Requirements

* [fivem-mysql-async](https://github.com/brouznouf/fivem-mysql-async)

## Download & Installation

* Download and extract the package: https://github.com/Mobius1/XpM/archive/master.zip
* Rename the `XpM-master` directory to `XpM`
* Drop the `XpM` directory into your `resources` directory on your server
* Import the `XpM.sql` file into your db
* Add `ensure XpM` in your `server.cfg`
* Edit `config.lua` to your liking
* Start your server

## Configuration

The `config.lua` file is set to emulate GTA:O as close as possible, but can be changed to fit your own needs.

```lua
Config.Enabled      = true  -- enable / disable the resource
Config.Locale       = 'en'  -- Current language
Config.Width        = 532   -- Sets the width of the XP bar in px
Config.Timeout      = 5000  -- Sets the interval in ms that the XP bar is shown before fading out
Config.BarSegments  = 10    -- Sets the number of segments the XP bar has. Native GTA:O is 10
Config.UIKey        = 20    -- The key that toggles the UI - default is "Z"

Config.Ranks        = {}    -- XP ranks. Must be a table of integers with the first element being 0.

Config.Leaderboard = {
    Enabled     = true,     -- Enable the leaderboard
    ShowPing    = true,     -- Show player pings on the leaderboard
    Order       = "rank",   -- Order the player list by "name", "rank" or "id"
    PerPage     = 12        -- Max players to show per page    
}
```

## Functions

### Setters

Set initial XP rank for player
```lua
exports.XpM:XPM_SetInitial(xp --[[ integer ]])
```

Set Rank for player. This will add the required XP to advance the player to the given rank.
```lua
exports.XpM:XPM_SetRank(rank --[[ integer ]])
```

Give player XP
```lua
exports.XpM:XPM_Add(xp --[[ integer ]])
```

Remove XP from player
```lua
exports.XpM:XPM_Remove(xp --[[ integer ]])
```

### Getters

Get player's current XP
```lua
exports.XpM:XPM_GetXP()
```

Get player's current rank
```lua
-- Get rank from current XP
exports.XpM:XPM_GetRank()

-- or

-- Get rank from given XP
exports.XpM:XPM_GetRank(xp --[[ integer ]])

```

Get XP required to advance the player to the next rank
```lua
exports.XpM:XPM_GetXPToNextRank()
```

Get XP required to advance the player to the given rank
```lua
exports.XpM:XPM_GetXPToRank(rank --[[ integer ]])
```

Get max attainable XP
```lua
exports.XpM:XPM_GetMaxXP()
```

Get max attainable rank
```lua
exports.XpM:XPM_GetMaxRank()
```

### Utils
Show the UI
```lua
XPM_ShowUI()

-- update the leaderboard at the same time

XPM_ShowUI(true)
```

Hide the UI
```lua
XPM_HideUI()
```

Show the UI and hide after timeout
```lua
XPM_TimeoutUI()
```

Sort the leaderboard
```lua
XPM_SortLeaderboard("rank")

-- or

XPM_SortLeaderboard("name")
```


## Client Event Listeners

Listen for rank change events. These can be used to reward / punish the player for changing rank.

Listen for rank-up event
```lua
AddEventHandler("XpM:rankUp", function(newRank --[[ integer ]], previousRank --[[ integer ]])
    -- Do something when player ranks up
end)
```
Listen for rank-down event
```lua
AddEventHandler("XpM:rankDown", function(newRank --[[ integer ]], previousRank --[[ integer ]])
    -- Do something when player drops a rank
end)
```

## Client Triggers
```lua
-- SET INTITIAL XP
TriggerEvent('XpM:SetInitial', xp)

-- ADD XP
TriggerEvent('XpM:Add', xp)

-- REMOVE XP
TriggerEvent('XpM:Remove', xp)

-- SET RANK
TriggerEvent('XpM:SetRank', rank)
```

## Server Triggers
```lua
-- SET INTITIAL XP
TriggerClientEvent('XpM:SetInitial', source, xp)

-- ADD XP
TriggerClientEvent('XpM:Add', source, xp)

-- REMOVE XP
TriggerClientEvent('XpM:Remove', source, xp)

-- SET RANK
TriggerClientEvent('XpM:SetRank', source, rank)

```

## UI
The UI can be toggled with the `Z` key by default. The UI will fade out after the interval defined by `Config.Timeout` or you can close it immediately with the `Z` key.

The leaderboard is paginated and can be navigated with arrow keys. The number of players displayed per page can be customised with the `PerPage` variable.

You can customise the UI key with `Config.UIKey` in `config.lua`.

The data in the leaderboard is refreshed whenever it is opened so you get up-to-date information.

## Commands
Get current XP stats
```lua
/XPM
```
output
```lua
You currently have xxxx XP
Your current rank is xxxx
You require xxxx XP to advance to rank yyyy
```

## Demo Commands

These commands are for testing and will change the UI, but no data will be saved.

If these are not required, you can delete the `demo.lua` file and remove it's entry in the `fxmanifest.lua` file.

Set initial XP
```lua
/XPM_SetInitial xp
```

Add XP
```lua
/XPM_Add xp
```

Remove XP
```lua
/XPM_Remove xp
```

Add fake player to leaderboard
```lua
/XPM_AddFakePlayer
```

Add number of fake players to leaderboard
```lua
/XPM_AddFakePlayer count
```

Remove all fake players from leaderboard
```lua
/XPM_RemoveFakePlayers
```

Sort the leaderboard
```lua
/XPM_SortLeaderboard order --[[ rank or name ]]
```

## FAQ

#### Does this use  VenomXNL's `XNLRankBar`?

No. I thought about using it, but I created a HTML5 version of the GTA:O rankbar so I could have greater control / customisation. You can see the base system I created [here](https://codepen.io/Mobius1/pen/yLeMwzO).

#### How do I change the look of the UI?

With a little knowledge of HTML5,  CSS3 and JS you can change all aspects of the look and layout of the bar to make it fit with your UI. The main structure is defined in `html/ui.html`, the main style is defined in `html/css/app.css` and scripting is defined in `html/js/app.js`.

You can find a demo of customised UI [here](https://codepen.io/Mobius1/full/eYJRmVy)

#### How do I lock a weapon / vehicle / unlockable to a rank?

To lock something to a rank you can listen for the `XpM:rankUp` or `XpM:rankDown` events:

Example of unlocking the minigun at rank 10:
```lua
AddEventHandler("XpM:rankUp", function(newRank, previousRank)
    if newRank == 10 then
        GiveWeaponToPed(PlayerPedId(), GetHashKey("WEAPON_MINIGUN"), 100, false, false)
    end
end)
```

If player ranks down then you can remove it:
```lua
AddEventHandler("XpM:rankDown", function(newRank, previousRank)
    if newRank < 10 then
        local player = PlayerPedId()
        local weapon = GetHashKey("WEAPON_MINIGUN")
        
        if HasPedGotWeapon(player, weapon, false) then
            RemoveWeaponFromPed(player, weapon)
        end
    end
end)
```

## Contributing
Pull requests welcome.

## Legal

### License

XpM - FiveM XP System

Copyright (C) 2020 Karl Saunders

This program Is free software: you can redistribute it And/Or modify it under the terms Of the GNU General Public License As published by the Free Software Foundation, either version 3 Of the License, Or (at your option) any later version.

This program Is distributed In the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty Of MERCHANTABILITY Or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License For more details.

You should have received a copy Of the GNU General Public License along with this program. If Not, see http://www.gnu.org/licenses/.