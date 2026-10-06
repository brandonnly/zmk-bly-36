# ZMK update research

Checked October 6, 2026 against official ZMK documentation, source, pull requests, and GitHub build metadata.

This is a research snapshot of config commit `75e53da`, before the Studio
migration. See [the README](../README.md) for the current firmware and controls.

## Build baseline

The last successful GitHub build was June 5, 2026, at config commit `75e53da`. It ran from 23:34 to 23:40 UTC. GitHub no longer provides its logs or firmware artifacts, so the exact upstream ZMK commit used in that build cannot be confirmed. The latest upstream `main` commit before the build was `773dec58eaacaef4703b3e4595e50bd71f6cad3d`, dated June 3. That is an inferred baseline, not a recovered build manifest. A successful build also does not establish when the keyboard was flashed. [Build run](https://github.com/brandonnly/zmk-bly-36/actions/runs/27045652478), [inferred upstream baseline](https://github.com/zmkfirmware/zmk/commit/773dec58eaacaef4703b3e4595e50bd71f6cad3d).

This config follows `revision: main`, builds both Corne halves with `nice_nano//zmk` and nice!view, and already uses home-row mods with `require-prior-idle-ms = <125>`. [Manifest](../config/west.yml), [build matrix](../build.yaml), [keymap](../config/corne.keymap).

## Changes since June 5

The checked upstream head is `5b51501fead672c41b5cfb396f3dafe0894bf4e9`, dated September 26, 2026. Comparing it with the inferred build baseline shows mostly documentation and dependency maintenance, plus these firmware fixes. There is no broad new Corne feature in this interval. [Upstream comparison](https://github.com/zmkfirmware/zmk/compare/773dec58eaacaef4703b3e4595e50bd71f6cad3d...5b51501fead672c41b5cfb396f3dafe0894bf4e9).

- September 26: avoid a split peripheral input-report queue deadlock. The reported trigger was a peripheral trackball while holding a key, so its benefit to this Corne without a pointer is limited. [PR #3110](https://github.com/zmkfirmware/zmk/pull/3110).
- September 15: correct HID keycode validation and avoid C keycode macro conflicts. [PR #3503](https://github.com/zmkfirmware/zmk/pull/3503), [PR #3498](https://github.com/zmkfirmware/zmk/pull/3498).
- July 20: fix Studio save/reset handling for large key position indexes. This is a position within one layer, not the total number of bindings across all layers. The six 42-position layers here do not trigger that issue. [PR #3431](https://github.com/zmkfirmware/zmk/pull/3431), [settings implementation](https://github.com/zmkfirmware/zmk/blob/5b51501fead672c41b5cfb396f3dafe0894bf4e9/app/src/keymap.c#L870).
- June 29: prevent a null dereference when debug logging an unassigned sensor binding. This config has no sensor bindings. [PR #3406](https://github.com/zmkfirmware/zmk/pull/3406), [keymap](../config/corne.keymap).

The latest numbered firmware release is still `v0.3.0`, published August 1, 2025. `main` moved to Zephyr 4.1 in December 2025, so a June 2026 build following `main` already includes that migration. The current `nice_nano//zmk` target is the new spelling of `nice_nano_v2`. Do not change this config to `v0.3` without accounting for the older board naming. [Latest release](https://github.com/zmkfirmware/zmk/releases/tag/v0.3.0), [Zephyr migration](https://zmk.dev/blog/2025/12/09/zephyr-4-1).

## Studio status and fit

Studio reached general availability on November 11, 2024. Corne was on the supported keyboard list at launch. Updating to newer ZMK does not automatically enable Studio; it needs an explicit firmware build option and an unlock binding. [Studio announcement](https://zmk.dev/blog/2024/11/11/zmk-studio-mvp-ga).

This repository does not enable `CONFIG_ZMK_STUDIO`, use the `studio-rpc-usb-uart` snippet, or define `&studio_unlock`. A firmware built from this config is therefore not Studio enabled. [Build matrix](../build.yaml), [configuration](../config/corne.conf), [keymap](../config/corne.keymap).

Studio can remap keys and assign existing custom behaviors without reflashing. It can rename layers and use layers reserved at build time. The browser app supports USB; native macOS, Windows, and Linux apps also support BLE. It cannot yet tune hold-tap properties, edit macro definitions, combos, or conditional layers, or create new behaviors. The current `HOMEROW_MODS` and `MACOS_FULLSCREEN` definitions could be assigned to keys, but their definitions would stay in the source. Enabling Studio requires `CONFIG_ZMK_STUDIO=y`, the USB snippet on the left/central build, and an unlock binding. Once Studio saves a keymap, later `.keymap` edits require Studio's Restore Stock Settings to take effect. [Current Studio documentation](https://zmk.dev/docs/features/studio).

The stock Corne definition defaults to its 42-position, six-column physical layout and also provides a 36-position, five-column layout. This config retains all 42 logical positions with transparent outer columns. Studio will therefore start with 42 slots. Selecting five columns deserves a deliberate keymap and combo-position migration. [Corne definition](https://github.com/zmkfirmware/zmk/blob/5b51501fead672c41b5cfb396f3dafe0894bf4e9/app/boards/shields/corne/corne.dtsi), [keymap](../config/corne.keymap).

## Useful options already available before June

- Mouse keys can move the pointer, click, and scroll without new hardware. Movement and scroll support merged December 10, 2024. This config has no pointing option or mouse bindings. Adding `CONFIG_ZMK_POINTING=y` and a small mouse layer would be a useful experiment; Bluetooth hosts need re-pairing after the HID descriptor changes. [Merge](https://github.com/zmkfirmware/zmk/commit/6b40bfda5357), [mouse behaviors](https://zmk.dev/docs/keymaps/behaviors/mouse-emulation), [pointing setup](https://zmk.dev/docs/features/pointing).
- Positional home-row mods with separate left/right behaviors, `balanced` flavor, and `hold-trigger-on-release` can avoid same-hand typing rolls becoming modifiers while allowing opposite-hand shortcuts to resolve sooner. These are existing tuning options, not a feature introduced since June. The current config already has the prior-idle safeguard but lacks positional triggers. [Hold-tap documentation](https://zmk.dev/docs/keymaps/behaviors/hold-tap#custom-hold-tap-examples), [current behavior](../config/corne.keymap).
- Layer locking merged November 14, 2025. Hold `&mo 3`, tap `&tog 3`, and then release the thumb key: the NUM layer remains active. Tap `&tog 3` again to leave it. This uses the existing toggle behavior, not a new `&layer_lock` binding. It could help long number entry or navigation sessions. [PR #2717](https://github.com/zmkfirmware/zmk/pull/2717), [layer-lock documentation](https://zmk.dev/docs/keymaps/behaviors/layers#layer-locking).
- nice!view gained per-profile paired, connected, and selected indicators in July 2025. This should already be included automatically in the June build with the stock nice!view widget. [PR #2265](https://github.com/zmkfirmware/zmk/pull/2265).
- A dedicated USB dongle can take the central role and improve the left half's battery life. It requires another BLE-capable board and makes both halves dependent on the dongle. This is an optional hardware/configuration project, not a new post-June firmware feature. [Official dongle guide](https://zmk.dev/docs/hardware-integration/dongle).

Full-duplex wired split exists, but a normal Corne uses a single data GPIO and needs the still-planned half-duplex transport. It is not a drop-in TRRS upgrade for this keyboard. [Split transport documentation](https://zmk.dev/docs/features/split-keyboards#full-duplex-wired-uart).

Studio is the most relevant upgrade if remapping keys often. Mouse keys and layer locking are small useful additions. Home-row tuning is worth trying if current shortcuts feel slow or produce mistakes. Merely rebuilding the current config brings maintenance fixes but does not enable these optional capabilities.
