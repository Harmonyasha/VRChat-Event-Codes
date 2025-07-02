# OpRaise


### Code: 8
- **Description**: Voice Listeners
- **Data Type**: `BYTE[]`
- **Details**: Array of 32-bit integers indicating which players can hear the voice.





### Code: 42
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


  ### Code: 66
-    **Description**: EACHeartBeat (OpRaise Only)
-    **Data Type**: `Byte[]`
-    **Details**: An array of integers associated with the EAC connection.

  

  ### Code: 73
-    **Description**: Allow Download Private Avatar
-    **Data Type**: `String`
-    **Contents**
```
file_90cbd832-2e87-4b5a-b7a7-e70390767ffe:11:security
```
-    **Details**: Data for allowing download of a private avatar.



  
  ### Code: 74 OpRaise
-    **Description**: Portal,Inventory Things,Stickers
-    **Data Type**: `Dictionary<byte, Il2CppSystem.Object>`
-    -    **Portal Content**
 ```
  Key: 0 | Value: Enum (ÏÌÏÎÍÏÏÏÎÎÍÎÏÏÎÎÍÍÎÍÏÍÎ) = Portal
  Key: 132 | Value: deb67zfe (System.String) //instance id
  Key: 1 | Value: Enum (ÌÏÍÌÌÎÍÏÍÎÎÎÎÎÌÍÎÍÏÏÌÏÎ) = Create
  Key: 6 | Value: 30 (System.Int32) //no idea
  Key: 7 | Value: False (System.Boolean)
  Key: 2 | Value: usr_8be02542-201d-40ee-8ea2-0df07aaac03b (System.String)
  Key: 8 | Value: Enum (ÎÏÌÍÌÍÏÌÏÍÏÍÍÏÏÏÍÎÍÏÏÏÍ) = All
  Key: 4 | Value: System.Byte[] //position
      Length: 12 | Bytes:
      06-C2-26-42-00-80-ED-BA-4B-98-58-C2
  Key: 5 | Value: System.Byte[] //rotation
      Length: 12 | Bytes:
      00-00-00-00-64-00-AC-42-00-00-00-00
```

-    -    **Spawn Inventory Object**
 ```
  Key: 0 | Value: 09 (System.Byte) //Action ID
  Key: 1 | Value: 00 (System.Byte)
  Key: 4 | Value: System.Byte[] //position
      Length: 12 | Bytes:
      29-49-3B-40-00-94-D6-3C-82-7C-A5-41
  Key: 5 | Value: System.Byte[] //rotation
      Length: 12 | Bytes:
      88-9F-B3-43-E3-94-6F-42-10-52-B3-43 
  Key: 8 | Value: 00 (System.Byte)
  Key: 6 | Value: 0 (System.Int32)
  Key: 7 | Value: True (System.Boolean)
  Key: 128 | Value: prop_829ba6f6-b837-49d9-b9a9-056b82103b58 (System.String) //prop id
  Key: 129 | Value: 1 (System.Single) //size
  Key: 130 | Value: 6710007 (System.Int32) //ViewID
```
-    -    **Remove Inventory Object**
```
  Key: 0 | Value: Enum (IIIIIIIIIIIIIIIIIIIIIII) = Prop
  Key: 1 | Value: Enum (IIIIIIIIIIIIIIIIIIIIIII) = Destroy
  Key: 2 | Value: ObjectID (System.String)
```
-    -    **Stickers**
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
-    -    **Custom Stickers**
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

### Code: 76
-    **Description**: Default Emoji,Inventory things
-    **Contents**

-    **Default emoji**
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 0 | Value: default_cloud (System.String)
  Key: 1 | Value: eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..0ySvZYulwXITQGwo.Hgw7iyJpgf5JpYPKfvvxvrVqcSZRf7P8S6gmbn_LSvl8BhrLvIC4j7avnV55-qv3i38J9YlCRJPftRg6PAn89-zgvOHhdZwxQG0pwEzSmDsD_AChee1Pbsjc4Hic1k1euw0TJhHEixefsFWAkTjkji_qI5ch-UnhS_0qjaVb0eIfXG10NtNhAeOkU0UEzlOSpdklSS7mDdg6F6XZAferkwMbIr8-eJNay-7ZPNJg9b5xbprZjXRxJGQIs2MfXvJHdVDl_Q.WpVdw6_U_ktMh1odwdDj6w (always random generated token) (System.String)
```
-    **CustomEmoji / Inventory Emoji**
```
Il2Dictionary<System.Byte, Il2CppSystem.Object>
  Key: 0 | Value: inv_9f02acfd-1041-45c0-bdf9-28d3701365d5 (System.String)
  Key: 1 | Value: eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIn0..PC9cf7i1Vx3QiNW0.4o3IQ4fCAEvkt9VqYvQcUMUYbuG_Is6hm9PZKztXmZL2Jjx-kvnYdzu_tcssis0TmjKp6Tq3qwZzF7z85H-YUA-IsyspgVLRrC_-TBWXbD6HcGQfJYrRO8SaBUoX-wFS434cpstuUvvjWlkxrgZPxyT9U60Qe1nXHDaogrBf_yXiItYOkQACAsV6kHCatk4eDrQg4TvtDE8jzE_q4-X7DeY6uYHRZ4b7tMCWFg_Sjj65Hdt_ue0B7t7EXRBFJ0U-1ija6XHkgzGALTvNoETVRPvdjaBe5ZYLgThGlj1HMw.V2ejWwY3km1qKJlsXWzW7Q (System.String) //Temporary Token
```
-    **Details**: Dictionary mapping byte keys to emoji-related data.

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

