# Changelog

## Unreleased

### Fixed

- **The settings screen rendered empty, and writes had nowhere to go.** A
  third-party bar widget's `bar.shell` is a scoped `PluginShellApi` with no
  `pluginRegistry` on it, so the screen never found the manifest schema. The
  schema is now read from the service, which the shell does inject a scoped
  registry into, and writes go through `bar.shell.updateEntryInline`.

## 1.1.0

A settings screen, a keyboard, and a plugin that works on Omarchy's current
default instead of asking anyone off it.

### Added

- **Settings screen**, built from `manifest.json`'s own schema rather than a
  second copy of the field list. Opens over the panel with `s`, or through
  `omarchy-shell avila.ultra-docker settings`.
- **Keyboard.** The panel opens in command mode: `f` to find, `s` for settings,
  `r` to refresh, `1`…`9` to jump to a section, Tab to step, Escape to back out
  one thing at a time.
- **Colour palettes** — five built in, plus a custom one, or the Omarchy theme.
- **Stack order** — A–Z, running first, or worst first.
- **Per-metric toggles** for what rotates in the label.
- **A URL for right click**, for whatever you run your containers behind.
- **Rootless Docker** works with no configuration: nothing here names a socket
  path, so the `docker` CLI's own context is followed.

### Fixed

- **Stack actions were broken on every compose project.** The command array goes
  to argv with no shell in front of it, and every value was being quoted for a
  shell that was never there, so compose answered
  `invalid project name "'web-shop'"`. They name container ids now.
- **The daemon controls addressed the system unit unconditionally.** With a
  rootless daemon also running, "stop the Docker daemon" stopped the one the
  mosaic was *not* showing. The scope is read off the daemon that answered.
- **The third gauge said DISCO to English users.** Its two neighbours are the
  same word in both languages, so the odd one out looked like it belonged.
- **Notifications were Portuguese for everyone**, because they leave through
  `notify-send` rather than through a `Text`, where the translation rules live.
- **A flapping container notified about twice a minute, forever.** The pause
  between two restarts read as a recovery. A condition is announced once now,
  and a recovery needs two consecutive good reads.
- **Hovering the widget flickered.** There is no tooltip at all now — the mosaic
  is the summary, and a tooltip was a second, worse copy of the panel.

### Security

- **Works without the `docker` group**, which Omarchy stopped granting by
  default because it is passwordless root. Reads fail honestly, "no access" is
  its own state next to "daemon down", controls that cannot work are hidden, and
  the panel points at Omarchy's own opt-in rather than shipping one. No
  `usermod`, `gpasswd`, `setfacl`, `sudo` or `pkexec` anywhere, and nothing
  elevates on the polling path.
- **Compose labels are not paths.** A plain `LABEL` in a Dockerfile lands in
  every container from that image, so `working_dir` and `config_files` are
  attacker-chosen. They are validated before use, and stack actions no longer
  hand them to `compose -f … up -d`.
- **The agent handoff cannot be spoken over.** Labels and container logs are
  fenced, flattened to one bounded line, and preceded — in both languages — by a
  paragraph saying they are data that may pose as instructions.
- **Daemon output is bounded before it is read**, not after it is parsed, and
  container logs are bounded by bytes as well as by lines.

### Tests

157 checks to 217.
