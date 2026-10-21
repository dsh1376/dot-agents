---
name: claude-design-system
description: >-
  Build or restyle chat and AI-assistant UI in the Claude desktop app's visual language (CDS): clay accent, warm four-level surfaces, Inter type, Phosphor icons, 20px dual-ring composer, message bubbles, thinking accordion, streaming grapheme reveal, spring presets. Use when a UI should look like Claude or uses these tokens. frontend-skill owns general page composition.
---

# Claude Design System (CDS)

CDS is a calm, warm interface language for conversational products. One clay accent sits on soft off-white or near-black surfaces. Depth comes from small lightness steps and faint rings, not borders. Motion is short and decisive, with light overshoot on entries.

## Source and trust

- Source: the CDS Storybook at https://claude-storybook.bystanderss.chatgpt.site/, snapshot 2026-10-17, one HTML file, sha256 `c4f1223b6cc3dd44e82613458fa8789788d378dc4c1f90c1489a6401304af748`.
- The Storybook is third-party. It says it extracted its tokens from Claude.app. This skill copies the Storybook faithfully but does not verify it against Claude.app.
- Second source: computed styles on https://claude.com/product/claude-code, Anthropic's own site, read 2026-10-19. It is first-party, so it wins for buttons, text color, and type.
- Every value in the references is a literal from one of these sources unless it is marked as a skill recommendation or a computed check.
- The spark glyph is Anthropic's logo, and "Claude" is Anthropic's product name. Use your own mark and name unless the work is for Anthropic.

## Workflow

