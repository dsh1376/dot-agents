# CDS foundations

Read this for color, surfaces, radius, heights, shadows, typography, and icons. [SKILL.md](../SKILL.md) owns the workflow. [assets/cds-tokens.css](../assets/cds-tokens.css) implements these values as CSS custom properties.

Values are exact literals from the source. "Source" means the CDS Storybook snapshot described in SKILL.md. Line numbers (`L123`) point into that snapshot.

## Color

### Clay brand palette

The source calls clay "a humanist palette that replaces saturated tech blue" (L342).

| Token | Hex | Role (source label) | Use |
| --- | --- | --- | --- |
| Clay | `#d97757` | Main brand clay | Thinking dots, emphasis buttons, brand icon |
| Clay Emphasized | `#cc5a36` | Dark clay | Button hover, stronger pressed state |
| Clay Light | `#fbf3ef` | Light clay fill | Light selected-state background |

Tailwind names from the source config (L23-27): `clay` = `#d97757`, `clay-hover` = `#cc5a36`, `clay-light` = `#fbf3ef`.

Clay at reduced opacity carries most tinted states:

| Use | Class |
| --- | --- |
| Text selection | `selection:bg-[#d97757]/30 selection:text-white` |
| Badge and pill fill | `bg-clay/10 text-clay` |
| Selected card fill | `bg-clay/5`, dark `dark:bg-clay/10` |
| Overshoot zone fill | `bg-[#d97757]/5` with `border-clay/20` |
| Height demo fill | `bg-clay/15 border border-clay` |
| Toggle "on" border | `border-clay/30` (pill), `border-clay/40` (duotone toggle, radius tiles) |
| Hover border on tiles | `hover:border-clay/60` |
| Dashed target line | `border-[#d97757]/60` |
| Draggable card border | `border-2 border-clay/80` |
| Faint caption | `text-clay/70` |
| Range inputs | `accent-clay` or `accent-[#d97757]` |

Contrast notes (computed with the WCAG formula, not stated in the source):

| Pair | Ratio | Source use |
| --- | --- | --- |
| White on `#d97757` | 3.12:1 | Primary button labels at 12px and 14px |
| White on `#cc5a36` | 4.13:1 | Primary hover |
| `#d97757` text on white | 3.12:1 | 12px hex labels, eyebrows, inline code |
| `#d97757` text on `neutral-100` | 2.86:1 | Code value pills |
| `#d97757` text on `clay/10` over white | 2.74:1 | "Verified" badge |
| White text on `clay/30` over white | 1.37:1 | Text selection |

All of these fail WCAG AA (4.5:1) for small text. Clay text and clay buttons pass the 3:1 large-text threshold only. When AA matters, use `#cc5a36` or darker for clay text, use 14px semibold or larger on clay fills, and keep selected text dark (for example `selection:text-neutral-900`). These fixes are skill recommendations.

### Surface ladder

The source states the rule: avoid harsh 1px pure black borders and build depth from four soft lightness levels (L321).

| Level | Light | Dark | Role |
| --- | --- | --- | --- |
| Surface 0 | `#f5f5f3` | `#0b0b0a` | Base layer and sidebar; borderless shell |
| Surface 1 | `#fcfcfb` | `#151515` | Main chat canvas; very soft |
| Surface 2 | `#ffffff` | `#1a1a19` | Cards, panels, the area around message bubbles |
| Surface 3 | `#ffffff` | `#20201e` | Composer body, popover bubbles; the top layer |

Tailwind names from the source config (L28-37): `surface-light0` to `surface-light3` and `surface-dark0` to `surface-dark3`.

Notes from the source:

- Surface 2 and Surface 3 are both `#ffffff` in light mode. In light mode, only shadow and border separate a card from the composer.
- No `surface-*` utility is used anywhere. The components use `bg-white dark:bg-neutral-900` (`#171717`, not `#1a1a19`) for cards.
- The Storybook sidebar uses `#fcfcfb` (Surface 1), although the ladder assigns the sidebar to Surface 0 (`#f5f5f3`). Follow the ladder.
- `clay-light` (`#fbf3ef`) is configured but never used.

