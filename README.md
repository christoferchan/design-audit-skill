# Design Audit Skill for Claude Code

An end-to-end visual design audit pipeline for [Claude Code](https://claude.ai/code). Captures screenshots of your entire app, analyzes every screen against your design system, proposes fixes with multiple options, and implements approved changes — all from a single command.

Works with **mobile** (React Native, Expo, Flutter) and **web** (Next.js, React, Vue, Svelte) apps.

---

## Quick Start

### 1. Install the skill

```bash
git clone https://github.com/christoferchan/design-audit-skill.git ~/.claude/skills/design-audit
```

That's it. No npm install, no dependencies, no config files.

### 2. Open your project in Claude Code

```bash
cd your-project
claude
```

### 3. Run the audit

```
/design-audit
```

Claude will walk you through the entire process — starting with a few questions about your design preferences.

---

## What It Does

```
/design-audit
    │
    ▼ Phase 0A: Asks about your design preferences
    ▼ Phase 0B: Creates missing design infrastructure (theme.ts, etc.)
    ▼ Phase 0C: Sets up screenshot capture (Maestro / Playwright)
    ▼ Phase 1:  Captures every screen — light mode + dark mode
    ▼ Phase 2:  Analyzes against your design system (25+ checks)
    ▼            ── STOPS for your review ──
    ▼ Phase 3:  Proposes 1-3 fix options per issue
    ▼            ── STOPS for your approval ──
    ▼ Phase 4:  Implements approved fixes
    ▼ Phase 5:  Retakes screenshots to verify fixes landed
    ▼ Phase 6:  Tracks regression across audit runs
    ▼ Phase 7+: Optional deep checks (screen sizes, accessibility, etc.)
```

### The Interview (Phase 0A)

Before auditing, the skill asks you:

1. **Design references** — What apps do you consider best-in-class? (Airbnb, Arc, Linear, etc.)
2. **Priority ranking** — Consistency, accessibility, premium feel, density, or simplicity?
3. **Known pain points** — Any screens that already bother you?
4. **Off-limits** — Anything you don't want changed?
5. **Scope** — Full app or specific screens?
6. **Implementation preference** — Just report, propose options, or full fix?

Your answers are saved so future runs don't re-ask.

### Auto-Setup (Phase 0B-0C)

The skill handles setup automatically:

- **No theme file?** Creates one by extracting colors, spacing, and fonts from your existing components
- **No CLAUDE.md design rules?** Generates them from your codebase patterns
- **No Maestro?** Installs it (plus Java if needed)
- **No Playwright?** Installs it
- **No capture flows?** Generates them by scanning your route structure and testIDs
- **No simulator running?** Tells you exactly what to run

### The Analysis (Phase 2)

Every screenshot is checked against a 25+ point checklist:

| Category | What's Checked |
|----------|---------------|
| **Consistency** | Spacing tokens, button radius, typography hierarchy, back button style, card treatment |
| **Accessibility** | WCAG AA contrast, 44pt touch targets, error state colors, interactive affordance |
| **Dark Mode** | No light-mode color leaks, border visibility, card/background contrast, badge opacity |
| **Layout** | FAB overlap, safe areas, scroll padding, modal bleed-through, element stacking |
| **Data States** | Image placeholders, loading skeletons, empty states, validation timing, error messages |
| **Visual Quality** | Duplicate actions, visual hierarchy, information density, color semantics |

Output is a severity-grouped table:
- **CRITICAL** — broken functionality or accessibility violations
- **MAJOR** — visible to users, degrades trust
- **MINOR** — polish items
- **NICE-TO-HAVE** — subjective improvements

### Fix Proposals (Phase 3)

For each issue, you get 1-3 options:

- **Conservative** — fix the specific bug, nothing else
- **Moderate** — fix the bug + improve surrounding context
- **Redesign** — rethink the component entirely

Each option includes: exact files to change, token values, effort estimate, and cascade impact.

If you have Figma connected, the skill can generate mockups for proposed changes.

### Implementation (Phase 4)

After you approve, the skill:
1. Makes surgical code changes
2. Runs typecheck — zero new errors
3. Runs tests — no regressions
4. Retakes screenshots to verify the fix actually landed

---

## Supported Platforms

| Platform | Capture Tool | Viewports | Auto-Detected |
|----------|-------------|-----------|---------------|
| React Native / Expo | Maestro | iPhone SE (375pt), iPhone Pro (393pt) | ✅ |
| Flutter | Maestro | Same as above | ✅ |
| Next.js / React | Playwright | Mobile (375px), Tablet (768px), Desktop (1440px) | ✅ |
| Vue / Nuxt | Playwright | Same as above | ✅ |
| Svelte / SvelteKit | Playwright | Same as above | ✅ |
| Static HTML/CSS | Playwright | Same as above | ✅ |
| Monorepo (mobile + web) | Both | All viewports | ✅ |

The skill auto-detects your platform. If both mobile and web exist, it asks which to audit.

If a platform's code exists but isn't running, it tells you exactly what command to run to start it.

---

## Optional Extended Checks

Beyond the core visual audit, you can enable:

| Check | What It Does |
|-------|-------------|
| **Screen size variants** | Captures on SE/Pro/iPad to catch responsive issues |
| **Edge case data** | Tests long names, empty states, overflow text, missing images |
| **Copy/microcopy audit** | Finds "Sign in" vs "Log in" inconsistencies, tone mismatches |
| **Accessibility deep dive** | Color blindness checks, dynamic type scaling, VoiceOver labels |
| **Component drift detection** | Measures design system adoption (% of styles using tokens vs hardcoded) |
| **CI integration** | Generates GitHub Actions workflow for visual regression on PRs |
| **Regression tracking** | Compares audit results across runs — "12 fixed, 3 new issues" |

---

## Autonomous Mode

For CI/CD pipelines, cron jobs, or `--dangerously-skip-permissions`:

- Uses stored preferences (or safe defaults)
- Runs capture → analyze → propose without stopping
- **Only auto-fixes** if ALL of:
  - User previously opted "full autopilot"
  - Issue is CRITICAL or MAJOR
  - Confidence is 95%+
  - Fix is conservative (not a redesign)
  - Fix passes typecheck + tests
- Everything else → generates `maestro/screenshots/audit-report.md` for human review

---

## Installation Options

### Option 1: Git clone (recommended)

```bash
git clone https://github.com/christoferchan/design-audit-skill.git ~/.claude/skills/design-audit
```

### Option 2: Manual download

1. Download `skills/design-audit/SKILL.md` from this repo
2. Create the directory: `mkdir -p ~/.claude/skills/design-audit`
3. Place the file: `~/.claude/skills/design-audit/SKILL.md`

### Option 3: Project-level install

If you want the skill available only for a specific project:

```bash
mkdir -p .claude/skills/design-audit
cp ~/.claude/skills/design-audit/SKILL.md .claude/skills/design-audit/SKILL.md
```

### Verify Installation

Open Claude Code and type:
```
/skills
```

You should see `design-audit` in the list.

---

## Requirements

| Requirement | Needed For | Auto-Installed? |
|-------------|-----------|-----------------|
| Claude Code | Everything | No — [install here](https://claude.ai/code) |
| Node.js | Everything | No |
| Maestro | Mobile screenshot capture | ✅ Yes |
| Java (Zulu JDK 17) | Maestro runtime | ✅ Yes (via `brew install --cask zulu@17`) |
| iOS Simulator | Mobile screenshots | No — requires Xcode |
| Android Emulator | Android screenshots | No — requires Android Studio |
| Playwright | Web screenshot capture | ✅ Yes (via `npm install -D @playwright/test`) |
| Figma MCP (optional) | Generating design mockups | No — [setup guide](https://developers.figma.com/docs/figma-mcp-server/) |

---

## FAQ

**Q: Does it modify my code?**
Only if you explicitly approve. The skill stops twice — once after analysis, once after proposals — and waits for your go-ahead. In default interactive mode, nothing changes without your say-so.

**Q: Does it work without a design system / theme file?**
Yes. If your project doesn't have one, the skill creates a theme file by extracting existing colors, fonts, and spacing from your components. This becomes your design system baseline.

**Q: Can I use it on an app I didn't build?**
Yes. It works with any React Native, Expo, Next.js, React, Vue, or Svelte project. It reads the codebase to understand the patterns.

**Q: What if Maestro screenshots don't scroll?**
This is a known Maestro limitation with some scroll implementations. The skill detects identical frames and flags them as "Maestro capture issue, not app bug." Those screens need manual verification.

**Q: How long does a full audit take?**
- Capture: 3-5 minutes (100+ screenshots)
- Analysis: 2-3 minutes (reading all screenshots)
- Proposals: 1-2 minutes per issue
- Implementation: depends on scope

**Q: Can I run it in CI?**
Yes. The skill can generate a GitHub Actions workflow that captures screenshots on every PR and diffs against a baseline. See the CI Integration section in the skill.

---

## Example: Real Audit Output

This skill was built during the development of [Spots](https://usespots.com), a travel discovery app. In one session it:

- Captured 153 screenshots (light + dark mode)
- Found 33 issues across 4 severity levels
- Fixed 25 issues including:
  - Premature form validation
  - Dark mode color tokens leaking light-mode values
  - Profile stats labels truncated
  - FAB overlapping list content
  - Paywall price using interactive color for non-interactive text
  - 6 dark mode contrast issues
- Tracked regression across 3 audit runs

---

## Credits

Created by [Christofer Chan](https://github.com/christoferchan) at [Odd Hours](https://oddhours.ai).

Built while developing [Spots](https://usespots.com) — a mobile-first travel discovery and trip-planning app.

## License

MIT — use it, modify it, share it.
