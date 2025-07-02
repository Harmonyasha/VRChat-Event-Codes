# Universal

### Code: 1
- **Description**: Voice
- **Data Type**: `BYTE[]`
- **Details**: Represents voice data as an array of bytes.
- **Update** No longer has actor number in BYTE[]

### Code: 7
- **Description**: Serialization Unreliable (Object and Movement Sync)
- **Data Type**: `BYTE[]`
- **Details**: Unreliable serialization data for object like new VRChat Drone
```c#
ViewId {BitConverter.ToInt32(Data, 0)}
ServerTime {BitConverter.ToInt32(Data, 4)}
```


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

### Code: 12
- **Description**: Movement
- **Data Type**: `BYTE[]`
- **Details**: Movement-related data, stored as an array of bytes.

### Code: 13
- **Description**: Avatar Parameter Sync
- **Data Type**: `BYTE[]`
- **Details**: Synchronization data for avatar parameters, stored as bytes.

### Code: 16
- **Description**: Object Sync 
- **Data Type**: `BYTE[]`

### Code: 17
- **Description**: VRCPickup
- **Data Type**: `BYTE[]`
- **Details**: Same as 12 but for Pickups.

### Code: 18
- **Description**: UdonSync
- **Data Type**: `Dictionary<System.Byte, Il2CppSystem.Object>`
  - **Contents**:
 ```
1(Byte) : 613549406(Int32) // hashed entrypoint
0(Byte) : 2(Byte) // action
2(Byte) : 450(Int32) //view id
```

### Code: 22
- **Description**: Take Ownership
- **Data Type**: Not specified
- **Details**: Event to take ownership of an object or entity.
- **Data Type**: `Int32[]`
  - **Contents**:
 ```
  Length: 2 | Int32:
  [ViewId, Your Actor Num]
```

### Code: 33
-    **Description**: Moderation Action
-    **Data Type**: `Dictinary<object,object>()`
-    **Example Block Someone. You Can Use That For Anti Block**
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
-    **Details**: Event related to a moderation action.


  ### Code: 43
-    **Description**: ChatBox Message
-    **Data Type**: `String`
-    **Example**
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
-    **Details**: Text message for the chatbox.


  ### Code: 44
-    **Description**: ChatBox Typing Indicator
-    **Data Type**: `Byte`
-    **Content**:
```
0 is ChatBox Typing OFF
1 is ChatBox Typing ON
```
-    **Details**: Indicates whether the user is typing in the chatbox.


  ### Code: 60
-    **Description**: PhysBonesPermissions 
-    **Data Type**: `System.Int32[]`