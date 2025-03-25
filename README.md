# Event Codes and Their Data Types
## Disclaimer

This document is a collection of event codes and their details that I have discovered through personal exploration. The information about some events may be inaccurate or incomplete, as it is based on my observations and limited resources. I take no responsibility for any inaccuracies or errors present here. This work was done purely out of enthusiasm and curiosity, and it is shared as-is for informational purposes only.

## Events
### Code: 1
- **Description**: Voice
- **Data Type**: `BYTE[]`
- **Details**: Represents voice data as an array of bytes.

### Code: 2, 3, 4, 5
- **Description**: Master Sync
- **Data Type**: `INT32` or `FLOAT` (assumed based on "Hz")
- **Details**: Synchronization data for the master, likely in Hertz (frequency), across four event codes.

### Code: 6
- **Description**: RPC/Udon Trigger
- **Data Type**: `BYTE[]` (convertible to `String`)
- **Details**: Data for an RPC call or Udon trigger, stored as bytes that can be converted into a string.

### Code: 7
- **Description**: Serialization Unreliable (Object and Movement Sync)
- **Data Type**: `BYTE[]`
- **Details**: Unreliable serialization data for object and movement synchronization, transmitted as an array of bytes.

### Code: 8
- **Description**: Voice Listeners
- **Data Type**: `INT32[]`
- **Details**: Array of 32-bit integers indicating which players can hear the voice.

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
- **Description**: Image Print Sync
- **Data Type**: `BYTE[]`
- **Details**: Data for synchronizing image printing, stored as bytes.

### Code: 17
- **Description**: Unknown
- **Data Type**: `BYTE[]`
- **Details**: Unknown purpose, stored as an array of bytes.

### Code: 20
- **Description**: Unknown
- **Sender**: `0`
- **Data Type**: `NULL`
- **Details**: No data provided.

### Code: 22
- **Description**: Take Ownership
- **Data Type**: Not specified
- **Details**: Event to take ownership of an object or entity.

### Code: 24
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
  
### Code: 28
-    **Description**: IDK Random Numbers
--   **Sender**:`0`
-    **Data Type**: `INT32`
-    **Details**: Random 32-bit integer, purpose unknown.

### Code: 33
-    **Description**: Moderation Action
-    **Data Type**: `Dictinary<object,object>()`
-    **Details**: Event related to a moderation action.

### Code: 42
-    **Description**: VR Mode State Broadcast
-    **Data Type**: `Hashtable`
-    **Details**: Broadcasts the VR mode state (e.g., whether the player is in VR mode) to others.

  ### Code: 43
-    **Description**: ChatBox Message
-    **Data Type**: `String`
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

### Code: 51
-    **Description**: Unknown Array
--   **Sender**:`0`
-    **Data Type**: `Object[]`
   - **Values**:
```
129(Int32)
True(Boolean)
```
-    **Contents**: Random 32-bit integer, purpose unknown.
  
  ### Code: 60
-    **Description**: IDK Possible EAC Connection
-    **Data Type**: `Int32[]`
-    **Details**: Array of integers, possibly related to Easy Anti-Cheat (EAC) connection status.

  ### Code: 66
-    **Description**: Unknown (OpRaise Only)
-    **Data Type**: `Byte[]`
-    **Details**: Byte array, purpose unclear, only used with `OpRaise`.

  ### Code: 71
-    **Description**: Emoji
-    **Data Type**: `Dictionary<byte, object>`
-    **Contents**
  ```
0 (Byte) : System.Runtime.Serialization.TypeLoadExceptionHolder (TypeLoadExceptionHolder)
2 (Byte) : EmojiID (Int32)
```
-    **Details**: Dictionary mapping byte keys to emoji-related data.

    ### Code: 73
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

  ### Code: 74
-    **Description**: Unknown Event
-    **Sender**: `0`
-    **Data Type**: `Dictionary<byte, Il2CppSystem.Object>`
-    **Contents**
  ```
1 (Byte) : 1 (Byte)
0 (Byte) : 2 (Byte)
3 (Byte) : usr_c5fc3234-1f60-33c0-84f1-2c2cbc8ef8b5 (String)
2 (Byte) : bcbc3645-4cdf-443d-9620-c2b631feb296 (String)
```
-    **Details**: Unknown event with a dictionary of byte-to-object mappings.

    ### Code: 202
-    **Description**: Sanity Check???
-    **Data Type**: `Hashtable`
-    **Contents**:
  ```
6 (Byte) : -2111675201 (Int32)
4 (Byte) : System.Int32[] (Int32[])
0 (Byte) : VRCPlayer (String)
7 (Byte) : 13000001 (Int32)
```
-    **Details**: Sanity check event with a hashtable of various data.

  ### Code: 226
-    **Description**: Unknown (OnEvent)
-    **Sender**: `-1`
-    **Data Type**: `NULL`
-    **Details**: Unknown event triggered with no parameters.

  ### Code: 253
-    **Description**: Unknown (OnEvent)
-    **Data Type**: `NULL`
-    **Details**: Unknown event triggered with no parameters.

    ### Code: 254
-    **Description**: Player Leave
-    **Data Type**: `NULL`
-    **Details**: Event indicating a player has left, no additional data.

    ### Code: 255
-    **Description**: Player Join
-    **Data Type**: `NULL`
-    **Details**: Event indicating a player has joined, no additional data.
