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

## RCE Exploit Fix(es)
The stock decoder had no destination-size parameter, so malicious compressed input could make it continue writing beyond the receiving buffer.
The patch redirects both network callers to a bounded decoder placed at 0x006ED380:
- Host path limit: 0x800 bytes.
- Client path limit: 0x10000 bytes.
- Capacity is checked before every decoded-byte write.
- Input reads stay within the declared compressed data.
- Invalid Huffman nodes, excessive tree depth, or oversized output return 0, causing the message to be discarded.
- Both client-to-host and host-to-client attack directions are covered.
Valid packets retain the normal decoding behavior. The original unsafe decoder is no longer called from either network path.


Enjoy!
