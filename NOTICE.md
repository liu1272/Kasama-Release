# NOTICE

Kasama is a derivative work that combines / integrates:

1. **Kasama** — Go control plane, PostgreSQL state, planner/worker engine, traffic proxy, governance.
   - License: GNU Affero General Public License v3.0 (AGPL-3.0).
   - Baseline: local workspace snapshot (`C:\kits\AISandbox\WORK\Kasama`).

2. **KasamaStrike** — TypeScript/Bun agent runtime, agent roster, tools, skills, methodology engine.
   - License: GNU Affero General Public License v3.0 (AGPL-3.0).
   - Baseline: local workspace snapshot (`C:\kits\AISandbox\WORK\KasamaStrike`).
   - KasamaStrike is a derivative of **CyberStrike** (`C:\kits\AISandbox\WORK\CyberStrike`); the
     upstream copyright/license notices are retained. Names such as `@cyberstrike-io/*`,
     `cyberstrike.jsonc`, `CYBERSTRIKE_*` and `x-cyberstrike-directory` are compatibility
     identifiers, not evidence that the old runtime is in use.

3. **norma** — Go agent harness used by Kasama.
   - License: see upstream (`github.com/Autumn-27/norma`).

## Obligations

- If Kasama is distributed or offered as a network service, the corresponding source of the derivative must be made available under AGPL-3.0.
- Preserve upstream copyright notices and license texts.
- Do not remove license headers from copied files.

## What to do when forking

1. Copy `LICENSE` from Kasama into the Kasama root.
2. Keep KasamaStrike's `LICENSE` **and** the upstream CyberStrike `LICENSE` in their directories.
3. Add a `THIRD_PARTY_NOTICES.md` listing upstream versions/commits.
4. If only invoking KasamaStrike as a separate process (not linking/copying code), document the boundary clearly in this NOTICE.

> This file is not legal advice. Review with counsel before commercial distribution.
