# Universal

Codes that appear in both directions. Incoming ones arrive through
`IPhotonPeerListener.OnEvent` ([OnEvent.md](OnEvent.md)), outgoing ones go out as op `253`
([Operations.md](Operations.md#op-253--raise-event)).

### Code: 1
- **Description**: Voice
- **Data Type**: `BYTE[]`
- **Details**: Represents voice data as an array of bytes.
- **Update** No longer has actor number in BYTE[]
- Starts with a server timestamp, followed by Opus frames. Each frame carries its own
  sequence number and length, so one packet can hold several frames — the client sends a
  couple at a time rather than one per frame.

### Code: 7
- **Description**: Serialization Unreliable (Object and Movement Sync)
- **Data Type**: `BYTE[]`
- **Details**: Unreliable serialization data for object like new VRChat Drone
```c#
ViewId {BitConverter.ToInt32(Data, 0)}
ServerTime {BitConverter.ToInt32(Data, 4)}
```
- Also usable as a movement channel instead of Code 12 — the payload carries a position
  and a rotation, and the ViewId for a player is derived from their player id.
- The bytes between the header and the position are animator/pose state. Most of them do
  nothing visible, a few change the played animation, and a couple break the avatar.

### Code: 6
- **Description**: RPC Trigger
- **Data Type**: `BYTE[]` (convertible to `String`)
- Examples of decode
```c#
//Bad code example but its work
        public static string ParseEV6(byte[] packet)
        {
            if (packet == null || packet.Length < 26)
            {
                return "";
            }
            string parsedString = Encoding.UTF8.GetString(packet, 26, packet.Length - 26);
            parsedString = parsedString.Replace("\0", "").Replace("\t", " ").Replace("\n", "").Replace("\r", "").Trim();
            parsedString = Regex.Replace(parsedString, @"[\x00-\x1F]", "");
            return parsedString;
        }
//Out Examples
//(Camera) >> ChangeVisibility
//(Camera) >> TimerBloop
//(Camera) >> PhotoCapture
//>> PlayEmoteRPC
//>> SanityCheck!
//>> nController  CancelRPC

```
- **Details**: Data for an RPC call or Udon trigger, stored as bytes that can be converted into a string.

### Code: 11
- **Description**: Udon Sync
- **Data Type**: `BYTE[]`
- **Details**: Synchronization data for Udon behaviors, stored as bytes.
- Short header — the network id of the object, a message id, and a chunk counter — then a
  FlatBuffers buffer with the synced variables.
- Large syncs are split across several events sharing one message id. A single chunk is
  not parseable on its own; the buffer only becomes valid once the whole message is
  reassembled.
- Variables come in two flavours: **named**, where the packet carries the variable name
  and a type tag, and **positional**, where it carries neither and the meaning comes from
  the Udon program itself.
- Compare with [Code 10](OnEvent.md#code-10), which does the same job for scene-owned
  objects in a smaller, never-chunked format.

### Code: 12
- **Description**: Movement
- **Data Type**: `BYTE[]`
- **Details**: Movement-related data, stored as an array of bytes.
- Begins with a server timestamp and a flag telling whether the packet includes tracking
  data. If it does, position, rotation and the IK payload follow; if not, the packet is
  much shorter.
- Rotation is not stored as four floats — it is compressed, which is why a value read out
  and written back is never bit-identical.
- Packet length says something about the sender: full-body and VR packets are long,
  trackingless ones are tiny.

### Code: 13
- **Description**: Avatar Parameter Sync
- **Data Type**: `BYTE[]`
- **Details**: Synchronization data for avatar parameters, stored as bytes.

### Code: 14
- **Description**: Props
- **Data Type**: `BYTE[]`
- **Details**: Same as 7 but for Props.

### Code: 16
- **Description**: Object Sync
- **Data Type**: `BYTE[]`
- Same idea as Code 7 but for world objects: the object's like cameras view id and a server timestamp
  up front, a position further in.

### Code: 17
- **Description**: VRCPickup
- **Data Type**: `BYTE[]`
- **Details**: Same as 7 but for Pickups.

### Code: 18
- **Description**: UdonSync — Udon RPC call
- **Data Type**: `Dictionary<System.Byte, Il2CppSystem.Object>`
- **Keys**:

| Key | Type | Meaning |
|---|---|---|
| `0` | Byte | action |
| `1` | Int32 | hashed entrypoint |
| `2` | Int32 | view id |
| `3` | Byte | argument count |
| `4` | Byte[] | arguments, FlatBuffers |
| `255` | UInt32 | unidentified |

- Without arguments — keys `0`, `1`, `2` only:
```
1(Byte) : 613549406(Int32) // hashed entrypoint
0(Byte) : 2(Byte) // action
2(Byte) : 450(Int32) //view id
```
- With arguments — keys `3`, `4` and `255` appear:
```
0(Byte)   : 2(Byte)
1(Byte)   : 431490142(Int32)
2(Byte)   : 324(Int32)
3(Byte)   : 1(Byte)
4(Byte)   : [12,0,0,0,8,0,8,0,0,0,4,0,8,0,0,0,4,0,0,0,0,0,0,0](Byte[])
255(Byte) : 809633187(UInt32)
```
- Key `4` is a FlatBuffers table. The one above holds a single int argument — matching
  key `3` = 1.
- Key `1` is a hash of the Udon entry point name. It is unsigned but travels in a signed
  field, so plenty of them show up negative.
- Key `2` is the photon object id and can be matched against the scene's Udon objects.
- Codes `10`, `11` and `18` together are the Udon sync family.

### Code: 22
- **Description**: Take Ownership
- **Data Type**: `Int32[]`
  - **Contents**:
```
  Length: 2 | Int32:
  [ViewId, Your Actor Num]
```
- **Details**: Event to take ownership of an object or entity.
- Ownership is first-come and not exclusive — the last raise wins, and nothing
  acknowledges or rejects a contested object.

### Code: 33
- **Description**: Moderation Action / Instance Control
- **Data Type**: `Dictionary<byte, object>`
- **Subcode** lives in key `0`:


- **Example Block Someone. You Can Use That For Anti Block**
```csharp
        internal static void OnEventBlock(int photonid, bool Block)
        {
                Networking.OnEvent(33, new Dictionary<byte, object>
                {
                    {0,(byte)21},
                    {1,photonid},
                    {10,Block},
                    {11,false}
                },0);
        }
```

### Code: 43
- **Description**: ChatBox Message
- **Data Type**: `String`
- **Example**
```csharp
        internal static void RaiseSendChatboxMessage(string msg)
        {
            RaiseEvent(43, msg, new RaiseEventOptions
            {
                Caching = EEvent_Caching.DoNotCache,
                Receiver = EEvent_Reciever.Others
            }, default(SendOptions));
        }
```
- **Details**: Text message for the chatbox.
- Rate-limited; the limit is one of the values the server publishes in
  [Code 34](OnEvent.md#code-34).

### Code: 44
- **Description**: ChatBox Typing Indicator
- **Data Type**: `Byte`
- **Content**:
```
0 is ChatBox Typing OFF
1 is ChatBox Typing ON
```
- **Details**: Indicates whether the user is typing in the chatbox.


### Code: 60
- **Description**: PhysBonesPermissions
- **Data Type**: `System.Int32[]`
