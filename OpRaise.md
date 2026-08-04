# OpRaise

Outgoing events. "OpRaise" is `PhotonPeer.SendOperation(253, ParameterDictionary, SendOptions)`
where parameter `244` is the event code and parameter `245` is the payload — see
[Operations.md](Operations.md#op-253--raise-event). The `SendOptions` that go with them —
channel, delivery mode and the `Reliability` trap — are described
[there too](Operations.md#sendoptions).

### Code: 1
- **Description**: Voice
- **Data Type**: `BYTE[]`
- A server timestamp, then Opus frames. Each frame carries its own sequence number and
  length, so several fit in one packet.
- Voice does not share a channel or a delivery mode with the rest of the events.


### Code: 8
- **Description**: Serialization interest table
- **Data Type**: `BYTE[]`
- **History**: this used to be a plain `INT32[]` of actor numbers and was documented as
  "Voice Listeners". It is now the packed table below, and the role is broader than voice.
- **Details**: A list of network objects you want serialized to you. This is a
  *subscription*, not a volume control: a player with no entry here stops being serialized
  at all, so their voice never arrives regardless of voice distance settings.
- A list of short records, each one a network id plus a state byte. The same list also
  travels compressed, and the two forms are told apart by the very first byte.
- Network ids are not opaque — they decompose into an owner and a local object id, which
  is how a scene object, a player root and a spawned prop are distinguished.

### Code: 12
- **Description**: Player serialization (movement / IK)
- **Data Type**: `BYTE[]`
- The payload carries no actor number — the origin comes purely from the Photon sender.
- Receivers interpolate against the timestamp inside the payload, so that is what decides
  how the motion plays back, not the order packets arrive in.
- Movement has its own channel, separate from everything else.

### Code: 22
- **Description**: Take Ownership
- **Data Type**: `Int32[2]` = `[viewId, actorNumber]`

### Code: 33
- **Description**: Moderation / instance control
- **Data Type**: `Dictionary<byte, object>`, subcode in key `0`

### Code: 40
- **Description**: Avatar Refresh?
- **Data Type**: `int16` 
- **Known keys**:

### Code: 42
- **Description**: User state broadcast
- **Data Type**: `Photon.Client.Hashtable` (string-keyed)
- **Known keys**:

| Key | Type | Meaning |
|---|---|---|
| `inVRMode` | bool | VR / desktop state |

- **Content**:
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
- **Details**: Broadcasts the VR mode state (e.g. whether the player is in VR mode) to
  others. `inVRMode` is also part of the player properties hashtable sent in the
  `JoinGame` operation (op `226`, parameter `249`), so the initial value is established
  before any Code 42 is raised.

### Code: 66
- **Description**: EACHeartBeat (OpRaise Only)
- **Data Type**: `Byte[]`
- **Details**: An array of integers associated with the EAC connection.

### Code: 67
- **Description**: AppId
- **Data Type**: `/shrug`
- **Details**: ""

### Code: 73
- **Description**: Allow Download Private Avatar
- **Data Type**: `String`
- **Contents**
```
file_90cbd832-2e87-4b5a-b7a7-e70390767ffe:11:security
```
- **Details**: `fileId:fileVersion:variant`. Same three fields the incoming Hashtable
  form carries, just flattened.

### Code: 74 OpRaise
- **Description**: Portal, Inventory objects, Stickers, Camera, Print, Drone, Warp effect
- **Data Type**: `Dictionary<byte, Il2CppSystem.Object>`
- **Discriminator**: key `0` = object kind. Complete kind table and per-kind layouts:
  **[Event74.md](Event74.md)**
- ⚠️ Outgoing, keys `0` and `1` are **obfuscated enums**, not `System.Byte` — they only
  come back as plain bytes on `OnEvent`. The underlying numeric values are the same in
  both directions; only the boxed type name differs.
- The view id of a spawned object is derived from the owner's player id, not random.

- **Portal Content**
```
  Key: 0 | Value: Enum (ÏÌÏÎÍÏÏÏÎÎÍÎÏÏÎÎÍÍÎÍÏÍÎ) = Portal
  Key: 132 | Value: deb67zfe (System.String) //instance id
  Key: 1 | Value: Enum (ÌÏÍÌÌÎÍÏÍÎÎÎÎÎÌÍÎÍÏÏÌÏÎ) = Create
  Key: 6 | Value: 30 (System.Int32) //lifetime, seconds
  Key: 7 | Value: False (System.Boolean)
  Key: 2 | Value: usr_8be02542-201d-40ee-8ea2-0df07aaac03b (System.String)
  Key: 8 | Value: Enum (ÎÏÌÍÌÍÏÌÏÍÏÍÍÏÏÏÍÎÍÏÏÏÍ) = All
  Key: 4 | Value: System.Byte[] //position
      Length: 12 | Bytes:
      06-C2-26-42-00-80-ED-BA-4B-98-58-C2
  Key: 5 | Value: System.Byte[] //rotation
      Length: 12 | Bytes:
      00-00-00-00-64-00-AC-42-00-00-00-00
  Key: 134 | Value: (System.String) ""
  Key: 135 | Value: default_portal (System.String)
```

- **Camera Spawn**
```
  Key: 0   | Value: 6 (System.Byte)
  Key: 1   | Value: 0 (System.Byte)
  Key: 4   | Value: System.Byte[12] //position
  Key: 5   | Value: System.Byte[12] //rotation
  Key: 128 | Value: viewId (System.Int32)
  Key: 129 | Value: skin (System.Byte)
  Key: 130 | Value: -1 (System.Int32)
  Key: 133 | Value: 0 (System.Byte)
```
- **Camera Despawn**
```
  Key: 0 | Value: 6 (System.Byte)
  Key: 1 | Value: 1 (System.Byte)
  Key: 2 | Value: camera UID (System.String)
```
- **Camera Sounds** — operation `128` on a live camera
```
  Key: 0   | Value: 6 (System.Byte)
  Key: 1   | Value: 128 (System.Byte)
  Key: 2   | Value: camera UID (System.String)
  Key: 131 | Value: 0 = timer beep, 2 = shutter (System.Byte)
  Key: 132 | Value: serverTime for the timer, 0 for the shutter (System.Int32)
```

- **Spawn Inventory Object** — current form, key `0` = 11, 12 keys total
```
  Key: 0   | Value: 11 (System.Byte) //Action ID
  Key: 1   | Value: 00 (System.Byte)
  Key: 4   | Value: System.Byte[12] //position
  Key: 5   | Value: System.Byte[12] //rotation
  Key: 6   | Value: 0 (System.Int32)
  Key: 7   | Value: True (System.Boolean)
  Key: 8   | Value: 00 (System.Byte)
  Key: 128 | Value: prop_829ba6f6-b837-49d9-b9a9-056b82103b58 (System.String) //prop id
  Key: 129 | Value: 1 (System.Single) //size
  Key: 130 | Value: 6710007 (System.Int32) //ViewID
  Key: 132 | Value: inv_0c262f27-e74e-436a-a96f-b5ebf14a3c19 (System.String) //inventory id
```
  An older form used kind `9` and carried no inventory id.
- **Remove Inventory Object**
```
  Key: 0 | Value: Enum (IIIIIIIIIIIIIIIIIIIIIII) = Prop
  Key: 1 | Value: Enum (IIIIIIIIIIIIIIIIIIIIIII) = Destroy
  Key: 2 | Value: ObjectID (System.String)
```
- **Stickers**
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 0 | Value: 08 (System.Byte) //Action ID
  Key: 1 | Value: 00 (System.Byte)
  Key: 2 | Value: (System.String)
  Key: 4 | Value: System.Byte[] //position
      Length: 12 | Bytes:
      E7-95-46-C1-4F-81-88-41-83-0E-E5-41
  Key: 5 | Value: System.Byte[] //rotation
      Length: 12 | Bytes:
      56-E0-6C-42-1D-0B-DF-3A-66-D8-AF-42
  Key: 8 | Value: 00 (System.Byte)
  Key: 6 | Value: 0 (System.Int32)
  Key: 7 | Value: True (System.Boolean)
  Key: 129 | Value: default_vault_cat (System.String) //special name
  Key: 130 | Value: 0,81397104 (System.Single) //size
  Key: 131 | Value: eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..PjA4pm2Dl6rFBbo2.CRA2gSLSJZA_QyLpPCjBuEeZtRTCsMi7OW3hHH2TwFdC2OyBBWupKrvif8EGHPF0JajNaJoLZ9anS2IMuroQdUUZo0oQeXHQfxqLTpVreyDCvNWXXM0iMglH7FgPXpnAGZtJoHjpTJfcpevdtpR_8quhN6vpOcknPG10xC3Pkcg8wQX-o8c_GXWxja5l3YSBzZUrmbRbVQCvPeznH9JKAo20Vak4fDUQR6vUYcgDeeRQdFeaez4DtrXXpu96k39czxBXgEj4eqk.Koug-C3siOxPQpkBkqEPgg (System.String) //Temporary Token
```
- **Custom Stickers** — identical, `129` holds an `inv_` id instead of a `default_` name
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 0 | Value: 08 (System.Byte)
  Key: 1 | Value: 00 (System.Byte)
  Key: 2 | Value: (System.String)
  Key: 4 | Value: System.Byte[] //position
      Length: 12 | Bytes:
      2D-4C-13-42-60-A8-88-3F-48-62-64-C2
  Key: 5 | Value: System.Byte[] //rotation
      Length: 12 | Bytes:
      5F-14-08-BA-8E-FF-33-43-7F-5F-08-3B
  Key: 8 | Value: 00 (System.Byte)
  Key: 6 | Value: 0 (System.Int32)
  Key: 7 | Value: True (System.Boolean)
  Key: 129 | Value: inv_1286f250-4b0c-4f41-ad56-14b6a8dce06d (System.String) //INV id
  Key: 130 | Value: 0,8752576 (System.Single) //Size
  Key: 131 | Value: eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..z1-ZXyXhM7gguppJ.CobWu4WKJ-Cdn_4Kqbp9NaToh-uZfS19cQI_p6mg3UghAqiK9i7mE55O2Ad77HL2ndnz3sMJlnb1x6jKcQ-tq24FqhvqbVrfbJ_i1pURiRinaB2adUtodJW1xfLhvruzBQDqF4Kf6b64V-tOsAUtU5eZj8maXq6s0Sh98Ez4BaZ5hStIdVa-keCFx-l23veNLzQfxBxqe1ClmMQeVTA79E4BzhwpKzHyIqpsuiWYar7yJPHz0x8GdFbwVGuI7esAyAfZMjfHfMMbMRLDY9azgMy0IFiHXLlUyJ9TE0JlWw.lxRHQbVkw92TpUArsyOMVg (System.String) //Temporary Token
```

- **Warp Effect** — key `0` = 10
```
  Key: 0   | Value: 10 (System.Byte)
  Key: 1   | Value: 0 (System.Byte)
  Key: 2   | Value: "<your usr_ id>_WarpEffect" (System.String)
  Key: 6   | Value: 0 (System.Int32)
  Key: 7   | Value: False (System.Boolean)
  Key: 8   | Value: 0 (System.Byte)
  Key: 128 | Value: inv_... (System.String)
  Key: 129 | Value: 0 = joining, 1 = leaving (System.Byte)
  Key: 130 | Value: JWT (System.String)
```
  The `_WarpEffect` suffix on key `2` is the reliable identifier. The inventory id in
  key `128` is only learned by observing your own packet at join time with the effect
  equipped.

### Code: 76
- **Description**: Default Emoji, Inventory things
- **Data Type**: `Dictionary<byte, object>` — `{0: id, 1: JWT}`
- **Contents**

- **Default emoji**
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 0 | Value: default_cloud (System.String)
  Key: 1 | Value: eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..0ySvZYulwXITQGwo.Hgw7iyJpgf5JpYPKfvvxvrVqcSZRf7P8S6gmbn_LSvl8BhrLvIC4j7avnV55-qv3i38J9YlCRJPftRg6PAn89-zgvOHhdZwxQG0pwEzSmDsD_AChee1Pbsjc4Hic1k1euw0TJhHEixefsFWAkTjkji_qI5ch-UnhS_0qjaVb0eIfXG10NtNhAeOkU0UEzlOSpdklSS7mDdg6F6XZAferkwMbIr8-eJNay-7ZPNJg9b5xbprZjXRxJGQIs2MfXvJHdVDl_Q.WpVdw6_U_ktMh1odwdDj6w (always random generated token) (System.String)
```
- **CustomEmoji / Inventory Emoji**
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 0 | Value: inv_9f02acfd-1041-45c0-bdf9-28d3701365d5 (System.String)
  Key: 1 | Value: eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..PC9cf7i1Vx3QiNW0.4o3IQ4fCAEvkt9VqYvQcUMUYbuG_Is6hm9PZKztXmZL2Jjx-kvnYdzu_tcssis0TmjKp6Tq3qwZzF7z85H-YUA-IsyspgVLRrC_-TBWXbD6HcGQfJYrRO8SaBUoX-wFS434cpstuUvvjWlkxrgZPxyT9U60Qe1nXHDaogrBf_yXiItYOkQACAsV6kHCatk4eDrQg4TvtDE8jzE_q4-X7DeY6uYHRZ4b7tMCWFg_Sjj65Hdt_ue0B7t7EXRBFJ0U-1ija6XHkgzGALTvNoETVRPvdjaBe5ZYLgThGlj1HMw.V2ejWwY3km1qKJlsXWzW7Q (System.String) //Temporary Token
```
- **Details**: Dictionary mapping byte keys to emoji-related data. The token is
  short-lived and re-issued per use — it is fetched from the API, not reusable from a
  previous capture.

### Code: 202
- **Description**: Player Instantiate
- **Data Type**: `Hashtable`

---

