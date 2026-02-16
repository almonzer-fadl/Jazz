# Use the repo OpenClaw everywhere (ditch global install)

Use the Jazz repo version of OpenClaw for both CLI and gateway so your code changes (and config) are the only ones in use.

## 1. Prefer the repo `openclaw` in your shell

Add the repo’s `node_modules/.bin` to the **front** of your `PATH` so `openclaw` resolves to the repo binary:

```bash
# In ~/.zshrc (or ~/.bashrc)
export PATH="/Users/almonzerfadl/Documents/Programming/Jazz/node_modules/.bin:$PATH"
```

Reload your shell (or run `source ~/.zshrc`). Then:

```bash
which openclaw
# Should show: .../Jazz/node_modules/.bin/openclaw

openclaw --version
# Should show repo version (e.g. 2026.2.10)
```

## 2. (Optional) Remove the global install

So you never accidentally use the old version:

```bash
# If you installed with npm:
npm uninstall -g openclaw

# If you installed with pnpm (e.g. into ~/.node_modules):
pnpm uninstall -g openclaw
```

## 3. Install and start the gateway from the repo

Config and credentials stay in `~/.openclaw`; the repo build uses the same paths, so no config migration.

From the repo:

```bash
cd /Users/almonzerfadl/Documents/Programming/Jazz

pnpm build
openclaw gateway install --force
openclaw gateway start
```

- `gateway install --force` overwrites the existing LaunchAgent so the **repo** binary and `dist/` are used.
- `gateway start` loads the LaunchAgent (gateway listens on the port in `~/.openclaw/openclaw.json`, e.g. 18789).

Check:

```bash
openclaw daemon status
```

You should see **Working dir** or **Command** pointing at the Jazz repo (or its `dist/`), and the service loaded.

## 4. Same config everywhere

- **Config:** `~/.openclaw/openclaw.json` (unchanged).
- **Credentials:** `~/.openclaw/credentials/` (unchanged).
- **Sessions / state:** `~/.openclaw/sessions/`, etc. (unchanged).

The repo build reads the same `HOME` and config paths as the global install, so everything stays the same; only the binary and gateway process come from Jazz.

## Summary

| Step | Action                                                                                   |
| ---- | ---------------------------------------------------------------------------------------- |
| 1    | Add Jazz `node_modules/.bin` to the front of `PATH` in `~/.zshrc`                        |
| 2    | (Optional) `pnpm uninstall -g openclaw` or `npm uninstall -g openclaw`                   |
| 3    | `cd Jazz` → `pnpm build` → `openclaw gateway install --force` → `openclaw gateway start` |

After this, `openclaw` and the gateway both run from the repo and use the same configs.
