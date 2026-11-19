# Changelog

## Changes in 2.6.5

- **Stock weapons** is now the default. It uses the original player stats format so patched and unmodified players can pass the stat checksum check when either player hosts.
- To enable custom weapons, open **PATCHES** from the multiplayer main menu, select **Custom weapons**, then choose **Apply**. Multiplayer restarts. Select **Stock weapons** and apply to switch back.
- When switching online weapon modes, the game saves each custom class weapon and its stock replacement locally for that account. Rank and XP use the normal online profile. Offline classes saved with custom weapons require Custom weapons mode.

## Changes in 2.6.4

- Added support for hotswapping patches

## Changes in 2.6.3

- Simplified the Content restart message.
- Hide the Author field until a package is selected.
- Fixed scrolling HUD text animations while retaining unlocalized text overflow protection.
- Removed the obsolete custom_ui_mp fastfile lookup; the Content and Custom Maps menus are embedded.
- Install or update multiple Content packages before restarting. Press Start in Content after an installation to restart; the list scrollbar appears only when needed.
- Added the interactive debug console and background console logging. Press L3 + R3 to toggle it, Cross to edit using the PS3 system keyboard, and Start to run the command. Hold L1 and use D-pad Up/Down to scroll output; tap L1/R1 to recall entered commands. Command text accepts up to 255 printable ASCII characters.

## Changes in 2.6.2

- Updated content UI + added logging on failed downloads

## Changes in 2.6.1

- Added support for custom weapons in packages
- Matches the Primary and Secondary Custom weapon selectors to the active menu style.
- Restores stock server-history allocation and 32 online history slots.

## Changes in 2.6.0

- Embeds the Content and Custom Maps menus in the executable, removing their dependency on a matching `patch_mp.ff`.
- Adds a NEW indicator next to Content when the catalog contains uninstalled packages.
- Shows the selected package's PNG/JPEG thumbnail in the details pane. Previews are capped at 256×144, loaded on demand, and released when selection changes or Content closes.
- Improves Content descriptions with smaller text and wrapping for long slash-separated text.
- Returns another 1 MiB from the main PMem reservation to system memory to accommodate the single 1 MiB preview workspace.

## Changes in 2.5.8

- added support for custom map ui fastfiles
- allows specifying of custom factions on maps

## Changes in 2.5.7

- Allows stock map weapons, including the mounted minigun, to use their stock ammo and clip groups on custom maps by reserving those groups separately from custom weapons. Custom maps do not need a map-name whitelist.

## Changes in 2.5.6

- Adds 9.5 MiB of free main PMem headroom through a guarded reduction in server-history allocation, measured on Estate.
- Increases `loaded_sound` capacity from 1,350 to 2,048 with enlarged backing storage, adding 64 KiB to the executable's mapped-memory budget.

## Changes in 2.5.5

- Reuses archive workspace for script parsing, removing a separate 1 MiB allocation from main PMem.
- Requests 1 MiB less system memory for the main PMem arena at startup.
- Fixed unlocalized HUD and `setText` lifecycle handling across Fast Restart, including missing Obsidian menu text. Existing text overflow protections remain in place.
- Added targeted guards for malformed inline-image text that caused known freezes.
- A host using custom GSC now ends the match instead of sending incompatible script state during outgoing host migration. Stock GSC migration follows its normal path.

## Changes in 2.5.4

- Reduced the executable's permanent writable-memory reservation by 64 KiB.
- Allocates Content's catalog and download workspace only when Content is opened.
- Releases that workspace when Content closes or before the automatic restart after installing a package.
- Use the matching 2.5.4 `patch_mp.ff` so closing Content releases its workspace.

## Changes in 2.5.3

- Added 3 MiB of free main PMem headroom, measured on Estate.
- Added garbage collection for unlocalized HUD text.
- Updated the Content catalog and downloads to cdn.iw4studio.org.
