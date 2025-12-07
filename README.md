# Mac Slovenian Keyboard Layout for Toshy on Linux

This configuration makes a Linux system with Toshy behave like a Mac with a Slovenian keyboard layout.

## Problem

When using Toshy on Linux with a Slovenian keyboard layout, many characters are in wrong positions because:
1. Linux "Slovenian (US)" layout differs from macOS Slovenian layout
2. Mac Slovenian uses QWERTY, but Linux "Slovenian (US)" is actually QWERTZ
3. The Option-key special characters are different between Mac and Linux

## Solution

Add the following configuration to your Toshy config file.

### Prerequisites

- Toshy installed and working
- `ibus` or `fcitx5` as your input method (required for UC() function)
- Linux keyboard layout set to "Slovenian (US)" - `si(us)`

Check your input method:
```bash
echo $GTK_IM_MODULE  # Should show: ibus or fcitx5
```

### Configuration

Add this to your `~/.config/toshy/toshy_config.py` inside the `user_apps` slice markers:

```python
###  SLICE_MARK_START: user_apps  ###

# Mac Slovenian Keyboard Layout Fixes
# Fixes for Mac Slovenian QWERTY keyboard on Linux
# Uses UC() function - has slight visual lag but works reliably
# Source: https://blog.spanger.org/?p=307

keymap("Mac Slovenian - Base layer", {
    # Base layer fixes - keys that differ between Mac and Linux Slovenian
    C("MINUS"):                     UC(0x002F),                     # / slash (key after 0)
}, when = lambda ctx:
    cnfg.screen_has_focus and
    matchProps(not_clas=remoteStr)(ctx)
)

keymap("Mac Slovenian - Shift layer", {
    # Shift layer fixes
    C("Shift-KEY_7"):               UC(0x0027),                     # ' apostrophe
    C("Shift-MINUS"):               UC(0x003F),                     # ? question mark (Shift + key after 0)
}, when = lambda ctx:
    cnfg.screen_has_focus and
    matchProps(not_clas=remoteStr)(ctx)
)

keymap("Mac Slovenian - Option layer", {
    # Option layer - characters via Option key on Mac Slovenian QWERTY
    C("Alt-LEFT_BRACE"):            UC(0x005B),                     # [ left square bracket (š key)
    C("Alt-RIGHT_BRACE"):           UC(0x005D),                     # ] right square bracket (đ key)
    C("Alt-BACKSLASH"):             UC(0x005C),                     # \ backslash (ž key)
    C("Alt-N"):                     UC(0x007E),                     # ~ tilde
    C("Alt-APOSTROPHE"):            UC(0x005E),                     # ^ caret (ć key)
    C("Alt-KEY_102ND"):             UC(0x0060),                     # ` backtick (ISO key)
    C("Alt-GRAVE"):                 UC(0x0060),                     # ` backtick (key left of 1)
}, when = lambda ctx:
    cnfg.screen_has_focus and
    matchProps(not_clas=remoteStr)(ctx)
)

keymap("Mac Slovenian - Option+Shift layer", {
    # Option+Shift layer
    C("Alt-Shift-LEFT_BRACE"):      UC(0x007B),                     # { left curly brace (š key)
    C("Alt-Shift-RIGHT_BRACE"):     UC(0x007D),                     # } right curly brace (đ key)
    C("Alt-Shift-BACKSLASH"):       UC(0x007C),                     # | pipe (ž key)
    C("Alt-Shift-KEY_2"):           UC(0x0040),                     # @ at sign
    C("Alt-Shift-KEY_5"):           UC(0x20AC),                     # € euro sign
    C("Alt-Shift-KEY_102ND"):       UC(0x007E),                     # ~ tilde (ISO key)
    C("Alt-Shift-GRAVE"):           UC(0x007E),                     # ~ tilde (grave key)
}, when = lambda ctx:
    cnfg.screen_has_focus and
    matchProps(not_clas=remoteStr)(ctx)
)

###  SLICE_MARK_END: user_apps  ###
```

### Z/Y Swap (QWERTY vs QWERTZ)

If your Mac has a QWERTY Slovenian layout but Linux gives you QWERTZ (Z and Y swapped), add this to the `user_custom_modmaps` slice:

```python
###  SLICE_MARK_START: user_custom_modmaps  ###

# Mac Slovenian keyboard QWERTY fix - swap Z and Y keys
# Mac Slovenian uses QWERTY layout but Linux "Slovenian (US)" is actually QWERTZ
modmap("Mac Slovenian QWERTY fix", {
    Key.Y: Key.Z,
    Key.Z: Key.Y,
}, when = lambda ctx:
    cnfg.screen_has_focus and
    matchProps(not_clas=remoteStr)(ctx)
)

###  SLICE_MARK_END: user_custom_modmaps  ###
```

### Apply Changes

After editing the config, restart Toshy:

```bash
toshy-config-restart
```

## Character Mapping Reference

### Base/Shift Layer

| Key | Base | Shift | Notes |
|-----|------|-------|-------|
| 7 | 7 | ' | Apostrophe on Shift |
| Key after 0 | / | ? | Slash and question mark |

### Option (Alt) Layer

| Key Combo | Output | Description |
|-----------|--------|-------------|
| Option + š | [ | Left square bracket |
| Option + đ | ] | Right square bracket |
| Option + ž | \ | Backslash |
| Option + N | ~ | Tilde |
| Option + ć | ^ | Caret |
| Option + ` | ` | Backtick |

### Option+Shift Layer

| Key Combo | Output | Description |
|-----------|--------|-------------|
| Option+Shift + š | { | Left curly brace |
| Option+Shift + đ | } | Right curly brace |
| Option+Shift + ž | \| | Pipe |
| Option+Shift + 2 | @ | At sign |
| Option+Shift + 5 | € | Euro sign |
| Option+Shift + ` | ~ | Tilde |

### Physical Key Positions

On a Slovenian keyboard, these special characters replace the bracket keys:
- š = where `[` is on US layout (LEFT_BRACE)
- đ = where `]` is on US layout (RIGHT_BRACE)
- ž = where `\` is on US layout (BACKSLASH)
- č = where `;` is on US layout (SEMICOLON)
- ć = where `'` is on US layout (APOSTROPHE)

## Known Limitations

1. **Slight visual lag**: The UC() function uses GTK's Unicode input method, which briefly shows the Unicode code (e.g., "u40") before the character appears. This is cosmetic - the correct character is always typed.

2. **Requires ibus/fcitx5**: The UC() function only works with these input methods.

3. **May not work in all apps**: Some non-GTK applications may not respond to the Unicode input method.

## Troubleshooting

### Characters not appearing

1. Check that ibus is your input method:
   ```bash
   echo $GTK_IM_MODULE
   ```

2. Verify Toshy is running:
   ```bash
   toshy-config-restart
   ```

### Wrong characters

1. Verify your Linux layout is set to Slovenian (US):
   ```bash
   setxkbmap -query
   ```
   Should show: `layout: si` and `variant: us`

## Credits

- [Toshy](https://github.com/RedBearAK/Toshy) - Mac-style keyboard shortcuts for Linux
- [Mac OS X Shortcuts (Slovenian keyboard)](https://blog.spanger.org/?p=307) - Reference for Mac Slovenian layout
- [Slovenian Mac OS X keyboard layout | Miha Hribar](https://miha.hribar.org/2012/03/mac-slovenian-keyboard-layout/)
