# Multiplayer Patch
> Version 2.3

This default_mp.self is a modified version of TU 1.14.

## How to use
- Navigate to the release folder and select your region.
- Then download either a CFW or HEN version of the binary.
- Place the downloaded default_mp.self in your game update directory.
> /dev_hdd0/game/{region}/usdir/default_mp.self
- That's it!

## Features
- Supports online play
- Native .gsc parsing, linking and compilation
- resolves a bug where PSN accounts created after or changed after 2018 were not syncing stats with the Activision DemonWare server.
- supports mod loader (see below)
- resolves numerous security issues.

## Mod Loader
This is still a work in progress but I wanted a way to automatically load custom user maps (or any fastfile really).

The binary checks it's executing directory for a "mods" folder (all lowercase).

Any user map fastfile/imagefile located in that directory can be loaded from the game engine.

An example setup would look like:
```
/dev_hdd0/game/BLUS30377/usdir/mods/imagefile7.pak
/dev_hdd0/game/BLUS30377/usdir/mods/mp_shipment_xmas.ff
/dev_hdd0/game/BLUS30377/usdir/mods/mp_shipment_xmas_load.ff
```

Then you can load the map with either a modified menuDef asset (see IW4Studio) or by setDvar ui_mapname "mp_shipment_xmas".

## Security fixes — player summary

This release includes several rounds of protection against malicious network traffic and in-game messages.

- **Stronger protection against crashes and unauthorized code execution.** Unsafe compressed messages and malformed party-join requests are rejected before they can corrupt game memory.
- **Safer weapons and effects.** Invalid weapon data, shellshock settings, sound settings, and destructible-object events are handled safely.
- **Safer text and menus.** Oversized messages, translations, vote text, and disconnect notices can no longer overrun the buffers covered by these fixes.
- **More robust network handling.** Added checks for malformed scoreboard entries, invalid relay destinations, oversized queued packets, and invalid message acknowledgments.
- **Protection against forced match disruption.** Non-host players cannot abuse the patched end-match menu actions or force an active host onto the host-migration screen.
- **Compatibility preserved.** Weapon validation follows the current weapon registry, including custom weapons. The migration fix retains the existing path for genuine host handoffs.

CFW and HEN releases are available for all seven supported regions.

## Technical analysis — developers and reverse engineers

Addresses below are **virtual addresses in the supported PS3 TU1.14 `default_mp` build**, not file offsets. They identify patched instructions or call sites unless explicitly labelled otherwise.

| Function or subsystem | PS3 location(s) | Security flaw and mitigation |
| --- | --- | --- |
| Compressed-message decoder — `MSG_ReadBitsCompress` | `0x000B33C0`, `0x00215F28` | Unbounded decompression could overwrite the destination. Both inbound network paths use a bounded decoder, with server/client output limits of `0x800` and `0x10000` bytes, bounded input reads, and invalid decoding-state rejection. |
| `PartyHost_HandleJoinPartyRequest` | `0x000D6044`; callback descriptor `0x006FE5B0` | Missing count and request-structure validation could permit writes outside party storage. A count guard and callback wrapper validate the request before the original handler processes it. |
| `NET_DeferPacketToClient` | `0x0021F1A4` | Invalid message lengths could overflow a deferred packet slot. Reject invalid or oversized lengths before copying. |
| Connectionless relay handler | `0x000AEA00` | A relay destination was used before validating its member index. Reject invalid destinations before lookup or forwarding. |
| `CG_ParseScores` | `0x00078010` | Unchecked scoreboard client indexes could access outside the client table. Normalize invalid indexes to client zero before the affected access. |
| `SV_PacketEvent` | `0x0021ED9C` | A signed reliable-acknowledgment comparison could mishandle invalid sequence deltas. Use an unsigned comparison so invalid values follow the existing recovery path. |
| `Cmd_MenuResponse_f` | `0x001798F0` | Match-ending menu responses lacked host authorization. Require the sender’s engine-maintained `localClient` flag before forwarding those responses to script notification. |
| `BG_GetWeaponDef` | `0x00032898` | Unchecked weapon indexes could return invalid definition pointers. Validate against the live `bg_lastParsedWeaponIndex`; out-of-range values resolve to the canonical weapon-zero entry. The highest valid index remains accepted. |
| `BG_GetShellshockParms` | `0x0001BA28` | An unchecked index could read outside the shellshock table. Accept indexes `0–15`; otherwise use entry zero. |
| `SND_DeactivateEnvironmentEffects` / `SND_SetEnvironmentEffects` | `0x003376B0`, `0x00337830` | Invalid priorities could drive out-of-bounds sound-state writes. Accept priorities `1–4`; reject other values without modifying state. |
| `SND_DeactivateChannelVolumes` / `SND_SetChannelVolumes` | `0x003379E0`, `0x00338080` | Invalid priorities could drive out-of-bounds channel-state writes. Accept priorities `1–3`; reject other values without modifying state. |
| `DynEntCl_DestroyEvent` | `0x0012B730` | Unchecked draw types and entity IDs could produce invalid writes. Validate the type and ID against the current map’s loaded dynamic-entity counts; ignore invalid events. |
| `UI_ReplaceDirective` | `0x00247EE4` | An unbounded directive copy could overflow its local buffer. Bound the copy, preserve string termination, and discard excess directive characters. |
| `UI_ReplaceConversions` | `0x00236AC0`, `0x00236B6C` | Raw and substituted text could exceed the caller’s output capacity. Bound both output paths while reserving termination space. Restore the live CR7 comparison to avoid the earlier first-character truncation regression. |
| `CG_UpdateVoteString` | `0x0007A594`, `0x0007A5C0` | Both map-name and raw vote-text copies could overflow a 256-byte stack buffer. Bound both paths and preserve the terminating NUL. |
| `SEH_LocalizeTextMessage` | `0x00229858`, `0x002298DC` | Raw tokens and expanded localization output could exceed fixed buffers. Limit raw tokens to 1,023 bytes and exit through the existing safe finish when expanded output is exhausted. |
| `CL_DisconnectError` → `Com_Error` | `0x000A9E8C` | Remote disconnect text was used as a format string. Supply a fixed `%s` format and pass the message as data. |
| Client migration — `HandleStartMsg` | `0x000B24F0`; guard `0x006EDB40` | The handler could start a migration-screen transition while the local server was still hosting normally. Reject the transition when `sv_running` is enabled and server migration is inactive, before side effects occur. Non-host clients and already-active server migrations retain the original path. |


Enjoy!
