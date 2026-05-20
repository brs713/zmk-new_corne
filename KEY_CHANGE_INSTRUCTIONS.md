# Eyelash Corne Key Change Instructions

This file explains exactly how to change one key in this Miryoku ZMK config.

## 1. Open the correct file

Open `config/eyelash_corne.keymap`.

That is the active keymap for this repo.

Do not edit `boards/arm/eyelash_corne/eyelash_corne.keymap` for normal key changes.

## 2. Find the layer you want to change

This keymap uses these layers:

- `base_layer`
- `nav_layer`
- `mouse_layer`
- `media_layer`
- `num_layer`
- `sym_layer`
- `fun_layer`

Each layer has a `bindings = < ... >;` block.

The commented lines above the bindings show the physical key order for that row.

Example from `base_layer`:

```text
// 13: outer_L  Q           W           E           R           T           joy_up      Y           U           I           O           P           outer_R
   &trans       &kp Q       &kp W       &kp E       &kp R       &kp T       &kp UP      &kp Y       &kp U       &kp I       &kp O       &kp P       &trans
```

Use the comment line to find the key position, then edit the binding directly under it.

## 3. Replace the binding

Change only the binding for that position.

Common binding forms in this repo:

- `&kp X`: send key `X`
- `&hm LCTL D`: tap `D`, hold `Left Control`
- `&lt NAV RET`: tap `Enter`, hold for the `NAV` layer
- `&trans`: transparent

### 3.1 Change a normal key

Examples:

- Change `&kp Q` to `&kp ESC`
- Change `&kp COMMA` to `&kp SEMI`
- Change `&kp SPACE` to `&kp TAB`

### 3.2 Change a homerow mod key

The `&hm` behavior means:

- first argument: hold behavior
- second argument: tap key

Example:

- `&hm LCTL D` means tap `D`, hold `Left Control`

To change it:

- change `&hm LCTL D` to `&hm LALT D` to keep tap `D` and change hold to `Left Alt`
- change `&hm LCTL D` to `&hm LCTL F` to keep hold `Left Control` and change tap to `F`

Keep the argument order the same.

### 3.3 Change a layer-tap key

The `&lt` behavior means:

- first argument: layer name
- second argument: tap key

Example:

- `&lt NUM BSPC` means tap `Backspace`, hold for the `NUM` layer

To change it:

- change `&lt NUM BSPC` to `&lt NUM DEL`
- change `&lt NAV RET` to `&lt MOUSE RET`

Keep the argument order the same.

## 4. Do not break the file

When you edit one key:

1. Keep the leading `&`.
2. Keep the binding inside the layer's `< ... >` block.
3. Do not delete the final `;` after the bindings block.
4. Do not delete `<` or `>`.
5. Do not remove arguments from `&hm` or `&lt`.

Common mistakes:

- `Q` instead of `&kp Q`
- `&kp` with no keycode after it
- `&hm D` with one argument instead of two
- editing the comment line instead of the binding line

## 5. Save the file

Save `config/eyelash_corne.keymap`.

## 6. Build the updated firmware

From the repo root, run:

```powershell
git add config/eyelash_corne.keymap KEY_CHANGE_INSTRUCTIONS.md
git commit -m "Change key in Eyelash Corne keymap"
git push
```

If you changed only the keymap, you can omit other files.

Then:

1. Open the repository on GitHub.
2. Open `Actions`.
3. Open the newest `Build ZMK firmware` run.
4. Wait for it to succeed.
5. Download the `firmware` artifact.
6. Extract the ZIP.

## 7. Flash the keyboard

This repo builds UF2 firmware for:

1. left half
2. right half
3. `settings_reset`

For a normal key change, use the normal left and right UF2 files. Do not use `settings_reset` unless you are fixing pairing problems.

### 7.1 Flash the left half

1. Put the left half into bootloader mode.
2. Copy the left UF2 file to the bootloader drive.
3. Wait for the drive to disconnect.
4. Let the half reboot.

### 7.2 Flash the right half

1. Put the right half into bootloader mode.
2. Copy the right UF2 file to the bootloader drive.
3. Wait for the drive to disconnect.
4. Let the half reboot.

`<add'l info needed - exact physical reset button location for entering bootloader on this hardware>`

`<add'l info needed - exact Windows drive name shown by this controller in bootloader mode>`

## 8. Test the changed key

1. Connect the left half by USB.
2. Press the changed key.
3. Confirm it sends the new keycode.
4. If it is a homerow mod or layer-tap key, test both tap and hold.
5. If the key is on a non-base layer, activate that layer and test there.

## 9. If the build fails

Open the failed GitHub Actions job and read the first error that mentions `config/eyelash_corne.keymap`.

Usually the problem is one of these:

- missing `;`
- missing `&kp`
- wrong number of arguments
- wrong keycode name

Fix that line, then commit and push again.

## 10. Minimal checklist

1. Open `config/eyelash_corne.keymap`.
2. Find the correct layer.
3. Replace the binding for that key.
4. Save the file.
5. Commit and push.
6. Download the built firmware artifact.
7. Flash left and right UF2 files.
8. Test the changed key.