# DRAFT - ojuny-pro-desktop

Status: draft scope. Nothing is built. This file describes what the repository
is supposed to contain, not what it contains today.

## Purpose

The GNOME desktop layer for Ojuny Pro. It is the Pro equivalent of
`ojuny-desktop`: a standalone flake that turns structured options into a
configured GNOME session, and exports a NixOS module and a Home Manager module.

It is consumed by `ojuny-pro-template`. It does not contain a host, a user, a
disk layout or an installer.

## What the repository will contain

```text
ojuny-pro-desktop/
  flake.nix                  package, modules and option exports
  modules/
    nixos.nix                session, GDM, portals, GNOME services
    home-manager.nix         user session and dconf defaults
    presets.nix              layout preset definitions
    extensions.nix           reviewed extension set
    branding.nix             logo, wallpaper and shell defaults
  docs/                      options, presets, design
  tests/                     evaluation and headless checks
```

### Flake outputs

- `packages.ojuny-pro-desktop`: the wrapped session package.
- `nixosModules.default`: installs the session on NixOS.
- `homeManagerModules.default`: installs the session for one user.
- `apps.run`: a nested or disposable session for evaluation.
- Checks that evaluate both modules.

### Options

A single option surface, shared by the NixOS and Home Manager modules. The
namespace is to be decided, with `desktop.ojuny.pro.*` or
`desktop.ojuny.gnome.*` as the current candidates.

The surface covers:

- Layout preset: default GNOME, Windows-like, macOS-like.
- Dock and panel behavior, overview and workspace behavior.
- Keybinding preset selection.
- GDM and login behavior.
- Theme, accent color, wallpaper, fonts and cursor.
- Reviewed extension enablement and per-extension settings.
- Default browser and default applications owned by the desktop layer.
- Lock screen and screen blanking policy.
- Reduced animation and accessibility defaults.

### Layout presets

Each preset is a complete, versioned set of GNOME settings and extension
configuration. A preset can be selected at install and changed later on the
desktop page. Presets must not change the application set, the security
baseline or the account model.

The three presets are:

- Default: stock GNOME behavior with Ojuny branding and defaults.
- Windows-like: bottom panel, application menu, taskbar-style window list.
- macOS-like: top status bar and a bottom dock, with a global menu if the
  extension set supports it.

### Security defaults

- The desktop does not add a remote access listener.
- Screen lock is enabled by default.
- Extension sources are pinned in the flake and reviewed.
- No shell integration that executes unverified code at login.
- No telemetry or online account integration.

### Documentation

- Every option and its default.
- The exact contents of each layout preset.
- The extension allowlist and why each extension is included.
- How the session is generated and linked.
- The Home Manager display-manager limitation, inherited from the core
  desktop.

### Tests

- Both modules evaluate.
- Each preset produces a valid set of dconf values.
- A headless GNOME session starts with the generated configuration.
- The extension allowlist contains no unpinned source.
- A rebuild changes the session without requiring a new account.

## Limits and non-goals

- GNOME only. No MangoWM, Dux Modular Shell, KDE or COSMIC.
- No host configuration, no hardware and no users.
- No professional application catalog. That belongs to the template.
- No fleet enrollment or policy engine.
- No theme marketplace and no arbitrary extension installation.
- The Home Manager session entry may not be scanned by GDM, as documented for
  the core desktop. The NixOS module is the primary path.

## Open questions

- Final option namespace.
- Whether the Windows-like and macOS-like layouts need extensions outside the
  GNOME core set.
- Whether the nested runner is feasible for GNOME, or whether a VM is the only
  evaluation path.
- Whether presets are shipped as data files or generated from Nix.
- Whether the desktop layer owns default applications or leaves them to the
  template.

## Sources

- `ojuny-desktop/README.md` and `DESIGN.md`.
- `nixos-configs/notes/ojuny pro - early draft.md`.
- `ojuny-docs/02-project-architecture.md`.
