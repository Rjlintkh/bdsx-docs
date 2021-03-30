# Packets
Packets are blocks of data sent of received by the server and client to communicate with each other. They are stored in the memory of a computer. In BDSX, you can read or write packets as binary streams according to the offset to the address of the packet in the memory. The packet names of BDSX are referenced from [MiNET](https://github.com/NiclasOlofsson/MiNET/blob/master/src/MiNET/MiNET/Net/MCPE%20Protocol%20Documentation.md).

## Packet listeners
There are five different listeners for packets.
##### Packet Receiving
- `nethook.raw`: listens serialized packet streams (raw packets), the first phase to process received packets.
- `nethook.before`: listens C++ packets before processing, the second phase to process received packets.
- `nethook.after`: listens C++ packets after processing, the third phase to process received packets.

#### Packet Sending
- `nethook.sendRaw`: listens serialized packet streams (raw packets).
- `nethook.send`: listens C++ packets before sending.

## Raw Packets

For raw packets, there are no default decoding nor encoding functions in BDSX, but you can refer to how [PMMP](https://github.com/pmmp/PocketMine-MP/tree/cb06be615aa3780d4c83a947520fa55c0d908618/src/pocketmine/network/mcpe/protocol) decodes and encodes the packets. BDSX uses `NativePointer` class to store packets while PMMP uses `BinaryStream`, therefore there are some differences in terms of the methods to manipulate with raw packets:

| Bytes | Type | BDSX NativePointer | PMMP BinaryStream | Remarks |
|---|---|---|---|---|
| 1 | Boolean | <span style="color:BurlyWood">readBoolean()</span> | <span style="color:Plum">getBoolean()</span> |
| 1 | Signed Byte | <span style="color:BurlyWood">readInt8()</span> | <span style="color:Plum">/</span> |
| 1 | Unsigned Byte | <span style="color:BurlyWood">readUint8()</span> | <span style="color:Plum">getByte()</span> |
| 2 | Signed Short | <span style="color:BurlyWood">readInt16()</span> | <span style="color:Plum">getSignedLShort()</span> |
| 2 | Unsigned Short | <span style="color:BurlyWood">readUint16()</span> | <span style="color:Plum">getLShort()</span> |
| 4 | Signed Int | <span style="color:BurlyWood">readInt32()</span> | <span style="color:Plum">getInt()</span> |
| 4 | Unsigned Int | <span style="color:BurlyWood">readUint32()</span> | <span style="color:Plum">/</span> |
| 8 | Signed Long | <span style="color:BurlyWood">/</span> | <span style="color:Plum">/</span> |
| 8 | Unsigned Long | <span style="color:BurlyWood">readBin(4)</span> | <span style="color:Plum">getLLong()</span> | Chakra doesn't support BigInt |
| 8 | Pointer Address | <span style="color:BurlyWood">readPointer()</span> | <span style="color:Plum">/</span> |
| 4 | Float | <span style="color:BurlyWood">readFloat32()</span> | <span style="color:Plum">getLFloat()</span> |
| 8 | Double | <span style="color:BurlyWood">readFloat64()</span> | <span style="color:Plum">/</span> |
| 1-∞ | String | <span style="color:BurlyWood">readVarString()</span> | <span style="color:Plum">getString()</span> |
| 1-5 | Signed Var Int | <span style="color:BurlyWood">readVarInt()</span> | <span style="color:Plum">getVarInt()</span> |
| 1-5 | Unigned Var Int | <span style="color:BurlyWood">readVarUint()</span> | <span style="color:Plum">getUnsignedVarInt()</span> |
| 1-10 | Unsigned Var Long | <span style="color:BurlyWood">readVarBin()</span> | <span style="color:Plum">getUnsignedVarLong()</span> | Chakra doesn't support BigInt |
| 16 | UUID | <span style="color:BurlyWood">readBin(8)</span> | <span style="color:Plum">getUUID()</span> |
| 1-10 | ActorUniqueId | <span style="color:BurlyWood">readVarBin()</span> | <span style="color:Plum">getEntityUniqueId()</span> |
| 3-15 | BlockPos | <span style="color:BurlyWood">{ x: readVarInt(),<br>y: readVarUint(),<br>z: readVarInt() }</span> | <span style="color:Plum">getBlockPosition()</span> |

### Examples

#### TextPacket:
```ts
    import { MinecraftPacketIds, nethook } from "bdsx";
    nethook.raw(MinecraftPacketIds.Text).on((pointer, size, networkIdentifier, packetId) => {
        const data: {[prop: string]: any} = {};
        pointer.move(1); // Skips the packet id
        data.type = pointer.readUint8();
        data.needsTranslation = pointer.readBoolean();
        switch (data.type) {
            case 1:
            case 7:
            case 8:
                data.sourceName = pointer.readVarString();
            case 0:
            case 5:
            case 6:
            case 9:
            case 10:
                data.message = pointer.readVarString();
                break;
            case 2:
            case 3:
            case 4:
                data.message = pointer.readVarString();
                data.params = [];
                for (let i = 0; i < pointer.readVarUint(); i++) {
                    data.params.push(data.readVarString());
                }
                break;
        }
        data.xboxUserId = data.readVarString();
        data.platformChatId = data.readVarString();
    });
 ```
 (Referenced from [PMMP](https://github.com/pmmp/PocketMine-MP/blob/stable/src/pocketmine/network/mcpe/protocol/TextPacket.php))

#### PlayerActionPacket:
```ts
    import { MinecraftPacketIds, nethook } from "bdsx";
    netevent.raw(MinecraftPacketIds.PlayerAction).on((pointer, size, networkIdentifier, packetId) => {
        const data: {[prop: string]: any} = {};
        ptr.move(2); // Skips the packet id and entityRuntimeId
        data.action = pointer.readVarInt();
        if (action === 21) { // Test if the actions is StartSwimming
            return CANCEL; // Cancels the packet
        }
    });
```
 (Referenced from [PMMP](https://github.com/pmmp/PocketMine-MP/blob/stable/src/pocketmine/network/mcpe/protocol/PlayerActionPacket.php))

## C++ Packets

For C++ Packets, there are a few predefined decoding and encoding methods in BDSX due to the complexity of some packets. If you want to know to decode or encode them, you need a disassmbler to understand it and manipulate them like `MantleClass` in BDSX. Note that C++ packet listeners do not return the size.

### Examples

#### TextPacket:
```ts
    import { MinecraftPacketIds, nethook } from "bdsx";
    nethook.before(MinecraftPacketIds.Text).on((packet, networkIdentifier, packetId) => {
        console.log(packet.type, packet.needsTranslation, packet.name, packet.message, packet.params);
        packet.message = "Another message"; // Changes the message
    });
```

#### StartGamePacket:
```ts
    import { MinecraftPacketIds, nethook } from "bdsx";
    nethook.before(MinecraftPacketIds.StartGame).on((packet, networkIdentifier, packetId) => {
        packet.settings.seed = 123; // Changes the seed
    });
```

#### SpawnExperienceOrbPacket:
```ts
    import { MinecraftPacketIds, nethook } from "bdsx";
    nethook.before(MinecraftPacketIds.SpawnExperienceOrb).on((packet, networkIdentifier, packetId) => {
        return CANCEL; // Cancels the packet
    });
```