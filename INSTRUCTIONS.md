# Eyelash Corne Miryoku ZMK Instructions

This file explains exactly how to change keys, change settings, build firmware, and install it on this keyboard.

## 1. Know which files matter

Edit these files for normal customization:

- `config/eyelash_corne.keymap`: keys, layers, hold-tap behaviors, Bluetooth/output actions, encoder behavior.
- `config/eyelash_corne.conf`: firmware settings such as sleep, RGB, pointing, backlight, and Bluetooth radio options.
- `build.yaml`: which firmware images GitHub builds.

Usually do not edit these files for normal keymap changes:

- `boards/arm/eyelash_corne/eyelash_corne.keymap`: board default example, not the active user keymap for this repo.
- `config/west.yml`: module source configuration. Change this only if the upstream board/module location changes.
- `config/eyelash_corne.json`: key-position metadata for visual tools. Normal key changes do not require edits here.

## 2. Understand what this repo builds

This repo builds three UF2 files:

1. Left half firmware.
2. Right half firmware.
3. A `settings_reset` firmware.

That comes from `build.yaml`.

The left half is the central half for this keyboard. Flash and test the left half first.

## 3. Change keys

### 3.1 Open the keymap

Open `config/eyelash_corne.keymap`.

This file is the active layout.

### 3.2 Understand the layer names

At the top of the file, these layer numbers are defined:

- `BASE 0`
- `NAV 1`
- `MOUSE 2`
- `MEDIA 3`
- `NUM 4`
- `SYM 5`
- `FUN 6`

Each layer appears later in the `keymap` block as:

- `base_layer`
- `nav_layer`
- `mouse_layer`
- `media_layer`
- `num_layer`
- `sym_layer`
- `fun_layer`

### 3.3 Understand the binding syntax used here

Use these patterns exactly as they already appear in the file:

- `&kp X`: press key `X`.
- `&hm LGUI A`: tap `A`, hold `Left GUI`.
- `&lt NAV RET`: tap `Enter`, hold for `NAV` layer.
- `&trans`: transparent; fall through to a lower layer.
- `sensor-bindings = <...>;`: encoder rotation behavior for that layer.

The custom `&hm` behavior is defined near the top of the file. It is the homerow-mod hold-tap behavior used on the base layer.

### 3.4 Change a normal key

Find the key in the correct layer and replace only that binding.

Example:

- Change `&kp Q` to `&kp ESC` if you want that position to send `Escape`.
- Change `&kp COMMA` to `&kp SEMI` if you want `;` there instead.

Do not remove:

- the leading `&`
- the surrounding `< >`
- the trailing `;` at the end of each bindings block

### 3.5 Change a hold-tap or layer-tap key

Examples from this repo:

- `&hm LCTL D`: tap `D`, hold `Left Control`
- `&lt NUM BSPC`: tap `Backspace`, hold for the `NUM` layer

To change one:

1. Keep the behavior name the same.
2. Change only the arguments.
3. Keep the argument order the same.

Examples:

- Change `&hm LCTL D` to `&hm LALT D` to make hold send `Left Alt` instead of `Left Control`.
- Change `&lt NUM BSPC` to `&lt NUM SPACE` to make tap send `Space` instead of `Backspace`.

### 3.6 Change encoder behavior

Each layer has a `sensor-bindings` line.

Current examples:

- Base/media/mouse/num/sym use volume up/down.
- Nav uses page up/down.

To change encoder rotation on a layer, edit that layer's `sensor-bindings` line only.

### 3.7 Save the file carefully

Common mistakes that break builds:

- missing `;`
- missing `&kp` or other behavior prefix
- wrong number of arguments to a behavior
- deleting `<` or `>`

If the GitHub build later fails, the error line usually points to the exact broken line in this file.

## 4. Change firmware settings

### 4.1 Open the config file

Open `config/eyelash_corne.conf`.

### 4.2 Edit one setting per line

This file uses `CONFIG_*` entries.

Patterns used here:

- `CONFIG_NAME=y`: enable
- `CONFIG_NAME=n`: disable
- `CONFIG_NAME=123`: numeric value

### 4.3 Change existing settings

Current settings already in this repo include:

- `CONFIG_ZMK_IDLE_SLEEP_TIMEOUT=3600000`
- `CONFIG_ZMK_SLEEP=y`
- `CONFIG_WS2812_STRIP=y`
- `CONFIG_ZMK_RGB_UNDERGLOW=y`
- `CONFIG_ZMK_RGB_UNDERGLOW_ON_START=n`
- `CONFIG_ZMK_RGB_UNDERGLOW_AUTO_OFF_IDLE=y`
- `CONFIG_ZMK_RGB_UNDERGLOW_HUE_START=160`
- `CONFIG_ZMK_RGB_UNDERGLOW_EFF_START=3`
- `CONFIG_EC11=y`
- `CONFIG_ZMK_POINTING=y`
- `CONFIG_ZMK_BACKLIGHT=y`
- `CONFIG_ZMK_BACKLIGHT_BRT_START=100`
- `CONFIG_ZMK_POINTING_SMOOTH_SCROLLING=y`
- `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y`

