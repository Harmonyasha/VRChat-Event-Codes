# Disclaimer

All of this came out of watching my own traffic. Some entries are confirmed, some are a
best guess, and a few amount to "this keeps showing up and I don't know why" — those say
so. Nothing here is official, and none of it is guaranteed to survive the next client
update. Done out of curiosity, shared as is.

# Disclaimer 2
Was updated with Calude AI to fix my english and improve some information about events

## Index

| File | Contents |
|---|---|
| [Both.md](Both.md) | Codes seen in both directions — the bulk of the reference |
| [OnEvent.md](OnEvent.md) | Incoming-only codes |
| [OpRaise.md](OpRaise.md) | Outgoing-only codes |
| [Operations.md](Operations.md) | Login, join and leave — the operations the events ride in |
| [Event74.md](Event74.md) | Code 74 in detail — every world object shares this one event |
| [UnSorted.md](UnSorted.md) | Unidentified codes and open questions |

## Code map

| Code | What it is | Where |
|---|---|---|
| `1` | Voice | [Both](Both.md#code-1) |
| `2` | Force disconnect | [OnEvent](OnEvent.md#code-2) |
| `3` | Sync request, like `5` | [OnEvent](OnEvent.md#code-3) |
| `4` | Master sync | [UnSorted](UnSorted.md#code-4) |
| `5` | Ask master for a data sync | [OnEvent](OnEvent.md#code-5) |
| `6` | RPC trigger | [Both](Both.md#code-6) |
| `7` | Unreliable serialization | [Both](Both.md#code-7) |
| `8` | Serialization interest table | [OpRaise](OpRaise.md#code-8) |
| `10` | Udon sync — scene objects | [OnEvent](OnEvent.md#code-10) |
| `11` `18` | Udon sync — player objects, RPCs | [Both](Both.md#code-11) |
| `12` | Player serialization / IK | [Both](Both.md#code-12) |
| `13` | Avatar parameter sync | [Both](Both.md#code-13) |
| `14` | Props | [Both](Both.md#code-14) |
| `16` `17` | Object sync / pickups | [Both](Both.md#code-16) |
| `20` `51` | Join world / loaded | [OnEvent](OnEvent.md#code-20) |
| `22` | Take ownership | [Both](Both.md#code-22) |
| `24` `25` | Instance snapshot — in / out | [OnEvent](OnEvent.md#code-24) · [OnEvent](OnEvent.md#code-25) |
| `33` | Moderation / instance control | [Both](Both.md#code-33) |
| `34` | Rate limit table | [OnEvent](OnEvent.md#code-34) |
| `35` | Ping-ish, no payload | [OnEvent](OnEvent.md#code-35) |
| `40` | Avatar refresh? | [OpRaise](OpRaise.md#code-40) |
| `42` | VR mode / nameplate group | [OpRaise](OpRaise.md#code-42) |
| `43` `44` | ChatBox message / typing | [Both](Both.md#code-43) |
| `53` | Store products | [OnEvent](OnEvent.md#code-53) |
| `60` | PhysBones permissions | [Both](Both.md#code-60) |
| `66` | EAC heartbeat | [OpRaise](OpRaise.md#code-66) |
| `67` | AppId | [OpRaise](OpRaise.md#code-67) |
| `71` | Emoji (legacy) | [UnSorted](UnSorted.md#code-71) |
| `73` | Private avatar token | [OnEvent](OnEvent.md#code-73-onevent) · [OpRaise](OpRaise.md#code-73) |
| `74` | All world objects | **[Event74](Event74.md)** |
| `76` | Emoji | [OpRaise](OpRaise.md#code-76) |
| `202` | Instantiate | [OpRaise](OpRaise.md#code-202) |
| `226` `253` `254` `255` | Photon internals | [OnEvent](OnEvent.md#code-226) |
| `21` `26` `28` | Unidentified | [OnEvent](OnEvent.md#code-21) |

Some of these only make sense as a group:

* **Serialization**: `1, 6, 7, 11, 12, 13, 17, 18, 22` — continuous state sync.
* **Udon sync**: `10, 11, 18`.
* **World objects**: `74`, plus `16`, `17`, `22` for the objects it spawns.

## Operation map

Events travel inside Photon operations, which are numbered separately. Four show up in a
normal session:

| Op | What it is |
|---|---|
| `231` | Login — auth cookie, hwid, region, app version |
| `226` | Join instance — **sent twice**, the second one carries the instance token |
| `253` | Raise event — everything in [OpRaise.md](OpRaise.md) is one of these |
| `254` | Leave |

Captured parameter dumps and the full connect sequence: **[Operations.md](Operations.md)**

## Conventions

* **OnEvent** = incoming, hooked at `IPhotonPeerListener.OnEvent(EventData)`.
* **OpRaise** = outgoing, i.e. `SendOperation(253, ...)` with the event code in parameter
  `244` and the payload in parameter `245`.
* The two number spaces are separate. Event `226` (`AppStats`, incoming) and operation
  `226` (`JoinGame`, outgoing) are unrelated. Same for `253` and `254`.

## A note on what is here

This documents **what** each event is and roughly how it is shaped — not a decoder.
Payload dumps are here because that is what was observed on the wire; exact offsets,
packing and parsing are deliberately left out. If you want them, go look.

Where something is inferred rather than confirmed, it says so.