### Warm neutral grays

| Step | Hex |
| --- | --- |
| Gray 0 | `#ffffff` |
| Gray 10 | `#fcfcfb` |
| Gray 20 | `#f5f5f3` |
| Gray 100 | `#dedcd6` |
| Gray 300 | `#a4a19b` |
| Gray 500 | `#6b6863` |
| Gray 700 | `#373634` |
| Gray 800 | `#20201e` |
| Gray 850 | `#151515` |
| Gray 900 | `#0b0b0a` |

Six steps repeat surface values (Gray 0, 10, 20, 800, 850, 900). The ladder has no 200, 400, or 600 step, and Surface 2 dark (`#1a1a19`) has no gray step.

Gap in the source: the components never use this ladder. They use Tailwind's stock `neutral-*` scale (most used: `neutral-800`, `neutral-200`, `neutral-900`, `neutral-100`, `neutral-400`, `neutral-500`). Stock neutral is a pure gray, so the rendered UI is cooler than the documented warm ladder. To get the warm look, map `neutral-*` to these steps. That mapping is a skill recommendation, not source data.

### Text and border colors in use

| Role | Light | Dark |
| --- | --- | --- |
| Primary text | `text-neutral-900` | `text-neutral-100` |
| Assistant body text | `text-neutral-800` | `text-neutral-200` |
| Secondary text | `text-neutral-600` or `-700` | `text-neutral-400` or `-300` |
| Muted text, captions | `text-neutral-500` | `text-neutral-500` or `-400` |
| Faint labels, placeholders | `text-neutral-400` | `text-neutral-500` |
| Default border | `border-neutral-200` | `border-neutral-800` |
| Soft border | `border-neutral-200/60`, `/80`, `/90`, `border-neutral-100` | `border-neutral-800/60` |
| Input border | `border-neutral-300` | `border-neutral-700` |
| Swatch outline | `border-black/10` | `border-white/10` |

Contrast notes (computed): `neutral-400` (`#a3a3a3`) on white is 2.52:1, on `#fcfcfb` 2.46:1, and on `#f0f0ee` 2.21:1. The source uses it for captions, placeholders, and breadcrumbs. Keep it for non-essential text only. In dark mode, `text-neutral-500` without a `dark:` variant gives 3.78:1 on `#171717`; give muted text a dark variant such as `dark:text-neutral-400`.

### Status colors

The source has no status palette. It uses stock Tailwind colors:

| Meaning | Class |
| --- | --- |
| Success, "copied" (light UI) | `text-emerald-500` |
| Success in the dark code block | `text-green-400` |
| Destructive hover (trash, remove) | `hover:text-red-500` |
| Wrong state in demos | `text-red-500` |
| Warning, drift in demos | `text-amber-500` |

### Other literal colors

| Hex | Where |
| --- | --- |
| `#f0f4f8` | User message bubble (light); dark uses `bg-white/10` |
| `#f8f9fa` | Storybook body background (light) |
| `#0f0f0f` | Storybook body and canvas stage (dark) |
| `#121212` | App wrapper (dark) |
| `#eef1f5` | Canvas stage (light) |
| `#cbd5e1` | Canvas dot grid (light), 1.2px dots |
| `#262626` | Canvas dot grid (dark), 1px dots |
| `#181818` | Sidebar (dark) |
| `#1e1e1e` | Top toolbar (dark) |
| `#202020` | Nav item hover (dark) |
| `#242424` | Search input (dark) |
| `#282828` | Toolbar segmented track and select (dark) |
| `#2a2a2a` | Active nav item (dark) |
| `#383838` | Active toolbar segment (dark) |
| `#f0f0ee` | Search input (light) |

## Radius

