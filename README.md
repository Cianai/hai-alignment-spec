# Two-host macOS configuration

Private chezmoi source for the persistent MacBook Pro and Mac Mini estate.
The repository declares reviewed configuration and intended host differences;
it does not publish local drift automatically.

## Ownership

| State | Owner |
| -- | -- |
| Reviewed shell and launchd configuration | chezmoi source plus host templates |
| Required Homebrew membership | the managed Brewfile; `brew bundle check/install` restores missing formulae |
| Upgrades of installed host tools | Topgrade and each retained native updater |
| Repository dependency pins | Renovate in the consuming repository |
| Secrets and credentials | 1Password; only `op://` references may enter templates |
| MCP contracts, plugin and skill membership, agent instructions | their native or Claudian repository authorities, never this repository |

The current managed slice is deliberately narrow:

- `~/.zshrc` on the MacBook; the Mini keeps its existing shell state until a
  reviewed host overlay replaces it
- `~/.config/topgrade.toml`
- `~/.local/bin/topgrade-daily.sh`
- `~/Library/LaunchAgents/com.cianosullivan.topgrade-daily.plist`
- `~/.config/chezmoi/Brewfile`

The always-on Mini additionally proves the split maintenance cadence before it
is projected onto the MacBook:

- read-only dependency drift observation every four hours;
- one successful daily mutation across bounded 05:30, 06:30, and 07:30 retry
  windows; and
- manager-native garbage collection at 04:30 each Sunday, excluding pnpm store
  pruning and undeclared-extra removal.

Secret-bearing paths and coding-agent primitive directories are denied in
`.chezmoiignore`.

## Host workflow

1. Change the source on a reviewed branch and verify both intended hosts.
2. Merge the reviewed source.
3. On a host, pull and inspect without applying:

   ```sh
   chezmoi git pull -- --autostash --rebase
   chezmoi diff
   ```

4. Apply only after the diff is understood:

   ```sh
   chezmoi apply
   ```

`git.autoCommit` and `git.autoPush` remain disabled. A failed pull, template,
diff, secret lookup, or apply stops at that host; it never force-converges or
publishes local state.

The Topgrade template is host-aware. Both hosts receive the same updater,
membership, scheduler, and safe cache-maintenance contract. MacBook-only uv
tools are omitted on the Mini, and an absent optional manager such as dotnet,
pipx, or gcloud is recorded as an intentional skip rather than a failed fleet
step.

The Mac Mini `cc-*` model-routing fallback functions are intentionally absent.
Their historical payload references keys that are not present in either
approved store, and one route still has an unauthenticated LAN-front caveat.
They can be added as a Mini-only template only after those bindings exist and
are verified headlessly.
