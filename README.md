# Bly Corne firmware

Firmware for a 36-key Corne with nice!nano v2 controllers and nice!view displays.
ZMK and its build workflow are pinned to commit
`5b51501fead672c41b5cfb396f3dafe0894bf4e9`, the September 26, 2026 upstream revision.

## Download and flash

Open the latest successful build on the
[main branch](https://github.com/brandonnly/zmk-bly-36/actions?query=branch%3Amain)
and download its `firmware` artifact. Extract the archive, then flash
`corne_left.uf2` to the left controller and `corne_right.uf2` to the right
controller. Double-tap each controller's reset button to enter its UF2
bootloader, then copy the matching file to its USB drive.

For the five-column migration, first unlock your current firmware in Studio
with CONFIG + A and choose **Restore Stock Settings**. This clears saved
Studio overrides that use the old position indexes. Then flash both halves.
The new firmware uses the 36-key, five-column layout by default.

If upgrading from firmware without mouse support, refresh each computer's
Bluetooth pairing after flashing because the HID descriptor changes: forget the
keyboard on the computer, select that Bluetooth profile on the keyboard, clear
it with CONFIG + H, and pair again. This does not require a settings-reset
firmware. See ZMK's
[HID descriptor refresh instructions](https://zmk.dev/docs/features/bluetooth#refreshing-the-hid-descriptor).

## Existing keys

The MAC, WIN, GAME, NUM, ARROW, and CONFIG layers retain their original order
and all previously assigned keys. The home-row modifier behavior keeps its
200 ms tapping term, 200 ms quick-tap, tap-preferred flavor, and 125 ms prior-idle
requirement. Existing combos, the fullscreen macro, and the NUM + ARROW
conditional CONFIG layer keep their behavior. All seven layers now use 36
positions, with the unused outer columns removed. Both launcher combos use
positions 32 and 33, the Space and Enter thumbs. The optional positional
home-row behaviors also use the new indexes.

## New controls

Hold both layer thumb keys to enter CONFIG. Key names below refer to their
positions on the MAC layer.

- A unlocks ZMK Studio.
- S toggles the new MOUSE layer.
- D locks or unlocks NUM.
- J locks or unlocks ARROW.

These four CONFIG positions were previously unassigned. To unlock a latched
NUM or ARROW layer, hold both layer thumb keys again and tap its CONFIG key.

In MOUSE, H/J/K/L move the pointer left/down/up/right. D/F/G click the
left/right/middle buttons. W/E scroll up/down, U/I scroll left/right, and Q/R
send mouse back/forward. Press S or the Escape thumb key to leave MOUSE.
The other five thumb keys retain their underlying bindings.

## ZMK Studio

Connect the left half over USB and open [ZMK Studio](https://zmk.studio/) in
Chrome or Edge. The native macOS, Windows, and Linux applications also support
Bluetooth. Unlock with CONFIG + A. If USB and Bluetooth are both connected,
select the output matching the Studio connection with the existing CONFIG + G
output toggle.

Studio exposes the original `HOMEROW_MODS` behavior and two optional behaviors,
`POSITIONAL_HOMEROW_LEFT` and `POSITIONAL_HOMEROW_RIGHT`. The positional versions
use balanced hold-tap resolution, opposite-hand and thumb triggers, and
`hold-trigger-on-release`. They keep the original timing values. No existing
key uses them; assign them in Studio only if you want to try different home-row
resolution. Choose the original modifier and tap key when assigning a behavior.

Studio starts with the existing six layers plus MOUSE and offers only the
five-column Corne physical layout. This keeps Studio, combos, and positional
behaviors on the same position indexes.
Once Studio saves a keymap, later source keymap changes take effect only after
**Restore Stock Settings**, which replaces the saved Studio map with the map
in the firmware. Timings, combos, macros, and conditional layers still require
source edits. See the [Studio documentation](https://zmk.dev/docs/features/studio).
