# 
# Code 74 — World Object Multiplexer

Code 74 is not one event. It is a single `Dictionary<System.Byte, System.Object>` that
carries every spawnable world object: portals, avatar pedestals, prints, stickers, drones,
the camera, inventory props, VRC+ gift drops and warp effects. Which one it is depends
entirely on **key 0**.

## Sender is 0

On the `OnEvent` side these arrive server-relayed, so `EventData.Sender` is `0` and cannot
be trusted or used to attribute the object. The real owner is in the payload:

| Object kind | Owner is in |
|---|---|
| Portal | key `2` |
| Everything else | key `3` |

Both hold a `usr_...` id, so resolving it means matching against the loaded player list by
UserId, not by actor number. Anything that attributes Code 74 to a player by `Sender`
attributes all of it to whoever happens to be actor 0.

Also worth noting: on the outgoing (`OpRaise`) side keys `0` and `1` go out as **obfuscated
enums**, not `System.Byte` — they come *back* on `OnEvent` as plain bytes. The numeric
values are identical in both directions; only the boxed type name differs.

## Common key layout

| Key | Type | Meaning |
|---|---|---|
| `0` | Byte / enum | **Object kind** — the discriminator, see table below |
| `1` | Byte / enum | Operation: `0` = create, `1` = destroy, `128` = event on an existing object |
| `2` | String | Object UID — or the owner `usr_...` for portals |
| `3` | String | Owner `usr_...` (incoming only; the server fills this in) |
| `4` | Byte[12] | Position, 3 × float |
| `5` | Byte[12] | Rotation, 3 × float (euler) |
| `6` | Int32 | Lifetime in seconds (`30` for portals) |
| `7` | Boolean | |
| `8` | Byte | |
| `9` | Boolean | |
| `11` | | present on incoming spawns |
| `128+` | | kind-specific payload |

## Object kinds (key 0)

| Id | Kind | Notes |
|---|---|---|
| `0` | Portal | |
| `1` | Avatar pedestal | key `2` = `file_...` |
| `2` | VRC+ gift drop | older sticker form used this id too |
| `4` | Print | key `128` = `prnt_...` |
| `5` | Drone — body | |
| `6` | Drone controller / **Camera** | two different things share this id |
| `8` | Sticker | current form |
| `9` | Inventory prop | older form |
| `10` | Warp effect | |
| `11` | Inventory prop | current form |

## ViewId

Every spawned object carries a view id. It is not random — it is built from the owning
player's id plus an index for the object, which is the same scheme network ids follow
everywhere else in the protocol. Inventory props get a limited range of indices.

---

## Camera (kind 6)

**Spawn** — `RaiseEvent(74, ..., Reliable)`:

```
0   : (Byte)   6
1   : (Byte)   0
4   : (Byte[12]) position
5   : (Byte[12]) rotation
128 : (Int32)  viewId
129 : (Byte)   skin
130 : (Int32)  -1
133 : (Byte)   0
```

**Despawn** — just the kind, the operation and the object id:

```
0 : (Byte)   6
1 : (Byte)   1
2 : (String) camera UID
```

**Camera sounds** — operation `128` on an existing camera:

```
0   : (Byte)   6
1   : (Byte)   128
2   : (String) camera UID
131 : (Byte)   0 = timer beep, 2 = shutter
132 : (Int32)  serverTime (0 for the shutter)
```

Incoming camera spawns look the same with the owner id added. A camera **dolly** carries
one extra key on top of that.

---

## Inventory prop (kind 11)

```
0   : (Byte)     11
1   : (Byte)     0
4   : (Byte[12]) position
5   : (Byte[12]) rotation
6   : (Int32)    0
7   : (Boolean)  true
8   : (Byte)     0
128 : (String)   prop_829ba6f6-b837-49d9-b9a9-056b82103b58
129 : (Single)   size
130 : (Int32)    viewId
132 : (String)   inv_0c262f27-e74e-436a-a96f-b5ebf14a3c19
```

The incoming form carries a few extra keys the sender does not fill in.

Key `129` is a plain float scale.

---

## Sticker (kind 8)

