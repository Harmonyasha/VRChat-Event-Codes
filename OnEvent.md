# OnEvent

Incoming events, hooked at `IPhotonPeerListener.OnEvent(EventData)`. The code lives in
`EventData.Code`, the payload in `EventData.CustomData`, the origin actor in
`EventData.Sender`. Outgoing traffic is a separate number space — see
[Operations.md](Operations.md).

`Sender` is the origin actor for most codes, but not all:

- Server-relayed object events — **Code 74**, and `20`, `24`, `51`, `53` — arrive with
  sender `0`. The real owner has to be taken from the payload, see [Event74.md](Event74.md).
- Event `226` (`AppStats`) arrives with sender `-1`.

### Code: 2
- **Description**: Message
- **Data Type**: System.String
- **Details**: Show message on screen

### Code: 3
- **Description**: Smth with sync like ev5
- **Data Type**: nullptr
- **Details**: sender is player id


### Code: 5
  - ---- SendOperation ----
  - Code: 253
  - Count: 2
  - Data Content: 

  - Key: 244 Value: 5 EVENT CODE
  - Key: 252 Value: [58,] PLAYERID
  - **Data Type**: `parameterdictionary`
  - **Note**: request data sync from master

### Code: 8
- **sender**: 0
- **Description**: Is Someone hear you?
- **Data Type**: `bool`
- **Details**: true/false
- **Note**: the outgoing Code 8 is a completely different payload — see
  [OpRaise.md](OpRaise.md#code-8).

### Code: 10
- **Description**: Udon variable sync for scene objects
- **Data Type**: `BYTE[]`
- **Details**: Carries the synced variables of world-owned Udon objects — doors, drawers,
  cabinets, game-state holders. Same idea as Code 11, but for objects that belong to the
  scene rather than to a player, and in a much more compact form: these packets are a few
  dozen bytes and are never chunked.
- Opens with the object's network id and a timestamp, the same way Code 11 does. The ids
  are low numbers, which is what marks them as scene objects.
- Captures below — the id repeats for the same object, the tail is the actual variable
  value:
```
[29,1,0,0, 86,32,62,237, ...]     id 285   game object, ~10 variables
[95,2,0,0, 184,43,62,237, ...]    id 607   Door (12)   one flag
[71,2,0,0, 112,63,62,237, ...]    id 583   Door (17)   one flag
[134,4,0,0, 217,39,62,237, ...]   id 1158  Drawer      one number
[233,4,0,0, 145,73,62,237, ...]   id 1257  Drawer      one number
```
- Objects running the same Udon program produce identically shaped packets — every door
  in the world looks the same on the wire, every drawer looks the same, and the game-state
  objects share their own shape. The layout follows the program, not the instance.
- A doorlike object syncs a single flag; a drawer syncs a small number that walks between
  a few states; the role/health objects push a whole row of numbers at once.
- In these captures every Code 10 arrived from the same actor while four different players
  were the ones opening things — consistent with the owner of the scene objects relaying
  the state rather than the interacting player sending it.
- Sits next to Code 18 in a normal interaction: the RPC fires the behaviour, Code 10
  carries the variable state that results from it.
- Part of the Udon sync family together with `11` and `18`.

### Code: 20
- **Description**: Join World
- **Sender**: `0`
- **Data Type**: `NULL`
- **Details**: No data provided.

### Code: 21
- **Description**: Idk
- **Data Type**: `Int32[]`
- Content
```
164(Int32)
0(Int32)
```
- **Details**: No data provided.

### Code: 24
- **Description**: Instance snapshot — the state of the instance's Udon objects, sent on join
- **Sender**: `0`
- **Data Type**: `Object[]`
- **Contents**:
```
  -100894439 (Int32)   msgId
  1 (Int32)            chunkCount
  0 (Int32)            chunkIndex
  System.Byte[]        chunk
```
- The chunks belong to one message and have to be put back together in order. What comes
  out is compressed, and inside that sits a FlatBuffers buffer with the instance state.
- The data of a single object can itself be split up again inside that buffer, and not in
  the order you would expect — so a raw, un-reassembled snapshot shows short fields fine
  while long ones look like they are not there at all.
- This only arrives **once**, at join. There is nothing to capture later in the session.
- The outgoing counterpart is [Code 25](OnEvent.md#code-25), below.


### Code: 25
- **Description**: Instance snapshot, outgoing — the counterpart of [Code 24](OnEvent.md#code-24)
- **Data Type**: `System.Object[]`
- **Contents**:
```
(System.Int32)  5     how many chunks the snapshot was split into
(System.Int32)  0     which chunk this is — 0,1,2,3,4 across the five events
(System.Byte[]) ...   the chunk itself
```
- Note the difference from Code 24, which also carries a message id. Here there is only a
  count and an index.
- The chunks are one continuous compressed stream — the first one opens with a gzip
  header and the last one closes it. Nothing is parseable until they are joined.
- Inflated, it is the instance's object table: no readable text at all, just an index
  followed by records keyed by object id. The ids land in the same range as the scene
  objects seen on [Code 10](OnEvent.md#code-10), and the network-id constants are the same
  ones the rest of the protocol uses.
- Chunks are the same size the other chunked events use, so a snapshot of a busy world
  turns into a handful of these back to back.
- Goes out reliably and on its own channel — this is bulk state transfer, not sync
  traffic, and it does not behave like the serialization events.


### Code: 26
- **Data Type**: `System.Boolean`
- **Details**: Purpose unknown. See [UnSorted.md](UnSorted.md#code-26).

### Code: 28
- **Sender**: `0`
- **Data Type**: `INT32`
- **Details**: Last joined? Sending player_id of latest person.

### Code: 33
- See [Both.md](Both.md#code-33) for the subcode table. Incoming subcode `21` is the
  block/unblock: `{0:(Byte)21, 1:(Int32)photonId, 10:(bool)blocked, 11:(bool)false}`.



### Code: 34
- **Description**: Rate limit / event cooldown table
- **Data Type**: `Dictionary<System.Byte, System.Object>`
- **Contents**: key `0` holds a `Dictionary<System.Byte, System.Int32>` mapping
  **event code → cooldown**.
```
0(Byte) : Dictionary<Byte,Int32> {
    12 : <cooldown>,     // player serialization
    43 : <cooldown>,     // chatbox message
    74 : <cooldown>,     // world objects
    ...
}
```
- **Details**: The server tells the client what the per-event limits are, and the client
  is what applies them.

### Code: 35
- **Sender**: `0`
- **Data Type**: `nullptr`
- **Details**: Look like ping really idk

### Code: 42
- **Description**: User state broadcast (see [OpRaise.md](OpRaise.md#code-42))
- **Details**: There is no dedicated "avatar changed" event, but this one tends to show up
  around an avatar switch.

### Code: 51
- **Description**: OnLoaded? Idk really
- **Sender**:`0`
- **Data Type**: `Object[]`
  - **Values**:
```
[  (System.Int32)534 VIEWID , (System.Boolean)true, (System.Collections.Generic.Dictionary<System.String,System.Object>[])[System.Collections.Generic.Dictionary<System.String,System.Object>[]] System.Collections.Generic.Dictionary`2[System.String,System.Object][], ]
```

### Code: 53
- **Description**: Products
- **Sender**:`0`
- **Data Type**: `Object[]`
  - **Values**:
```
  ":20F/",
  "prod_c94d0f90-db70-4454-b06d-432f71e68778",
  [
   "Player UserName1",
   "Player UserName2"
  ]
```
- Element `[1]` is a `System.String` of udon object name or code dont really remember.
- Element `[2]` is a `System.String[]` of buyer display names.

### Code: 73 OnEvent
- **Description**: Allow Download Private Avatar
- **Data Type**: `Hashtable`
- **Contents**
```
variant (String) : security (String)
fileVersion (String) : 17 (Int32)
fileId (String) : file_84724ddf-50e1-4056-afcc-7b4aacf0feea (String)
avatarTokenExp (String) : 1742920646 (Int64)
avatarToken (String) : eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..9YP465-68jb2e6Qp.uCw84wta_jfjRMsmwotfXSrt6IdzqGUjz3G-y1DUwjK5hTdFX15-DOXqLQMLL5_D0Y6l7C6U-a4hMWW-LH_lkk0ADschZlAJRliJA1Z1QDLe-ZLKuR0fheN79lHLcTtL9MmVEveTitkVNr2mONDq17x-d6BdLpS4TBe4S4S8CL9FEtzaexhciINFAS5JgEpWQmx250P6F6es5dnvX-_dBhQbSNk_ulf12ZB4J4EZ.y-0jcKAHqOt7rxZUWNy2gw (String)
```
- **Details**: Data for allowing download of a private avatar.

### Code: 74 OnEvent
- **Description**: Portal, Pedestal, Sticker, Print, VRC+ gift drop, Drone Spawn, Camera, Inventory Prop
- **Sender**: `0` — **always**. The real owner is in key `3`, or key `2` for portals.
  Attributing this event by `Sender` attributes all of it to actor 0.
- **Discriminator**: key `0` = object kind. Kind table and per-kind key layouts:
  **[Event74.md](Event74.md)**

- **Portal Content**
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 1 | Value: 00 (System.Byte)
  Key: 0 | Value: 00 (System.Byte)
  Key: 3 | Value: usr_d56ec34e-f8de-4ef7-b1c9-cb99f2128173 (System.String)
  Key: 2 | Value: usr_d56ec34e-f8de-4ef7-b1c9-cb99f2128173 (System.String)
  Key: 4 | Value: System.Byte[]
      Length: 12 | Bytes:
      9A-8B-0E-42-00-A0-0B-3A-11-6B-54-C2
  Key: 5 | Value: System.Byte[]
      Length: 12 | Bytes:
      00-00-00-00-1D-C0-AE-43-00-00-00-00
  Key: 9 | Value: False (System.Boolean)
  Key: 129 | Value: 00 (System.Byte)
  Key: 128 | Value: Dynamic_452 (System.String)
  Key: 131 | Value: 1E (System.Byte)
  Key: 130 | Value: 11 (System.Byte)
  Key: 132 | Value: wbsz50n9 (System.String)
  Key: 133 | Value: Il2Dictionary<System.String, Il2CppSystem.Object>
      Key: id | Value: wrld_566ff86a-bd70-4037-9bd5-7bba189e9267 (System.String)
      Key: name | Value: 5 Пятёрочка Russian (System.String)
      Key: capacity | Value: 80 (System.Int32)
      Key: recommendedCapacity | Value: 35 (System.Int32)
      Key: releaseStatus | Value: public (System.String)
      Key: imageUrl | Value: https://api.vrchat.cloud/api/1/file/file_94e7f8e7-1d66-441f-9a75-b61421c80d03/8/file (System.String)
      Key: thumbnailImageUrl | Value: https://api.vrchat.cloud/api/1/image/file_94e7f8e7-1d66-441f-9a75-b61421c80d03/8/256 (System.String)
      Key: authorId | Value: usr_c92014af-a8ab-4f5f-a4d3-7e3ce8398ad4 (System.String)
      Key: authorName | Value: Vasya (System.String)
      Key: unityPackages | Value: Il2CppSystem.Object[]
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_9977991e-e276-4815-889a-390160e0bc24 (System.String)
              Key: unityVersion | Value: 2018.4.20f1 (System.String)
              Key: platform | Value: android (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_7459e0aa-6fca-4c9f-8b11-744e4472b8cd (System.String)
              Key: unityVersion | Value: 2019.4.29f1 (System.String)
              Key: platform | Value: android (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_a5469a28-5ed3-40ce-9561-1d1bec670820 (System.String)
              Key: unityVersion | Value: 2019.4.31f1 (System.String)
              Key: platform | Value: android (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_7293db91-abdb-4a5a-a576-6a5fefa007bf (System.String)
              Key: unityVersion | Value: 2022.3.6f1 (System.String)
              Key: platform | Value: android (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_bbac11cc-d41d-4656-be2e-840c21049201 (System.String)
              Key: unityVersion | Value: 2022.3.22f1 (System.String)
              Key: platform | Value: android (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_b5ab5406-dc8e-4b55-9556-e5374267af37 (System.String)
              Key: unityVersion | Value: 2017.4.28f1 (System.String)
              Key: platform | Value: standalonewindows (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_bd00e406-aedf-4f89-b86d-5f75165472d5 (System.String)
              Key: unityVersion | Value: 2018.4.20f1 (System.String)
              Key: platform | Value: standalonewindows (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_b0d7b8da-3f50-4189-993d-89d0a64d0dc0 (System.String)
              Key: unityVersion | Value: 2019.4.29f1 (System.String)
              Key: platform | Value: standalonewindows (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_99a0a9de-4818-4190-b8ca-9a8abcf455a0 (System.String)
              Key: unityVersion | Value: 2019.4.31f1 (System.String)
              Key: platform | Value: standalonewindows (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_95d3e2b7-3f95-45f4-bd52-425642e762b5 (System.String)
              Key: unityVersion | Value: 2022.3.6f1 (System.String)
              Key: platform | Value: standalonewindows (System.String)
          Il2Dictionary<System.String, Il2CppSystem.Object>
              Key: id | Value: unp_16cf8a08-ea3c-48ef-89ce-a00ac0d056d9 (System.String)
              Key: unityVersion | Value: 2022.3.22f1 (System.String)
              Key: platform | Value: standalonewindows (System.String)
      Key: tags | Value: Il2CppSystem.Object[]
          author_tag_russian (System.String)
          author_tag_rus (System.String)
          author_tag_5 (System.String)
          author_tag_russia (System.String)
          author_tag_ru (System.String)
          system_approved (System.String)
```

- **Pedestal Content**
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
- **Sticker Content** (legacy form, kind `2` — the current one is kind `8`)
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
- **Print Content**
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
- **GiftDrop Content**
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
- **Camera Content**
```
0(Byte)   : 6(Byte)
1(Byte)   : 0(Byte)
2(Byte)   : camera UID (String)
3(Byte)   : usr_... (String)
4(Byte)   : position (Byte[12])
5(Byte)   : rotation (Byte[12])
9(Byte)   : false (Boolean)
128(Byte) : viewId (Int32)
129(Byte) : 0 (Byte)    // skin
130(Byte) : -1 (Int32)  // -1 marks a camera; a drone controller has a real viewId here
133(Byte) : 0 (Byte)
```
- **Drone Spawn**
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
- **Drone Despawn**
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

### Code: 226
- **Description**: AppStats
- **Sender**: `-1`
- **Data Type**: `NULL`
- **Details**: AppStats triggered with no parameters. Photon-internal.

### Code: 253
- **Description**: SetProperties
- **Data Type**: `NULL`
- **Details**: SetProperties triggered with no parameters. Photon-internal

### Code: 254
- **Description**: Player Leave
- **Data Type**: `NULL`
- **Details**: Event indicating a player has left, no additional data. The actor is in

### Code: 255
- **Description**: Player Join
- **Data Type**: `NULL`
- **Details**: Event indicating a player has joined, no additional data. The actor is in