Change the value on the existing line if you are changing one of these settings.

### 4.4 Add a new setting

To add a setting that is not already present:

1. Add a new line to `config/eyelash_corne.conf`.
2. Use the exact `CONFIG_...` name from ZMK or Zephyr documentation.
3. Save the file.

If you do not know the exact setting name or valid values, use:

`<add'l info needed - exact ZMK/Zephyr config symbol and allowed values>`

## 5. Build the firmware with GitHub Actions

This repo is already set up to build on every push through `.github/workflows/build.yml`.

### 5.1 Put your changes in Git

From the repo root, run:

```powershell
git status
git add config/eyelash_corne.keymap config/eyelash_corne.conf build.yaml INSTRUCTIONS.md
git commit -m "Update Eyelash Corne keymap/settings"
git push
```

If you changed only some of those files, remove the others from the `git add` command.

### 5.2 Wait for the build to finish

1. Open the repository on GitHub.
2. Open the `Actions` tab.
3. Open the newest `Build ZMK firmware` run.
4. Wait for it to finish.

If it fails:

1. Open the failed job.
2. Open the `West Build` step for the failed board.
3. Read the first error that mentions your `.keymap` or `.conf` file.
4. Fix that exact line.
5. Commit and push again.

## 6. Download the built files

After a successful Actions run:

1. Open the successful `Build ZMK firmware` run.
2. Download the `firmware` artifact.
3. Extract the ZIP file.

You should get UF2 files for:

1. left half
2. right half
3. settings reset

The exact filenames can vary with the build workflow naming scheme.

Match files by the words in the filename:

- `left` = left half firmware
- `right` = right half firmware
- `settings_reset` = reset firmware

## 7. Install the firmware on the keyboard

This board builds UF2 firmware. Install by copying each UF2 file to the keyboard while that half is in bootloader mode.

### 7.1 Put one half into bootloader mode

Use one of these methods:

1. Double-press the physical reset button on that half.
2. If that half is already running working firmware, trigger the bootloader key from the current keymap.

`<add'l info needed - exact physical reset button location on this keyboard/controller>`

When bootloader mode is active, Windows should show a new USB storage drive.

`<add'l info needed - exact drive name shown by this controller on Windows>`

### 7.2 Flash the left half

1. Put the left half into bootloader mode.
2. Copy the left-half UF2 file onto the bootloader drive.
3. Wait for the drive to disconnect itself.
4. Let the half reboot.

The left half is central. Test it first.

### 7.3 Flash the right half

1. Put the right half into bootloader mode.
2. Copy the right-half UF2 file onto the bootloader drive.
3. Wait for the drive to disconnect itself.
4. Let the half reboot.

### 7.4 If the halves will not pair, reset both halves

This repo already builds a `settings_reset` image. Use it only when pairing data is broken.

Warning: this clears stored settings such as Bluetooth bonds and other persistent data.

Reset procedure:

1. Put the left half into bootloader mode.
2. Flash the `settings_reset` UF2 to the left half.
3. Put the right half into bootloader mode.
4. Flash the `settings_reset` UF2 to the right half.
5. Flash the normal left-half UF2 to the left half.
6. Flash the normal right-half UF2 to the right half.
7. Reset both halves at about the same time.
8. Remove old Bluetooth pairings from the host computer.
9. Pair the keyboard again.

## 8. Test after flashing

### 8.1 Test USB first

1. Connect the left half by USB.
2. Confirm that key presses work.
3. Confirm that your changed keys do what you expect.
4. Confirm that layer-tap, homerow mods, joystick, and encoder changes still work.

### 8.2 Test wireless split behavior

1. Power both halves.
2. Reset both halves if they do not connect automatically.
3. Confirm that keys on the right half reach the host through the left half.

### 8.3 Test Bluetooth if you use Bluetooth

The keymap already includes Bluetooth and output-selection actions in the `media_layer`:

- `&out OUT_BLE`
- `&out OUT_USB`
- `&bt BT_CLR`

If you use Bluetooth, verify that:

1. the keyboard can pair
2. the active output is correct
3. key presses reach the host

`<add'l info needed - exact user-facing key positions for BT/profile/output controls in this Miryoku layout>`

## 9. Safe rules for future edits

1. Change `config/eyelash_corne.keymap` for keys.
2. Change `config/eyelash_corne.conf` for settings.
3. Leave `build.yaml` alone unless you need different firmware outputs.
4. Keep the `settings_reset` build entry. It is useful for split recovery.
5. Do not edit `boards/arm/eyelash_corne/eyelash_corne.keymap` for normal customization.
6. Build after every change.
7. Flash left and right halves with their matching UF2 files only.

## 10. Minimal change checklist

When you make a normal keymap or setting change, the full sequence is:

1. Edit `config/eyelash_corne.keymap` and/or `config/eyelash_corne.conf`.
2. Save the file.
3. `git add` the changed files.
4. `git commit`.
5. `git push`.
6. Wait for GitHub Actions to finish.
7. Download the `firmware` artifact.
8. Extract the ZIP.
9. Flash the left UF2 to the left half.
10. Flash the right UF2 to the right half.
11. Test USB.
12. Test split and Bluetooth behavior.