```
0   : (Byte)     8
1   : (Byte)     0
2   : (String)   usr_... (owner)
4   : (Byte[12]) position
5   : (Byte[12]) rotation
6   : (Int32)    0
7   : (Boolean)  true
8   : (Byte)     0
129 : (String)   default_vault_cat  |  inv_1286f250-...   (built-in vs. custom)
130 : (Single)   size
131 : (String)   JWT (short-lived, re-issued per spawn)
```

Key `130` is a plain float scale.

---

## Portal (kind 0)

Outgoing:

```
0   : (enum)     Portal   -> 0
1   : (enum)     Create   -> 0
2   : (String)   usr_... (owner)
4   : (Byte[12]) position
5   : (Byte[12]) rotation
6   : (Int32)    30        lifetime, seconds
7   : (Boolean)  false
8   : (enum)     All       target audience
132 : (String)   instance id, e.g. deb67zfe
134 : (String)   ""
135 : (String)   default_portal
```

The incoming form adds the owner id and, in key `133`, the full world metadata (id, name,
capacity, imageUrl, authorId, authorName, unityPackages, tags — see
[OnEvent.md](OnEvent.md#code-74-onevent)).


---

## Print (kind 4)

```
0   : (Byte)     4
1   : (Byte)     128
2   : (String)   object UID
3   : (String)   usr_... owner
4   : (Byte[12]) position
5   : (Byte[12]) rotation
9   : (Boolean)  false
128 : (String)   prnt_...
129 : (Int32)    viewId
130 : (String)   "" Should be text on print
131 : (Single)   0.75      scale
132 : (Byte[12]) position Spawn Animation Position
133 : (Byte[12]) rotation Spawn Animation Position
```

A print is recognisable by its `prnt_` id in key `128`.

---

## Warp effect (kind 10)

```
0   : (Byte)   10
1   : (Byte)   0
2   : (String) "<your usr_ id>_WarpEffect"
6   : (Int32)  0
7   : (Boolean) false
8   : (Byte)   0
128 : (String) inv_...   inventory item id
129 : (Byte)   0 = joining, 1 = leaving
130 : (String) JWT
```

The `_WarpEffect` suffix on key `2` is what marks it. The inventory id in key `128` only
shows up if the effect is actually equipped when you join.

---

## Drone (kinds 5 + 6)

A drone is two objects sent as two packets.

**Part 1 — body (kind 5)**

```
1   : (Byte)   0
0   : (Byte)   5
3   : (String) usr_...
2   : (String) 1645d211-69d2-4385-936d-484e0a50ff08   object UID
4   : (Byte[12]) position
5   : (Byte[12]) rotation
9   : (Boolean) false
128 : (Int32)  77110005   viewId
129 : (Byte[12]) 1,1,1  color 1
130 : (Byte[12]) 1,1,1 color 2
131 : (Byte[12]) 1,1,1 color 3
```

**Part 2 — controller (kind 6)**

```
1   : (Byte)   0
0   : (Byte)   6
3   : (String) usr_...
2   : (String) 2ba9bd25-96d0-4d26-86b6-b5025fa16382 object UID
4   : (Byte[12]) position
5   : (Byte[12]) rotation
9   : (Boolean) false
128 : (Int32)  77110006   viewId
129 : (Byte)   2
130 : (Int32)  77110005   viewId of part 1
133 : (Byte)   0
```

**Despawn** is the same pair with `1 : (Byte) 1` and only keys `0, 1, 2, 3`.

Note the id collision: kind `6` is both the drone controller and the camera. Key `130` is
what separates them.

---

## Pedestal (kind 1)

```
1   : (Byte)   0
0   : (Byte)   1
3   : (String) usr_...
2   : (String) file_ec3543e4-6c6c-4470-b5a9-2b60dae1098a
4   : (Byte[12]) position
5   : (Byte[12]) rotation
9   : (Boolean) false
8   : (Byte)   0
128 : (Byte)   0
130 : (String[]) []
```

## VRC+ gift drop (kind 2)

```
0   : 0
1   : 2
2   : usr_...
3   : usr_...
4   : position
5   : rotation
128 : "Dynamic_3"
129 : 0
130 : 42
131 : 30
```

Note keys `0` and `1` are swapped relative to the other kinds in this capture — `0` is the
operation and `1` is the kind. Unverified whether that is a real difference or a capture
artefact.

Also winner of vrc+ gift drop already in list (OPRAISE) so you cannot take it when someone already drop it
