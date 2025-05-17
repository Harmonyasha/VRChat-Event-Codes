# Event Codes and Their Data Types
## Disclaimer

This document is a collection of event codes and their details that I have discovered through personal exploration. The information about some events may be inaccurate or incomplete, as it is based on my observations and limited resources. I take no responsibility for any inaccuracies or errors present here. This work was done purely out of enthusiasm and curiosity, and it is shared as-is for informational purposes only.

## Events
### Code: 1
- **Description**: Voice
- **Data Type**: `BYTE[]`
- **Details**: Represents voice data as an array of bytes.
- **Update** No longer has actor number in BYTE[]

### Code: 4
- **Description**: Master Sync
- **Data Type**: `INT32`
- **Details**: Synchronization data for the master.

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

### Code: 7
- **Description**: Serialization Unreliable (Object and Movement Sync)
- **Data Type**: `BYTE[]`
- **Details**: Unreliable serialization data for object like new VRChat Drone and movement synchronization, transmitted as an array of bytes also you can use it as movement just send EV7 instead of EV12.

### Code: 8 WAS
- **sender**: 0
- **Description**: Voice Listeners
- **Data Type**: `INT32[]`
- **Details**: Array of 32-bit integers indicating which players can hear the voice.

### Code: 8 NOW
- **sender**: 0
- **Description**: Is Someone hear you?
- **Data Type**: `bool`
- **Details**: true/false

### Code: 10
- **Description**: Udon (Unknown Purpose)
- **Data Type**: `BYTE[]`
- **Details**: Udon-related data, purpose unclear, stored as an array of bytes.

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
- **Data Type**: `Dictionary`
  - **Contents**:
 ```
1(Byte) : 613549406(Int32) // string hash prob
0(Byte) : 2(Byte) // action
2(Byte) : 450(Int32) //view id
```


### Code: 20 OnEvent
- **Description**: Join World
- **Sender**: `0`
- **Data Type**: `NULL`
- **Details**: No data provided.

### Code: 21 OnEvent
- **Description**: Idk
- **Data Type**: `Int32[]`
- Content
```
164(Int32)
0(Int32)
```
- **Details**: No data provided.

### Code: 22
- **Description**: Take Ownership
- **Data Type**: Not specified
- **Details**: Event to take ownership of an object or entity.

### Code: 24 OnEvent
- **Description**: Unknown Array
- **Sender**: `0`
- **Data Type**: `Object[]`
- **Contents**:
 ```
  -100894439 (Int32)
  1 (Int32)
  0 (Int32)
  System.Byte[] (Byte[])
```
### Code: 26
-    **Description**: Possible Allow Interaction
-    **Data Type**: System.Boolean
-    **Details**: Boolean flag, possibly indicating whether interaction is allowed (purpose unclear).
  
### Code: 28 OnEvent
-    **Description**: IDK Random Numbers
-   **Sender**:`0`
-    **Data Type**: `INT32`
-    **Details**: Random 32-bit integer, purpose unknown.

### Code: 33
-    **Description**: Moderation Action
-    **Data Type**: `Dictinary<object,object>()`
-    **Example Block Someone You Can Use That For Anti Block**
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

### Code: 42 OpRaise
-    **Description**: VR Mode State Broadcast
-    **Data Type**: `Hashtable`
-    **Content**:
```csharp
        internal static void RaiseVRMode(bool state)
        {
            var hashtable = new System.Collections.Hashtable();
            hashtable.Add("inVRMode", state);
            RaiseEvent(42, hashtable, new RaiseEventOptions
            {
                Caching = EEvent_Caching.DoNotCache,
                Receiver = EEvent_Reciever.Others
            }, default(SendOptions));
        }

```
-    **Details**: Broadcasts the VR mode state (e.g., whether the player is in VR mode) to others.



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

### Code: 51 OnEvent
-    **Description**: OnLoaded
-   **Sender**:`0`
-    **Data Type**: `Object[]`
   - **Values**:
```
129(Int32) -your photonid?
True(Boolean)
```

### Code: 53 OnEvent
-    **Description**: Products
-   **Sender**:`0`
-    **Data Type**: `Object[]`
   - **Values**:
```
  ":20F/",
  "prod_c94d0f90-db70-4454-b06d-432f71e68778",
  [
   "Player UserName1",
   "Player UserName2"
  ]
```
  
  ### Code: 60
-    **Description**: PhysBonesPermissions 
-    **Data Type**: `Byte[]` or `Int32[]` not sure

  ### Code: 66 OpRaise
-    **Description**: EACHeartBeat (OpRaise Only)
-    **Data Type**: `Byte[]`
-    **Details**: An array of integers associated with the EAC connection.

  ### Code: 71