The source states one scale factor for radii: `--cds-rem-scale: calc(16/17)`, about 0.941 (L387). Each step shows the scaled value and the unscaled endpoint.

| Step | Rendered | Range in source | Source class label | Use |
| --- | --- | --- | --- | --- |
| XS | 4.7px | 4.7px to 5px | `rounded` | Tags, checkboxes, small keys |
| SM | 5.6px | 5.6px to 6px | `rounded-md` | Small buttons, kbd badges |
| MD | 7.5px | 7.5px to 8px | `rounded-lg` | Standard buttons, dropdown items |
| LG | 9.4px | 9.4px to 10px | `rounded-xl` | Accordion cards, popovers, attachment tiles |
| XL | 13.2px | 13.2px to 14px | `rounded-2xl` | Mobile composer, standalone modals |
| Composer | 20px | fixed 20px | `rounded-[20px]` | Desktop composer shell |
| Full | 999px | 999px | `rounded-full` | Segmented pill, avatars, status badges |

Source defects:

- The class labels do not produce the ladder values. The source config has no `borderRadius` extension, so stock Tailwind v3 renders `rounded` at 4px, `rounded-md` at 6px, `rounded-lg` at 8px, `rounded-xl` at 12px, `rounded-2xl` at 16px, and `rounded-full` at 9999px. Runtime check on 2026-10-17: the thinking card (`rounded-xl`) renders 12px.
- Only the radius demo tiles render the ladder values. Every component in the source uses the stock classes, so its cards render 12px, not 9.4px.
- `--cds-rem-scale` is display text only; the source never declares it.
- The labels mix conventions: XS and SM show scaled values, MD, LG, and XL show unscaled values.
- Composer (20px) and Full (999px) are not scaled, which contradicts "uniform scale factor".

