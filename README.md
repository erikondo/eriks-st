# st - the simple (suckless) terminal

A build of the [suckless terminal (st)](https://st.suckless.org/), based on
Luke Smith's fork, updated to upstream **st 0.9.3** and extended with inline
IME (input method) support.

## Changes in this build

+ **Updated to st 0.9.3** (from 0.8.5) by merging upstream st into the
  patched tree, keeping all patches and features below intact. This brings
  in all upstream fixes and improvements from 0.9.x, including the reworked
  window creation/embedding flow and various escape sequence and rendering
  fixes.
+ **Inline (on-the-spot) IME preedit.** When composing text with an input
  method (e.g. typing Korean, Japanese, or Chinese with fcitx5), the
  composing text is rendered directly in the terminal at the cursor,
  instead of in a floating preedit window. See
  [Inline input method composition](#inline-input-method-composition) below
  for the required fcitx5 setting. Falls back to the previous over-the-spot
  behavior if the input method doesn't support it.

## Unique features (using dmenu)

+ **follow urls** by pressing `alt-l`
+ **copy urls** in the same way with `alt-y`
+ **copy the output of commands** with `alt-o`

## Bindings for

+ **scrollback** with `alt-↑/↓` or `alt-pageup/down` or `shift` while scrolling the
  mouse.
+ OR **vim-bindings**: scroll up/down in history with `alt-k` and `alt-j`.
  Faster with `alt-u`/`alt-d`.
+ **zoom/change font size**: same bindings as above, but holding down shift as
  well. `alt-home` returns to default
+ **copy text** with `alt-c`, **paste** is `alt-v` or `shift-insert`

## Pretty stuff

+ Compatibility with `Xresources` and `pywal` for dynamic colors.
+ Default [gruvbox](https://github.com/morhetz/gruvbox) colors otherwise.
+ Transparency/alpha, which is also adjustable from your `Xresources`.
+ Default font is system "mono" at 14pt, meaning the font will match your
  system font.

## Other st patches

+ Boxdraw
+ Ligatures
+ font2

## Installation

You should have xlib header files and libharfbuzz build files installed.

```
cd st
sudo make install
```

Obviously, `make` is required to build. `fontconfig` is required for the
default build, since it asks `fontconfig` for your system monospace font. It
might be obvious, but `libX11` and `libXft` are required as well. Chances are,
you have all of this installed already.

On OpenBSD, be sure to edit `config.mk` first and remove `-lrt` from the
`$LIBS` before compiling.

Be sure to have a composite manager (`xcompmgr`, `picom`, etc.) running if you
want transparency.

## How to configure dynamically with Xresources

For many key variables, this build of `st` will look for X settings set in
either `~/.Xdefaults` or `~/.Xresources`. You must run `xrdb` on one of these
files to load the settings.

For example, you can define your desired fonts, transparency or colors:

```
*.font:	Liberation Mono:pixelsize=12:antialias=true:autohint=true;
*.alpha: 0.9
*.color0: #111
...
```

The `alpha` value (for transparency) goes from `0` (transparent) to `1`
(opaque). There is an example `Xdefaults` file in this respository.

### Colors

To be clear about the color settings:

- This build will use gruvbox colors by default and as a fallback.
- If there are Xresources colors defined, those will take priority.
- But if `wal` has run in your session, its colors will take priority.

Note that when you run `wal`, it will negate the transparency of existing windows, but new windows will continue with the previously defined transparency.

## Inline input method composition

This build renders IME composition (preedit) text inline at the cursor,
underlined, with the input method's highlighting respected. For this to
work, the input method must offer the on-the-spot (`XIMPreeditCallbacks`)
style over XIM.

For **fcitx5**, enable it with either:

- `fcitx5-configtool` → Addons → X Input Method Frontend → check
  "Use On The Spot Style", or
- add the following to `~/.config/fcitx5/conf/xim.conf`:

  ```
  UseOnTheSpotStyle=True
  ```

Then restart fcitx5 (`fcitx5 -r -d`). Make sure `XMODIFIERS=@im=fcitx` is
set in your environment so st connects to fcitx5 via XIM.

Note: candidate lists (e.g. hanja/kanji conversion) are still shown in a
popup by the input method itself; only the composing text is drawn inline.
