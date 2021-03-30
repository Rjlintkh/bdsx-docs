# Event Handling

There are different events you can handle with BDSX, like in-game events and network events, but we will mainly discuss in-game event on this page.

## Events

### In-game Events:
| Event | Description | Cancellable | Variables | Remarks |
|---|---|---|---|---|
| BlockDestroy | A event which is fired when a block is destroyed by a player. | Yes | <span style="color:LightBlue">player</span>: <span style="color:DarkSeaGreen">Player</span>,<br><span style="color:LightBlue">blockPos</span>: <span style="color:DarkSeaGreen">BlockPos</span> |
| BlockPlace | A event which is fired when a block is placed by a player. | Yes | <span style="color:LightBlue">player</span>: <span style="color:DarkSeaGreen">Player</span>,<br><span style="color:LightBlue">block</span>: <span style="color:DarkSeaGreen">Block</span>,<br><span style="color:LightBlue">blockSource</span>: <span style="color:DarkSeaGreen">BlockSource</span>,<br><span style="color:LightBlue">blockPos</span>: <span style="color:DarkSeaGreen">BlockPos</span> |
| EntityHurt | A event which is fired when an entity is hurt. | Yes | <span style="color:LightBlue">entity</span>: <span style="color:DarkSeaGreen">Actor</span>,<br><span style="color:LightBlue">damage</span>: <span style="color:DarkSeaGreen">number</span> |
| EntityHeal | A event which is fired when an entity is regenerating. | Yes | <span style="color:LightBlue">entity</span>: <span style="color:DarkSeaGreen">Actor</span>,<br><span style="color:LightBlue">damage</span>: <span style="color:DarkSeaGreen">number</span> |
| PlayerAttack | A event which is fired when a player attacks. | Yes | <span style="color:LightBlue">player</span>: <span style="color:DarkSeaGreen">Player</span>,<br><span style="color:LightBlue">victim</span>: <span style="color:DarkSeaGreen">Actor</span> |
| PlayerDropItem | A event which is fired when a player drops some items. | Yes | <span style="color:LightBlue">player</span>: <span style="color:DarkSeaGreen">Player</span>,<br><span style="color:LightBlue">itemStack</span>: <span style="color:DarkSeaGreen">ItemStack</span> | Cancellable only when the player is in container screen |
| PlayerJoin | A event which is fired when a player has finished loading everything and is spawned. | No | <span style="color:LightBlue">player</span>: <span style="color:DarkSeaGreen">Player</span> |
| PlayerPickupItem | A event which is fired when a player picks some items up. | Yes | <span style="color:LightBlue">player</span>: <span style="color:DarkSeaGreen">Player</span> |
| QueryRegenerate | A event which is fired when the server query information regenerates. | No | <span style="color:LightBlue">motd</span>: <span style="color:DarkSeaGreen">string</span>,<br><span style="color:LightBlue">levelname</span>: <span style="color:DarkSeaGreen">string</span>,<br><span style="color:LightBlue">currentPlayers</span>: <span style="color:DarkSeaGreen">number</span>,<br><span style="color:LightBlue">maxPlayers</span>: <span style="color:DarkSeaGreen">number</span> |

## Examples:

### BlockDestroy:
```ts
    import { CANCEL } from "bdsx";
    import { events } from "bdsx/event";
    events.blockDestroy.on(event => {
        const { blockPos, player } = event;
        console.log("Position:", { x: blockPos.x, y: blockPos.y, z: blockPos.z }, "Player", player.getName());
        return CANCEL; // cancels the event
    });
```

### QueryRegenerate:
```ts
    import { events } from "bdsx/event";
    events.queryRegenerate.on(event => {
        event.motd = "New MOTD"; // changes the values of the variables of the event
        event.levelname = "New levelname";
        event.currentPlayers = 1000;
        event.maxPlayers = 9999;
    });
```