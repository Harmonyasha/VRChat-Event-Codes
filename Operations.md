# Operations

Events are the cargo, operations are the truck. `PhotonPeer.SendOperation(opCode,
ParameterDictionary, SendOptions)` takes the op code as its **first argument**, not as a
parameter — so anything that isn't `253` shows up as a bare code plus a bag of numbered
parameters, with no event code anywhere in it.

Almost everything the game sends is op `253` (RaiseEvent) and lives in
[OpRaise.md](OpRaise.md). This file is about the handful that isn't.

> These are Photon **operation** codes. They share the number space with nothing — op
> `226` is JoinGame, event `226` is AppStats, and the two never meet. Incoming events come
> in through a different hook entirely — see [OnEvent.md](OnEvent.md).

## SendOptions

Every operation carries a third argument. It does not change what you send, only how it
gets there:

| Field | What it does |
|---|---|
| `DeliveryMode` | Unreliable / Reliable / UnreliableUnsequenced / ReliableUnsequenced |
| `Encrypt` | encrypts the payload |
| `Channel` | which Photon channel — each keeps its own order |

Not everything travels the same way. Voice, movement and the moderation events each use
their own channel and delivery mode. The game is consistent about it, so anything sent on
the wrong one is easy to spot.

> `Reliability` is not its own setting — it just reflects `DeliveryMode`. Set it to
> `false` and `UnreliableUnsequenced` (2) becomes `Unreliable` (0). The packet still
> sends, it just no longer matches what the real client emits.

## The connect sequence

One join, in the order it goes out:

```
op 231    7 params     login
```

```
op 226    2 params     "let me in" — location string, nothing else
          (HTTPS)       the client goes and fetches the instance JWE token
op 226    8 params     the actual join — same location, plus the token
```

and leaving:

```
op 254    no params    leave
op 226    2 params     next instance — the pair starts over
```

The double `226` is what trips people up when reading a log: **one join produces two of
them**, a couple of hundred milliseconds apart. The first carries only the location, no
credentials. Between the two the client makes an HTTP call and comes back with a JWE
token, which the second one carries in its room properties. Treat the first `226` as
"joined" and your world history comes out twice as long as it should be.

Op `231` is sent once per session, before any of this. Reconnecting to another world does
**not** repeat it — 254, then 226, 226, and that is the whole switch.

## op 231 — login

Photon calls it `AuthenticateOnce`. It goes out first, once per session, and never again.

| Param | Value seen | What it is |
|---|---|---|
| `193` | `13` | unidentified, constant across sessions |
| `195` | `0` | unidentified, constant across sessions |
| `210` | `eu` | region |
| `216` | see below | auth parameters, as a query string |
| `217` | `0` | auth type — `0` is Photon's "custom auth" |
| `220` | `Release_1626_2.5` | app version — VRChat build, then the Photon client version |
| `224` | `bf0942f7-…` | Photon application id, constant |

Parameter `216` is a plain, unencrypted query string:

```
token=authcookie_<redacted>&user=usr_<redacted>&hwid=<40 hex chars>
&platform=standalonewindows&unityVersion=2022.3.22f2-DWR
```

That is the session cookie, the account id and the machine fingerprint, in the clear,
in one parameter. Redacted here for the obvious reason — a capture of your own op 231 is
a capture of your own credentials, so be careful what you paste into a bug report.

`193` and `195` never changed value in anything I captured, so there was nothing to
correlate them against. Photon's own parameter table puts encryption setup around there,
which is a guess and nothing more.

## op 226 — join instance

Photon calls it `JoinGame`. Two of these go out on every join.

**First one** — two parameters:

| Param | Value | Meaning |
|---|---|---|
| `215` | `1` | join mode |
| `255` | `wrld_ed4a3561-…:97783~hidden(usr_…)~region(eu)` | location string |

The location string is the same one the game shows in its own log: world id, instance
number, access type with its owner in brackets, region.

**Second one** — same `215` and `255`, plus six more:

| Param | Type | Meaning |
|---|---|---|
| `191` | Int32 `35` | unidentified, constant |
| `232` | bool `true` | unidentified |
| `241` | bool `true` | unidentified |
| `248` | byte-keyed dict | room properties |
| `249` | string-keyed Hashtable | player properties |
| `250` | bool `true` | unidentified |

Room properties (`248`) mix Photon's own well-known keys with VRChat's:

```
253 : True            IsOpen        (Photon)
254 : False           IsVisible     (Photon)
250 : Object[]        lobby props   (Photon)
  2 : "eyJhbGciOi…"   JWE token     <- the point of the whole two-step dance
  3 : 2 (Int32)
  4 : Enum "Active"   obfuscated enum, same trick as key 0/1 on event 74
  5 : False
  6 : True
  7 : False
```

The token in key `2` is different on every join — it is minted for that instance and
that session, so a captured one is dead by the time you read the log.

Player properties (`249`) are the string-keyed hashtable the rest of the docs refer to:

```
isInvisible             False
inVRMode                False
showSocialRank          True
showGroupBadgeToOthers  True
groupOnNameplate        grp_…
useImpostorAsFallback   True
avatarEyeHeight         880   (Int32)
avatarHipHeight         0.5976793 (Single)
```

The last two are not always there. On the very first join of a session they are missing
and the hashtable has six entries; on every later join it has eight. The avatar has to
have loaded once before the client knows its own height.

This is also where `inVRMode` is established, before any [event 42](OpRaise.md#code-42)
is ever raised.

## op 253 — raise event

The one everything else rides in. I never saw more than three parameters on it:

| Param | Meaning |
|---|---|
| `244` | event code |
| `245` | payload |
| `252` | target actors — `Int32[]`, omitted for a normal broadcast |

Parameter `245` is optional. A `253` without one is not broken — it is an event that means
something on its own, with no payload. Two showed up:

```
Count: 1
  244 : 34                 sent once, right after the join completes
```

The server replies with incoming [event 34](OnEvent.md#code-34), the rate-limit table.
Nothing else asks for it and nothing else receives it, so it reads as a request and its
answer.

```
Count: 2
  244 : 3
  252 : [1800]             a single actor number
```

Same shape as the [event 5](OnEvent.md#code-5) capture, which is `{244: 5, 252: [58]}` —
an empty event aimed at one specific player instead of broadcast. This one went out
shortly after the join, at an actor who was already in the instance before I got there.

## op 254 — leave

`Leave`. No parameters at all:

```
---- SendOperation ----
Code: 254
Count: 0
```

This is what actually ends your session in an instance. The client sends it, drops the
connection, then starts a new `226` pair for wherever it goes next. A player vanishing
from your side is incoming [event 254](OnEvent.md#code-254) — different number space,
different thing.

## Where this came from

Captured by hooking `PhotonPeer.SendOperation` — the same hook that produces everything in
[OpRaise.md](OpRaise.md), just logging the branch that *isn't* `253`. Ops `231`, `226` and
`254` are the only non-`253` operations that showed up in a normal session.

One thing to know if you read captures made this way: `253` normally gets unpacked into
its event code and payload. So a `253` printed raw, as a code with numbered parameters,
means there was no `245` to unpack. That is not a logging bug, that is the packet.