To make the recipe classes render the documented ladder, add the `borderRadius` mapping in [Tailwind setup](#tailwind-setup). Without it, the recipes reproduce the Storybook's rendered look (stock radii). Choose one per project and say which.

Other radii in use: user bubble `rounded-[18px]`, which is off the ladder between XL and Composer; Storybook canvas card `rounded-2xl`.

## Heights

The source states that control heights snap to this ladder so rows and grids stay aligned (L420).

| Step | Height | Class | Use |
| --- | --- | --- | --- |
| XS | 20px to 24px | `h-6` | Inline actions, tag close buttons |
| SM | 24px to 28px | `h-7` | Dense toolbar buttons |
| MD (default) | 32px (2rem) | `h-8` | Send button, standard buttons, tab items |
| LG | 40px (2.5rem) | `h-10` | Sign in, sign up, primary call to action |
| Titlebar | 46px to 48px | `h-12` | macOS frameless window drag region |

Other fixed heights: segmented control 36px (`h-9`); composer textarea minimum 52px (`min-h-[3.25rem]`) and maximum 384px (`max-h-[24rem]`); Storybook top toolbar 48px (`h-12`).

## Shadows

### Composer dual ring

At rest the composer floats softly. On focus the outer ring and inner ring both darken (L446-457).

| State | `box-shadow` |
| --- | --- |
| Rest | `0 0.25rem 1.25rem rgba(0,0,0,0.035), inset 0 0 0 1px rgba(0,0,0,0.08)` |
| Focus-within | `0 0.25rem 1.25rem rgba(0,0,0,0.075), inset 0 0 0 1px rgba(0,0,0,0.16), 0 0 0 1px rgba(0,0,0,0.16)` |

The rest caption in the source writes the value as `0 0.25rem 1.25rem rgba(0,0,0,3.5%), inset 0 0 0 1px var(--cds-border)`. The source never defines `--cds-border`; the rendered value is `rgba(0,0,0,0.08)`.

Gap in the source: there is no dark composer shadow. The same black rings render on `#20201e`, where they are nearly invisible. Runtime check on 2026-10-17 confirmed the light value in dark mode.

### Elevation ladder

| Level | Light | Dark | Use |
| --- | --- | --- | --- |
| SM | `0 1px 2px 0 rgba(0,0,0,0.05), 0 2px 8px 0 rgba(0,0,0,0.04)` | `0 1px 2px 0 rgba(0,0,0,0.3)` | Badges, small button hover |
| MD | `0 2px 4px 0 rgba(0,0,0,0.06), 0 6px 16px 0 rgba(0,0,0,0.07)` | `0 4px 6px -1px rgba(0,0,0,0.4)` | Standalone cards, thinking block |
| Popover | `0 8px 24px rgba(0,0,0,0.12), 0 2px 6px rgba(0,0,0,0.08)` | `0 8px 24px rgba(0,0,0,0.5)` | Dropdown menus, slash command panel |

Segmented thumb: `inset 0 0 0 1px rgba(0,0,0,0.06), 0 1px 2px 0 rgba(0,0,0,0.08)`. It is an inline style, so it overrides the thumb's `shadow-sm` class. It has no dark variant.

Ladder notes from the source:

- Dark variants use one layer; light variants use two. Dark MD (`0 4px 6px -1px`) has a different geometry from light MD.
- SM and MD list the small layer first; Popover lists the large layer first.
- The ladder assigns MD to the thinking block, but the thinking card in the source has no shadow.
- The surface sample cards use stock `shadow-sm` instead of the SM token.

Source defect: the source uses `shadow-xs` 29 times. Tailwind v3 has no `shadow-xs`, and the page loads Tailwind v3 from the CDN. A runtime check on 2026-10-17 showed `box-shadow: none` for these elements. Tailwind v4 does define `shadow-xs`. Decide per project: omit the shadow to match the rendered source, or use `--cds-shadow-sm`.

Stock Tailwind shadows also appear: `shadow-sm` (send button, sidebar logo), `shadow-md` (code block, runner chips), `shadow-lg` (spring press button), `shadow-xl` (canvas card light, draggable card), `shadow-2xl` (canvas card dark), `shadow-inner` (clay swatches), `shadow` (custom runner chip).

## Typography

### Families

| Role | Stack | Loaded weights |
| --- | --- | --- |
| Sans (all UI) | `Inter, system-ui, -apple-system, sans-serif` | 300, 400, 500, 600, 700 |
| Serif | `Newsreader, ui-serif, Georgia, serif` | optical size 6 to 72; 300 and 400 roman; 300 italic |
| Mono | `JetBrains Mono, ui-monospace, monospace` | 400, 500 |

Body defaults: `font-sans antialiased`; `text-neutral-900` on `#f8f9fa` (light), `text-neutral-100` on `#0f0f0f` (dark).

Gaps in the source:

- Newsreader is loaded and configured, but no element uses `font-serif`. The source gives no rule for serif use. Inter 300 is loaded but unused.
- JetBrains Mono loads only 400 and 500, but many labels use `font-mono font-bold`. The browser synthesizes the bold. Load 700, or use `font-medium` for mono labels.
- `font-[470]` and the 460/560 weights render correctly because the served Inter file is variable. Runtime check on 2026-10-17: one Inter file serves all weights.

The source text names "Anthropic-Sans" and "Anthropicons" as the real app's typeface and icon font. Inter and Phosphor are the Storybook's public substitutes.

### Type roles in use

| Role | Size and leading | Weight | Color (light / dark) |
| --- | --- | --- | --- |
| Assistant message body | `text-[15px] leading-[1.65]` | normal | `neutral-800` / `neutral-200` |
| Composer input | `text-[15px] leading-[1.4]` | normal | `neutral-900` / `neutral-100`; placeholder `neutral-400` |
| User bubble | `text-sm leading-relaxed` (14px, 1.625) | normal | `neutral-900` / `neutral-100` |
| Story and section title | `text-base` (16px) | semibold | `neutral-900` / `neutral-100` |
| Card title | `text-sm` | semibold | `neutral-900` / `neutral-100` |
| UI label, button, nav | `text-xs` (12px) | medium | varies by state |
| Byline ("Claude") | `text-xs` | medium | `neutral-400` |
| Eyebrow ("Constraint 01") | `text-xs font-mono uppercase tracking-wider` | bold | `text-clay` |
| Section label | `text-[11px] uppercase tracking-wider` | semibold | `neutral-400` |
| Caption, meta | `text-[11px]` | normal or medium | `neutral-400` or `-500` |
| Micro label | `text-[10px]`, often mono | normal to bold | `neutral-400` or `-500` |
| Chart axis | 9px to 10px monospace | normal | `currentColor` at 40% |
| Segmented label | `text-xs tracking-tight` | `font-[470]`, active semibold | see Segmented control |

`leading-relaxed` (1.625) is the default for multi-line UI text and code.

### Constraint 01: dark-mode weight compensation

Light text on a dark background looks 10% to 15% heavier (optical irradiation). The source compensates with the variable `wght` axis (L520-523):

| Text | Light mode | Dark mode |
| --- | --- | --- |
| Body medium | `"wght" 500` | `"wght" 460` |
| Heading semibold | `"wght" 600` | `"wght" 560` |

Apply it per text role with `font-weight: 460` and `560`. Do not set `font-variation-settings` on a wrapper: it overrides the `wght` axis for every child, so a `font-bold` child also renders at 460. The source demo has that defect. It needs a variable font. The source's Google Fonts request serves one variable Inter file for all weights (checked 2026-10-17), so the axis works. For your own setup, load Inter as a range (`Inter:wght@300..700`).

### Constraint 02: inline icon baseline

A default inline SVG sits on the baseline and rides 2px to 3px above the x-height. `vertical-align: middle` follows line-height and drifts. The source shifts icons down by 1/8 em so the icon center aligns with cap height and x-height (L592-595).

| Text size | Offset | Class |
| --- | --- | --- |
| 14px to 16px body and headings | `-0.125em` (about -2px at 16px) | `.align-neg-125` |
| 11px to 12px captions | `-0.075em` | `.align-neg-075` |
| Fixed pixel nudges | `-1px`, `-2px` | `.align-neg-1px`, `.align-neg-2px` |

Limits:

- `vertical-align` works only in inline layout. It has no effect on a flex item, so in a `flex items-center` row the offset does nothing. Use the offset for icons inside running text, and `items-center` for flex rows.
- The icon component already applies `-0.125em`. Do not add a second offset on a wrapper span.
- `-0.125em` is -2px only at 16px; at 14px it is -1.75px.
- The demo row labeled "11px caption" renders at `text-xs` (12px).

The source text names these as the app's Tailwind arbitrary-value classes `align-[-0.125em]` and `align-[-0.075em]`. The Storybook implements them as `.align-neg-125` and `.align-neg-075`; either form works. Every icon component in the source applies `.align-neg-125` by default.

### Constraint 03: balanced headings, pretty paragraphs

- Headings: `text-wrap: balance` (`.text-balance-native`). It evens line lengths so the last line never holds one or two lone words or CJK characters.
- Paragraphs: `text-wrap: pretty` (`.text-pretty-native`). It pulls the second-to-last word down so no word stands alone on the last line.

### Constraint 04: long identifiers

Use `overflow-wrap: anywhere; word-break: normal` (`.overflow-wrap-anywhere`) wherever model ids, hashes, or paths can appear. Normal words stay whole, and only an unbroken run longer than the line breaks. `word-break: break-all` splits ordinary words ("engi-neering"). `overflow-wrap: normal` lets a 60-character hash overflow the container. In the demo, the `break-all` mode also sets `overflow-wrap: break-word`. Its sample id is 71 characters and contains hyphens, which already allow breaks.

### Constraint 05: tabular numbers

Use `font-variant-numeric: tabular-nums` (`tabular-nums`) for thinking timers, token counters, tables, and any changing number. Digits 0 to 9 then share one width, and the layout does not jitter. The source demo updates every 40ms to show the difference. The rule matters for Inter, whose default digits are proportional. JetBrains Mono digits are already fixed width.

### Constraint 06: line-clamp ladder

| Lines | Use | Class |
| --- | --- | --- |
| 1 | Sidebar conversation titles | `truncate` |
| 2 | Thinking summary cards | `line-clamp-2` |
| 3 | System prompt cards | `line-clamp-3` |

The source says the clamped card expands to full text through a smooth grid-height transition. The demo toggles classes with no transition. To animate it, use the grid-rows pattern in [motion.md](motion.md). Note that `truncate` is single-line `white-space: nowrap`, not `line-clamp-1`.

## Icons

### Icon set and sizes

The source draws Phosphor regular icons (viewBox `0 0 256 256`, `fill="currentColor"`) through one component with five sizes:

| Size | px | Typical use |
| --- | --- | --- |
| `xs` | 12 | Composer toolbar, badges, bylines, code block header |
| `sm` | 14 | Buttons, send arrow, icon buttons |
| `md` (default) | 16 | Headings, standalone |
| `lg` | 20 | Large controls |
| `xl` | 24 | Hero or empty states |

A number passes through as px. Every icon gets `inline-block align-neg-125 shrink-0 transition-transform`.

The control panel caption (L868) states the real app's icon weight: native 533.3, active state 700, inline offset `-0.125em`, on a 256 x 256 grid.

Weights in the source:

- Regular: filled path, no stroke.
- Bold: the same path plus `stroke="currentColor" stroke-width="10"` with round caps and joins. This is a simulated bold. The Phosphor packages ship a real bold weight; prefer it.
- Duotone: an extra copy of the path behind it at `opacity="0.28"` with `transform="scale(0.96) translate(5, 5)"`. Phosphor also ships a real duotone weight.

### Icon catalog

"Exact" means the path data is identical to `@phosphor-icons/core@2.1.1` regular (checked 2026-10-17). "Look-alike" means the source path matched no checked Phosphor file; the Phosphor name given is the nearest equivalent by meaning.

| Source name | Label | Category | Phosphor name | Match |
| --- | --- | --- | --- | --- |
| `spark` | Brand spark | Brand | none (Anthropic logo, viewBox `0 0 100 100`) | not Phosphor |
| `chat` | Conversation | General | `chat` | look-alike |
| `fileCode` | Code file | Files | `file-code` | exact |
| `fileText` | Text document | Files | `file-text` | exact |
| `search` | Search | General | `magnifying-glass` | exact |
| `copy` | Copy | Action | `copy` | exact |
| `check` | Confirm | Action | `check` | exact |
| `plus` | Add | Action | `plus` | exact |
| `arrowRight` | Send | Action | `arrow-right` | exact |
| `chevronDown` | Dropdown | Action | `caret-down` | exact |
| `settings` | Settings | General | `gear` | look-alike (same start) |
| `refresh` | Retry, refresh | Action | `arrow-counter-clockwise` | look-alike |
| `trash` | Delete | Action | `trash` | exact |
| `stop` | Stop generating | Action | `stop` | look-alike |
| `info` | Hint badge | Status | `info` | exact |
| `terminal` | Console | Dev | `terminal-window` | look-alike |
| `branch` | Version branch | Dev | `git-branch` or `share-network` | look-alike |
| `cpu` | Compute core | Dev | `cpu` | look-alike |
| `layers` | Layer stack | Dev | `stack` | look-alike |
| `code` | Code mode | Dev | `code` | look-alike |
| `eye` | Preview | General | `eye` | look-alike (same start) |
| `pin` | Pin card | General | `push-pin` | look-alike |
| `user` | User profile | General | `user` | look-alike |

An unknown name falls back to `chat`; an unknown size string falls back to 16px.

Defects in the source icons. Use the official Phosphor package instead of the inline paths:

- `settings` renders as a solid gear with no hole, and its outline is off center.
- `cpu` has a broken outer arc and a tapered right wall.
- `refresh` has an uneven ring (27 units thick at the top, about 6 near the bottom).
- `code` has asymmetric brackets.
- The spark fills its whole 100 x 100 box, while Phosphor glyphs leave margin in 256 x 256. At the same size the spark looks about 20% to 25% larger.
- The duotone copy sits almost exactly under the main path, so only faint slivers show.
- The caption's "weight 533.3, active 700" is not implemented; the component has only the stroke-based bold toggle. The "256 x 256" badge does not apply to the spark (100 x 100).

The spark glyph is Anthropic's logo. Use it only in work for Anthropic or Claude. Use your own product mark elsewhere.

### Icon placement recipes

| Context | Recipe |
| --- | --- |
| Icon inside text | wrap or render with `.align-neg-125`; 12px captions use `.align-neg-075` |
| Icon plus label button | `flex items-center gap-1.5`, icon `sm` |
| Icon-only button | `size-8 rounded-lg flex items-center justify-center`, icon `sm` |
| Icon tile (catalog) | `size-11 rounded-lg bg-neutral-100 dark:bg-neutral-800/80`, hover `group-hover:text-clay group-hover:scale-110` |
| Destructive icon button hover | `hover:text-red-500` |
| Neutral icon button hover | `hover:text-clay` |

## Tailwind setup

The source configures Tailwind v3 like this (L19-49). It is runtime-verified on the source page:

```js
tailwind.config = {
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        clay: { DEFAULT: '#d97757', hover: '#cc5a36', light: '#fbf3ef' },
        surface: {
          light0: '#f5f5f3', light1: '#fcfcfb', light2: '#ffffff', light3: '#ffffff',
          dark0: '#0b0b0a', dark1: '#151515', dark2: '#1a1a19', dark3: '#20201e',
        },
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', '-apple-system', 'sans-serif'],
        serif: ['Newsreader', 'ui-serif', 'Georgia', 'serif'],
        mono: ['JetBrains Mono', 'ui-monospace', 'monospace'],
      },
    },
  },
}
```

The source loads the unversioned Tailwind Play CDN, which is a development tool. Pin a Tailwind version in real projects.

To make the radius class names render the documented ladder, add this to `theme.extend` (a skill recommendation; the source has no radius extension):

```js
borderRadius: {
  DEFAULT: 'calc(5px * 16 / 17)',   // rounded     -> 4.7px
  md: 'calc(6px * 16 / 17)',        // rounded-md  -> 5.6px
  lg: 'calc(8px * 16 / 17)',        // rounded-lg  -> 7.5px
  xl: 'calc(10px * 16 / 17)',       // rounded-xl  -> 9.4px
  '2xl': 'calc(14px * 16 / 17)',    // rounded-2xl -> 13.2px
  full: '999px',
},
```

For Tailwind v4, declare the same names in CSS. This mapping follows v4's `@theme` syntax; it was not run against a v4 build in this skill:

```css
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));
@theme {
  --color-clay: #d97757;
  --color-clay-hover: #cc5a36;
  --color-clay-light: #fbf3ef;
  --color-surface-light0: #f5f5f3;
  --color-surface-light1: #fcfcfb;
  --color-surface-light2: #ffffff;
  --color-surface-light3: #ffffff;
  --color-surface-dark0: #0b0b0a;
  --color-surface-dark1: #151515;
  --color-surface-dark2: #1a1a19;
  --color-surface-dark3: #20201e;
  --font-sans: "Inter", system-ui, -apple-system, sans-serif;
  --font-serif: "Newsreader", ui-serif, Georgia, serif;
  --font-mono: "JetBrains Mono", ui-monospace, monospace;
}
```

Font loading used by the source:

```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Newsreader:ital,opsz,wght@0,6..72,300;0,6..72,400;1,6..72,300&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
```
