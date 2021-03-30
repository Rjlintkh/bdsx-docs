# Command Registration

You can register commands and hook command execution with BDSX.

## Registering a Command
There are limited supported command parameters currently and `allow-cheats` is required.

### Supported Command Parameters
| Parameter | Class | Remarks |
|---|---|---|
| int | int32_t |
| float | float32_t |
| Boolean | bool_t | displays as "unknown" |
| string | CxxString |
| target | ActorWildcardCommandSelector |
| value | RelativeFloat |
| text | CommandRawText |

### Examples

#### Registering a command with all supported parameter types:
```ts
    import { command } from "bdsx";
    import { RelativeFloat } from "bdsx/bds/blockpos";
    import { ActorWildcardCommandSelector, CommandPermissionLevel, CommandRawText } from "bdsx/bds/command";
    import { bool_t, CxxString, float32_t, int32_t } from "bdsx/nativetype";
    command.register("test", "desc", CommandPermissionLevel.Normal, 0, 0).overload((param, origin, output) => {
        console.log("from:", origin.getName());
        for (let actor of param.target.newResults(origin)) { // gets an array of Actor from ActorWildcardCommandSelector
            console.log("selected target:", (actor.getName());
        }
    }, { int: int32_t,
        float: float32_t,
        Boolean: bool_t,
        string: CxxString,
        target: ActorWildcardCommandSelector,
        value: RelativeFloat,
        text: [CommandRawText, true] // optional parameter
    });
```

#### Registering a command with debug command flag (light blue commands):
```ts
    import { command } from "bdsx";
    import { CommandPermissionLevel } from "bdsx/bds/command";
    command.register("debug", "desc", CommandPermissionLevel.Normal, 0, 1).overload((param, origin, output) => { // sets the second flag to 1 to enable debug color
    }, {
        // no parameter
    });
```

## Hooking command execution
Commands can also be hooked, even the not registered ones

### Example
```ts
    import { command } from "bdsx";
    command.hook.on((command, origin)=>{
        if (command.startsWith('/tp ')) {
            return 0x10000; // returns MCRESULT_CommandNotFound
        }
        switch (command) {
            case "/whoami":
                if (origin === "Server") {
                    console.log('You are the server console');
                } else {
                    console.log(`You are ${origin}`);  
                }
                return 1; // returns MCRESULT_Success 
                break;
            default: return; // process the default command
            }
        return 0; // suppress the command, It will mute 'Unknown command' message.
    });
```