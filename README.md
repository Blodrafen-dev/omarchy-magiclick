# MagiClick — Omarchy bar widget

The bar front-end for the [MagiClick](https://github.com/Blodrafen-dev/magiclick)
daemon. Select text, click where it goes, it is already there.

This repository is **only the widget**. It is one plugin, `blodrafen.magiclick`,
and it does nothing on its own — it reads four files the daemon publishes and
calls the `magiclick` command. The design reasoning, including the QML bug that
made the menu rows clickable with no daemon running, lives in the daemon's
[`docs/decisions.md`](https://github.com/Blodrafen-dev/magiclick/blob/master/docs/decisions.md).

## Install

**1. Install the daemon first.** The widget cannot install it, and a widget
without a daemon shows `off` and does nothing when clicked.

```bash
yay -S magiclick-bin                    # or: paru -S magiclick-bin
magiclick doctor                        # tells you what works on your machine
systemctl --user enable --now magiclick.service
```
Without the AUR: clone the [daemon repository](https://github.com/Blodrafen-dev/magiclick) and run `./install.sh` in it.

Start with `confirm_action = "observe"` in `~/.config/magiclick/config.toml`
unless you already trust it: it logs the paste it *would* have made and types
nothing.

**2. Add the widget.**

```bash
omarchy plugin add https://github.com/Blodrafen-dev/omarchy-magiclick.git --enable
```

Or, to place it by hand:

```bash
omarchy bar put blodrafen.magiclick right
```

## What the glyph says

| Glyph | State | Meaning |
|---|---|---|
| ⚠ | no daemon | the daemon is not running — it removes its state file on a clean stop, so this is honest rather than a guess. Click the glyph for the install line |
| ✕ | `off` | nothing is being watched |
| copy | `on` | auto-copy is on, waiting for a selection |
| clipboard | `armed` | a selection was just copied; a click would paste |

`armed` is the safety feature — you can see when the thing is loaded.

- **Left click** turns auto-copy on (from `off`) or opens the menu.
- **Right click** opens the menu.
- The menu sets the auto-copy window: 1× to 4× the configured `arm_timeout_ms`,
  each row labelled with the seconds it buys. It is session-scoped, so a
  lengthened window goes back to your config at logout.
- **Disable** in the menu stops watching. This is deliberately a second,
  deliberate click: a stray click on the bar should not disarm the feature whose
  whole job is to tell you it is armed.

## Remove

```bash
omarchy plugin remove blodrafen.magiclick
```

Removing the widget leaves the daemon installed. To remove that too:

```bash
systemctl --user disable --now magiclick.service
sudo pacman -Rns magiclick-bin     # from source instead? rm ~/.local/bin/magiclick
```

## Requirements

- The `magiclick` binary on `PATH` — this widget shells out to it.
- Omarchy (Quickshell shell) with the bar.
- Your config's `confirm_key` needs to be bound to a real button if you want the
  paste half; the widget only reports and configures.

External dependency, stated plainly: **none of this repository is self-contained
without `magiclick`.** That is why the daemon is a separate repository — the
plugin installer clones one repository into your shell and validates its root,
so it cannot build a Rust daemon.

## License

MIT, © 2026 Dek.
