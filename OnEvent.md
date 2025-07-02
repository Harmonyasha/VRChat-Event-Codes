# OnEvent


### Code: 8
- **sender**: 0
- **Description**: Is Someone hear you?
- **Data Type**: `bool`
- **Details**: true/false


### Code: 10
- **Description**: Udon (Unknown Purpose)
- **Data Type**: `BYTE[]`
- **Details**: Udon-related data, purpose unclear, stored as an array of bytes.

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
idk
### Code: 28
idk

### Code: 51
-    **Description**: OnLoaded
-   **Sender**:`0`
-    **Data Type**: `Object[]`
   - **Values**:
```
129(Int32) -your photonid?
True(Boolean)
```

### Code: 53
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

### Code: 74 OnEvent
-    **Description**: Portal,Pedestrial,Sticker,Print,Vrc+ gift drop,Drone Spawn
-    **Sender**: `0`
-    **Portal Content**
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