-    **Description**: Emoji
-    **Data Type**: `Dictionary<byte, object>`
-    **Contents**
  ```
0 (Byte) : System.Runtime.Serialization.TypeLoadExceptionHolder (TypeLoadExceptionHolder)
2 (Byte) : EmojiID (Int32)
```
-    **CustomEmoji**
  ```
0(Byte) : 1(Byte)
1(Byte) : file_0f514c10-7ce5-4694-9f62-74e328bab6fd(String)
```

-    **Details**: Dictionary mapping byte keys to emoji-related data.

  ### Code: 73 OnEvent
-    **Description**: Allow Download Private Avatar
-    **Data Type**: `Hashtable`
-    **Contents**
  ```
variant (String) : security (String)
fileVersion (String) : 17 (Int32)
fileId (String) : file_84724ddf-50e1-4056-afcc-7b4aacf0feea (String)
avatarTokenExp (String) : 1742920646 (Int64)
avatarToken (String) : eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..9YP465-68jb2e6Qp.uCw84wta_jfjRMsmwotfXSrt6IdzqGUjz3G-y1DUwjK5hTdFX15-DOXqLQMLL5_D0Y6l7C6U-a4hMWW-LH_lkk0ADschZlAJRliJA1Z1QDLe-ZLKuR0fheN79lHLcTtL9MmVEveTitkVNr2mONDq17x-d6BdLpS4TBe4S4S8CL9FEtzaexhciINFAS5JgEpWQmx250P6F6es5dnvX-_dBhQbSNk_ulf12ZB4J4EZ.y-0jcKAHqOt7rxZUWNy2gw (String)
```
-    **Details**: Data for allowing download of a private avatar.

  ### Code: 73 OpRaise
-    **Description**: Allow Download Private Avatar
-    **Data Type**: `String`
-    **Contents**
  ```
file_90cbd832-2e87-4b5a-b7a7-e70390767ffe:11:security
```
-    **Details**: Data for allowing download of a private avatar.


  ### Code: 74 OnEvent
