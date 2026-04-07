# Design Audit Skill for Claude Code

An end-to-end visual design audit pipeline for Claude Code. Captures screenshots, analyzes every screen against your design system, proposes fixes, and implements approved changes.

Works with **mobile** (React Native, Expo, Flutter) and **web** (Next.js, React, Vue, Svelte) apps.

## What It Does

```
/design-audit
    |
    v
Interview → Setup → Capture → Analyze → Propose → Implement → Verify
```

1. **Interviews you** about design preferences, priorities, and known pain points
2. **Sets up capture tools** (Maestro for mobile, Playwright for web) — installs them if missing
3. **Creates missing design infrastructure** (theme files, design tokens, CLAUDE.md rules) if your project doesn't have them
4. **Captures every screen** — generates automation flows from your route structure
5. **Analyzes screenshots** against your design system with a 25+ point checklist covering consistency, accessibility, dark mode, layout, and visual quality
6. **Proposes fixes** with 1-3 options per issue (conservative, moderate, redesign)
7. **Implements approved fixes** — writes code, verifies with typecheck + tests
8. **Retakes screenshots** to confirm fixes landed
9. **Tracks regression** across audit runs

### Optional Extended Checks
- Screen size variants (SE vs Pro, mobile vs tablet vs desktop)
- Edge case data testing (long names, empty states, overflow)
- Copy/microcopy consistency audit
- Accessibility deep dive (color blindness, dynamic type, VoiceOver)
- Component drift detection (design system adoption score)
- CI integration (GitHub Actions for visual regression on PRs)

## Install

### Option 1: Clone to skills directory
```bash
git clone https://github.com/christoferchan/design-audit-skill.git ~/.claude/skills/design-audit
```

### Option 2: Manual copy
Copy `skills/design-audit/SKILL.md` to `~/.claude/skills/design-audit/SKILL.md`

## Usage

In Claude Code, type:
```
/design-audit
```

Or say: "audit my app's design" / "review the UI" / "take screenshots and find issues"

### Autonomous Mode

For CI/cron jobs or when running with `--dangerously-skip-permissions`:
- Uses stored preferences from last interactive run
- Captures + analyzes + proposes without stopping
- Only auto-fixes clear bugs (95%+ confidence, passes tests)
- Generates a report file for human review

## Requirements

- **Claude Code** with multimodal support (image reading)
- **For mobile**: Maestro + Java + iOS Simulator or Android Emulator
- **For web**: Playwright + Node.js
- **For Figma mockups** (optional): Figma MCP server connected

The skill auto-installs Maestro and Playwright if missing.

## What Gets Created

If your project doesn't have design infrastructure, the skill creates:
- `src/constants/theme.ts` — design tokens (colors, spacing, radius, typography)
- Design system rules in `CLAUDE.md`
- Maestro capture flows in `maestro/flows/design/`
- Playwright capture scripts in `tests/visual-capture.ts`

## Example Output

```
CRITICAL
| # | Issue                          | Screen | Confidence |
|---|--------------------------------|--------|------------|
| 1 | Validation error shows before  | 054    | 98%        |
|   | user submits form              |        |            |

MAJOR
| # | Issue                          | Screen | Confidence |
|---|--------------------------------|--------|------------|
| 2 | Dark mode uses light-mode      | 095    | 95%        |
|   | warning color token            |        |            |
| 3 | Profile stats labels truncated | 017    | 99%        |
```

## Credits

Created by [Christofer Chan](https://github.com/christoferchan) — built while developing [Spots](https://usespots.com), a travel discovery app.

## License

MIT
