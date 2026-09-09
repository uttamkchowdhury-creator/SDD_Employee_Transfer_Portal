# Release vX.Y.Z — <YYYY-MM-DD>

<!-- Filename: .ai-context/releases/vX.Y.Z.md -->

**Cut by:** <name> · **Tag:** `vX.Y.Z` · **Target:** <branch> → <environment>

## Specs included

| Spec ID | Title | Status after cut |
|---|---|---|
| `<slug>` | <title> | Released (vX.Y.Z) |

## Release notes
<!-- Drafted from spec intents, never from commit messages. -->
- <User-facing outcome> (`<slug>`).

## Hotfixes included
| Hotfix ID | Fixes | Original spec |
|---|---|---|
| HOTFIX-<...> | <what> | `<slug>` |

## Checklist
- [ ] All constituent specs were `Ready for Release` in `status.md` before the cut
- [ ] Each included spec's Status flipped to `Released (vX.Y.Z)`
- [ ] `status.md` Active Specs rows moved or archived the same day
- [ ] Release notes drafted from spec intents
- [ ] `dbDelta()` migrations reviewed and applied in order, `db_version` bumped
- [ ] REST contract matches each spec's documented API contract
- [ ] Breaking change? If yes, new namespace version + ADR + consumer coordination

## Rollback
<Migration reversibility and the rollback procedure for this specific release.>
