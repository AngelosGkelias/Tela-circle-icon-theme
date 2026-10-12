---
name: maintain-tela-circle
description: "Maintain this personal Tela-circle fork: add or repair app icons, merge upstream theme updates, and verify or install the personal theme. Use for this repository's icon maintenance, not generic Git questions or unrelated themes."
---

# Maintain Tela-circle

Read the repository's `AGENTS.md` for its branch, remotes, custom icons, and
installation conventions. Resolve paths relative to the repository, not a
hardcoded checkout directory. Apply only the workflow requested by the user;
adding one icon need not also synchronize upstream or activate the desktop.

## Establish the current state

Inspect `git status --short`, `git branch --show-current`, and `git remote -v`.
Preserve existing user edits and avoid including unrelated changes in delivery.
Inspect the relevant source icons, aliases, and `install.sh` before changing them.
Do not assume that the installed theme and repository have identical artwork.

## Add or repair an app icon

1. Inspect the app's effective desktop launcher and its `Icon=` value. A value
   such as `pulseview` resolves through the theme; an absolute image path bypasses
   it. Use the requested app's launchers rather than broadly rewriting launchers.
2. Put canonical artwork in `src/scalable/apps/<icon-name>.svg`. Reuse supplied
   artwork or the existing custom icon where appropriate. Follow nearby icons for
   dimensions, padding, and circular background. Choose native SVG for geometric
   artwork; follow the available image-generation workflow for requested raster
   artwork or edits.
3. To package an existing PNG without changing its pixels, embed its base64 data
   in an SVG `<image>` using a `data:image/png;base64,...` URI and explicit canvas
   dimensions. Keep it self-contained and preserve its aspect ratio. Explain that
   an embedded bitmap remains raster artwork.
4. Add relative symlink aliases for additional launcher names when needed.
   Check both `src/scalable/apps/` and `links/scalable/apps/` for collisions: the
   installer copies artwork first and then aliases. For example, an alias named
   `org.sigrok.PulseView.svg` can target `pulseview.svg` in the same installed
   directory. Existing PulseView artwork and its alias are already in `src`.
5. When theme lookup is part of the request, change the relevant launcher's
   `Icon=` to the icon name without an extension. Preserve its launch command and
   other behavior. Check a separate desktop shortcut if the user uses one.
6. Update the custom-icon inventory in `AGENTS.md` and the existing README's
   custom-icon note when additions or removals change it.

## Synchronize upstream

Verify the original repository's remote and work on the intended personal branch.
Fetch upstream, then inspect the incoming changes, especially matching icon
names, aliases, and installer changes. Merge `upstream/master` into the personal
branch; updating local `master` first is unnecessary.

```bash
git fetch upstream
git diff HEAD...upstream/master -- src/scalable/apps links/scalable/apps install.sh
git merge upstream/master
```

Use an ordinary merge to preserve published history. If a conflict appears,
inspect the artwork or link from each side and preserve the intended custom
result; do not choose an entire side blindly. If upstream now supplies a formerly
custom icon, compare the versions before changing which one the fork uses.
Re-run relevant checks after the merge.

## Verify the result

- Run `git diff --check`. Parse affected SVGs and load them with an SVG renderer
  such as librsvg when available. Inspect a rendered preview for new or changed
  artwork. Check relative links resolve and embedded images are self-contained.
- For installer edits, also run `bash -n install.sh`.
- Check installation in a fresh temporary directory, using the existing installer
  and the requested options:

  ```bash
  preview_dir="$(mktemp -d /tmp/tela-circle-preview.XXXXXX)"
  bash ./install.sh -d "$preview_dir" -n Tela-circle-personal
  ```

  Inspect output as well as exit status. Verify affected icon names and aliases
  in the standard, light, and dark variants and confirm their SVGs load. When GTK
  is available, use an isolated `Gtk.IconTheme` with this directory as its search
  path to check launcher-name lookup. Inspect changed art at a small app-icon size
  and a larger preview. Remove only this temporary build after verification.
- Report any unavailable rendering or desktop lookup checks without claiming
  they passed. Do not fix unrelated pre-existing theme defects as part of adding
  one icon.

## Install and deliver

For a requested local installation, use:

```bash
bash ./install.sh -d "$HOME/.local/share/icons" -n Tela-circle-personal
```

The installer recreates named destinations, so keep custom work in tracked
source. Retain the requested folder/color choices. Activate the theme only when
desktop activation is within the user's request:

```bash
gsettings set org.gnome.desktop.interface icon-theme 'Tela-circle-personal'
gsettings get org.gnome.desktop.interface icon-theme
```

An exit code alone does not confirm activation; verify the readback. For requested
Git delivery, stage the task's changes explicitly, review the staged diff, commit,
and push to the intended fork branch. Do not force-push or open an upstream PR as
an implicit part of maintaining the personal fork. Report what changed, the
checks completed, whether it was installed/activated, and its commit/push state.