1. **Install tokens.** Copy [assets/cds-tokens.css](assets/cds-tokens.css) into the project and import it once. For Tailwind, check the installed major version and add the matching theme from [references/foundations.md](references/foundations.md#tailwind-setup). On v4, rename recipe classes as listed there. Load Inter as a variable font.
2. **Assign surfaces.** Give every region one claude.com surface role: page, secondary (tiles, cards), tertiary (tracks, inputs), or overlay (menus). Separate regions by surface step before you add a ring or border. Use the Storybook levels 0 to 3 only to match its chat screens.
3. **Build components.** Use the recipes in [references/components.md](references/components.md) and the `.cds-btn` classes for buttons. Apply its copy checklist and each accessibility fix listed with a recipe.
4. **Add motion.** Use only the duration and easing tokens and the keyframes in [references/motion.md](references/motion.md). Use a spring preset for direct manipulation.
5. **Check dark mode.** Toggle `.dark` on `<html>`. Confirm surfaces, text weights (460 and 560), and shadows switch.
6. **Verify.** Render the result in light and dark mode and check it against the completion criteria below.

## Core rules

These apply on every screen. The references hold the full values.

- **One accent.** Clay `#d97757` marks the logo, text selection (solid, white text), the thinking state, the send button, active indicators, and selected tints. Use it sparingly. Clay hover and brand buttons use `#c6613f`. Everything else is warm gray. Small clay text and white-on-clay labels fail WCAG AA; the AA fix is `#b65030` in light mode.
- **Buttons.** Primary is near-black `#141413` with a `#faf9f5` label (inverted in dark mode); secondary is `#e8e6dc` with a 1px `#d1cfc5` ring. Radius 8px, heights 28px, 36px, or 40px, weight 400. Hover grows a ring; focus is a 2px `#c46849` outline at 4px offset.
- **Text color.** Primary `#141413`, secondary `#30302e`, tertiary `#5e5d59` on page `#faf9f5`. Dark: `#faf9f5`, `#b0aea5`, `#87867f` on `#141413`. Override Tailwind `neutral-*` with the claude.com grays.
- **Surfaces.** claude.com: page `#faf9f5`, secondary `#f5f4ed`, tertiary `#f0eee6`, overlay `#ffffff`. Dark: `#141413`, `#1a1918`, `#262624`, `#1a1918`. Hairlines `#e8e6dc`, rings `#d1cfc5`. Only overlays cast a shadow. The Storybook ladder (`#f5f5f3`, `#fcfcfb`, white; dark `#0b0b0a` to `#20201e`) is unverified.
- **Radius.** About 4.7px, 5.6px, 7.5px, 9.4px, and 13.2px for tags through modals; the desktop composer is fixed at 20px; pills and avatars are 999px.
- **Heights.** Controls are 24px, 28px, 32px (default), or 40px. The title bar is 46px to 48px.
- **Type.** Inter for UI, JetBrains Mono for code, ids, and numeric readouts, and a serif (Newsreader, then Georgia) for page headings. Reading text is 15px; UI labels are 12px; meta text is 11px or 10px. In dark mode, drop weight 500 to 460 and 600 to 560.
- **Icons.** Phosphor regular, `currentColor`, sizes 12px, 14px, 16px, 20px, 24px. Shift inline icons by `vertical-align: -0.125em`.
- **Text.** Balance headings, use `text-wrap: pretty` on paragraphs, use `overflow-wrap: anywhere` for ids and hashes, and use `tabular-nums` for changing numbers.
- **Motion.** State transitions use 60ms, 120ms, 200ms, 300ms, or 450ms with the `out`, `snap`, or `overshoot` easing. Message entries use their own keyframes (0.45s user bubble, 1.1s assistant text).

## Known source defects

Do not copy these. Each reference gives the fix next to the affected item.

| Defect | Where to read |
| --- | --- |
| `shadow-xs` renders nothing in Tailwind v3 (29 uses) | foundations.md, Shadows |
| Radius class labels do not match the px values; component cards render 12px | foundations.md, Radius |
| Clay text and white-on-clay labels fail WCAG AA for small text | foundations.md, Color |
| Several inline icon paths are malformed (solid gear, broken cpu) | foundations.md, Icons |
| `vertical-align` icon offset does nothing inside flex rows | foundations.md, Typography |
| Collapsed thinking accordion keeps a 21px strip | components.md, Thinking accordion |
| Components use cool stock `neutral-*`, not the documented warm grays | foundations.md, Site grays |
| Surface ladder, selection at `clay/30`, and stock status colors differ from claude.com | foundations.md, Color |
| Clay primary buttons, `#cc5a36` hover, and no focus style differ from claude.com | components.md, Buttons |
| No dark composer shadow | foundations.md, Shadows |
| No `aria-*`, `role`, `tabIndex`, or `focus-visible` style anywhere | components.md, each recipe |
| Composer does not auto-resize | components.md, Chat composer |
| Copy check fade never shows (reset at 2500ms) | components.md, Code block |
| Segmented thumb uses 150ms, token says 120ms | components.md, Segmented control |
| Grapheme split by code point, not grapheme | motion.md, Streaming grapheme reveal |
| Spring overshoot and settle labels disagree with the physics | motion.md, Spring presets |
| Five keyframes have no timing | motion.md, Keyframes |
| No reduced-motion handling | motion.md, Reduced motion |

## References

| File | Read when |
| --- | --- |
| [references/foundations.md](references/foundations.md) | Choosing any color, text color, surface, radius, height, shadow, type style, or icon; setting up Tailwind or fonts |
| [references/components.md](references/components.md) | Building buttons (claude.com spec), the composer, messages, thinking accordion, switches, segmented control, attachments, code block, cards, inputs, or app shell |
| [references/motion.md](references/motion.md) | Adding transitions, keyframes, streaming text, thinking dots, or springs |
| [assets/cds-tokens.css](assets/cds-tokens.css) | Starting a project; it holds every token, keyframe, and helper class |

## Completion criteria

- Every color, radius, height, shadow, duration, and easing traces to a token or a listed literal.
- Clay is the only saturated color, except error `#bf4d43`, the switch on-state `#2c84db`, and illustration colors.
- The report states three choices: `shadow-xs` omitted or replaced, stock or ladder radii, and whether small clay text meets AA.
- Light and dark mode both render, checked by a screenshot of each.
- Every interactive element is a native control or has a role, an accessible name, and a visible focus style.
- Changing numbers use `tabular-nums`, and long ids wrap without overflow at a 390px width.
- `prefers-reduced-motion: reduce` removes looping and spring motion.