-    **Description**: Portal,Pedestrial,Sticker,Print,Vrc+ gift drop,Drone Spawn
-    **Sender**: `0`
-    **Portal Content (Soon)**
  ```
Failed to serialize data Exception err: System.Runtime.Serialization.SerializationException: Invalid BinaryFormatter stream.
System.InvalidCastException: Object must implement IConvertible.
```
-    **Pedestrial Content**
  ```
1(Byte) : 0(Byte)
0(Byte) : 1(Byte)
3(Byte) : "usr_d3a5c087-0332-4881-99f3-ac865854bb9d"(String)
2(Byte) : "file_ec3543e4-6c6c-4470-b5a9-2b60dae1098a"(String)
4(Byte) : [7E-CF-6B-C1-00-00-00-00-1A-B6-B5-C1](Byte[])
5(Byte) : [00-00-00-00-55-3D-00-43-00-00-00-00](Byte[])
9(Byte) : false(Boolean)
128(Byte) : 0(Byte)
130(Byte) : [](String[])
8(Byte) : 0(Byte)
```
-    **Sticker Content**
  ```
1(Byte) : 0(Byte)
0(Byte) : 2(Byte)
3(Byte) : "usr_d3a5c087-0332-4881-99f3-ac865854bb9d"(String)
2(Byte) : "cd46af49-7a2e-4970-9a29-416a6d6cd6e2"(String)
4(Byte) : 62-DF-8D-C1-8C-98-F0-3E-EA-C6-B3-C1(Byte[])
5(Byte) : 80-96-0C-42-88-7B-4E-43-63-12-F7-40(Byte[])
9(Byte) : false(Boolean)
128(Byte) : 39(Int32)
129(Byte) : "file_1916e65e-c761-4a83-9f7e-4bbfe311825d"(String)
130(Byte) : 0.521622(Single)
```
-    **Print Content**
  ```
1(Byte) : 128(Byte)
0(Byte) : 4(Byte)
3(Byte) : "usr_924468d4-cab2-451c-baae-93ffc666c8fb"(String)
2(Byte) : "fbfc1b63-b420-478b-8b7e-55493455a5a6"(String)
4(Byte) : [15-DA-84-C1-34-86-2E-3F-0A-4E-A6-C1](Byte[])
5(Byte) : [00-00-00-80-12-43-71-C3-00-00-00-80](Byte[])
9(Byte) : false(Boolean)
128(Byte) : "prnt_eb469bd1-036f-42dc-8ce0-840879b61aa4"(String)
129(Byte) : 77110007(Int32) PhotonID
130(Byte) : ""(String)
131(Byte) : 0.75(Single)
132(Byte) : [15-DA-84-C1-34-86-2E-3F-0A-4E-A6-C1](Byte[])
133(Byte) : [00-00-00-80-12-43-71-C3-00-00-00-80](Byte[])
```
-    **GiftDrop Content**
  ```
"0":0
"1":2
"2":"usr_463e6a09-d88f-4f4c-a2c5-139d6db384f2"
"3":"usr_463e6a09-d88f-4f4c-a2c5-139d6db384f2"
"4":[24,102,234,193,9,64,217,66,110,49,157,65]
"5":[0,0,0,0,87,230,153,67,0,0,0,0]
"128":"Dynamic_3"
"129":0
"130":42
"131":30
```
-    **Drone Spawn**
  ```
Part1
1(Byte) : 0(Byte)
0(Byte) : 5(Byte)
3(Byte) : usr_924468d4-cab2-451c-baae-93ffc666c8fb(String)
2(Byte) : 1645d211-69d2-4385-936d-484e0a50ff08(String)
4(Byte) : 65-3D-84-C1-C7-09-39-3F-78-E9-A5-C1(Byte[])
5(Byte) : 00-00-00-80-DB-F8-BF-42-00-00-00-00(Byte[])
9(Byte) : false(Boolean)
128(Byte) : 77110005(Int32)
129(Byte) : 00-00-80-3F-00-00-80-3F-00-00-80-3F(Byte[])
130(Byte) : 00-00-80-3F-00-00-80-3F-00-00-80-3F(Byte[])
131(Byte) : 00-00-80-3F-00-00-80-3F-00-00-80-3F(Byte[])

Part2
1(Byte) : 0(Byte)
0(Byte) : 6(Byte)
3(Byte) : usr_924468d4-cab2-451c-baae-93ffc666c8fb(String)
2(Byte) : 2ba9bd25-96d0-4d26-86b6-b5025fa16382(String)
4(Byte) : 5B-DF-83-C1-AA-E4-31-3F-54-F3-A5-C1(Byte[])
5(Byte) : 00-00-00-80-DC-F8-BF-42-00-00-00-80](Byte[])
9(Byte) : false(Boolean)
128(Byte) : 77110006](Int32)
129(Byte) : 2(Byte)
130(Byte) : 77110005(Int32)
133(Byte) : 0(Byte)
```
-    **Drone Despawn**
  ```
Part1
1(Byte) : 1(Byte)
0(Byte) : 5(Byte)
3(Byte) : "usr_924468d4-cab2-451c-baae-93ffc666c8fb"(String)
2(Byte) : "1645d211-69d2-4385-936d-484e0a50ff08"(String)

Part2
1(Byte) : 1(Byte)
0(Byte) : 6(Byte)
3(Byte) : usr_924468d4-cab2-451c-baae-93ffc666c8fb(String)
2(Byte) : 2ba9bd25-96d0-4d26-86b6-b5025fa16382(String)
```
  ### Code: 74 OpRaise
-    **Description**: SpawnPortal
-    **Data Type**: `Dictionary<byte, Il2CppSystem.Object>`
-    **Contents**
  ```
0(Byte) : System.Runtime.Serialization.TypeLoadExceptionHolder(TypeLoadExceptionHolder)
132(Byte) : 6saucwx9(String)
1(Byte) : System.Runtime.Serialization.TypeLoadExceptionHolder(TypeLoadExceptionHolder)
6(Byte) : 30(Int32)
7(Byte) : False(Boolean)
2(Byte) : usr_YourPlayerId(String)
8(Byte) : System.Runtime.Serialization.TypeLoadExceptionHolder(TypeLoadExceptionHolder)
4(Byte) : System.Byte[](Byte[])
5(Byte) : System.Byte[](Byte[])
```
-    **Details**: None


  ### Code: 202
-    **Description**: Instantiate 
-    **Data Type**: `Hashtable`
-    **Contents**:
  ```
6 (Byte) : -2111675201 (Int32)
4 (Byte) : System.Int32[] (Int32[])
0 (Byte) : VRCPlayer (String)
7 (Byte) : 13000001 (Int32)
```
-    **Details**: Instantiate  event with a hashtable of various data.

  ### Code: 226
-    **Description**: AppStats 
-    **Sender**: `-1`
-    **Data Type**: `NULL`
-    **Details**: AppStats triggered with no parameters.

  ### Code: 253
-    **Description**: SetProperties
-    **Data Type**: `NULL`
-    **Details**: SetProperties triggered with no parameters.

  ### Code: 254
-    **Description**: Player Leave
-    **Data Type**: `NULL`
-    **Details**: Event indicating a player has left, no additional data.

  ### Code: 255
-    **Description**: Player Join
-    **Data Type**: `NULL`
-    **Details**: Event indicating a player has joined, no additional data.
