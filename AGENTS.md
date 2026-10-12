# Personal Tela-circle fork

This repository maintains custom app icons on top of vinceliuice's Tela-circle.
For adding or repairing app icons, merging upstream icon-theme updates, or
verifying/installing this personal theme, read
`.agents/skills/maintain-tela-circle/SKILL.md`.

## Repository conventions

- Personal changes normally belong on `personal-icons`. Respect another branch
  explicitly selected by the user.
- `origin` is the user's fork, `AngelosGkelias/Tela-circle-icon-theme`.
  `upstream` is `vinceliuice/Tela-circle-icon-theme`; its baseline branch is
  `master`. Inspect the actual remotes before synchronizing.
- App artwork belongs in `src/scalable/apps/`. Upstream generally keeps app
  aliases in `links/scalable/apps/`; existing custom aliases can also be beside
  their artwork. Preserve relative symlinks and check the installed result.
- Current custom artwork: `antigravity.svg`, `antigravity-ide.svg`,
  `pulseview.svg`, and `ltspice.svg`. `org.sigrok.PulseView.svg` aliases
  `pulseview.svg`.
- Keep the user's circular style and existing artwork unless a redesign is
  requested. `ltspice.svg` embeds the original PNG; it is not vectorized artwork.
- Install personal builds as `Tela-circle-personal`, in the user's icon directory.
  The installer also creates `-light` and `-dark` variants. The `-c` flag changes
  folder artwork, so use it only when circular folders are requested.
- Edit tracked source, not installed theme files. Installation recreates its
  destination directories. Desktop launchers are machine configuration outside
  this repository; change them only as part of the requested task.

## Checks and delivery

Run `git diff --check`, validate affected SVGs and aliases, and check an isolated
installation when changing app artwork or installation behavior. Use `bash -n
install.sh` when editing the installer. Follow the user's task scope for desktop
activation, commits, and GitHub publishing; creating a skill alone does not
authorize future maintenance runs or scheduled updates.
