# CDS components

Read this when you build a chat surface or a control. Each recipe gives anatomy, Tailwind classes from the source, states, motion, and known defects. [foundations.md](foundations.md) owns token values. [motion.md](motion.md) owns keyframes and springs.

Classes assume the Tailwind config in foundations.md (`clay`, `clay-hover`, `dark:` by class). Line numbers (`L123`) point into the source snapshot.

Accessibility gap: the source has no `aria-*`, `role`, or `tabIndex` attribute anywhere, and several clickable elements are `div` or `span`. Each recipe lists the fix. Treat those fixes as required.

## Chat composer

Purpose: the main message input. Radius is fixed at 20px; the shell has the dual-ring shadow and darkens on focus (L1015-1069).

Anatomy:

```text
wrapper            w-full max-w-[42rem]
  shell            relative flex flex-col w-full rounded-[20px] p-[0.875rem] gap-y-2 cursor-text
                   bg-white dark:bg-[#20201e] transition-all duration-200
    textarea       rows=1, placeholder "How can I help you today?"
    toolbar        flex items-center justify-between pt-1
      left group   flex items-center gap-1
        "Add content" button   (plus icon xs + label)
        model picker button    (label + chevronDown icon xs)
      right group  flex items-center gap-2
        send button            (arrowRight icon sm)
  hint             text-center text-xs text-neutral-400 mt-4
```

| Part | Classes and values |
| --- | --- |
| Shell shadow, rest | `0 0.25rem 1.25rem rgba(0,0,0,0.035), inset 0 0 0 1px rgba(0,0,0,0.08)` |
| Shell shadow, focus | `0 0.25rem 1.25rem rgba(0,0,0,0.075), inset 0 0 0 1px rgba(0,0,0,0.16), 0 0 0 1px rgba(0,0,0,0.16)` |
| Textarea | `w-full max-h-[24rem] min-h-[3.25rem] bg-transparent border-none outline-none resize-none text-[15px] leading-[1.4] text-neutral-900 dark:text-neutral-100 placeholder-neutral-400 pl-2 focus:ring-0` |
| Toolbar buttons | `h-8 px-2.5 rounded-lg flex items-center gap-1.5 text-xs font-medium text-neutral-500 hover:text-neutral-800 dark:hover:text-neutral-200 hover:bg-neutral-100 dark:hover:bg-neutral-800 transition-colors` |
| Send button | `size-8 rounded-lg flex items-center justify-center transition-all duration-150 bg-[#d97757] hover:bg-[#cc5a36] text-white shadow-sm disabled:opacity-20 disabled:cursor-not-allowed` |

States:

- Focus: the source switches the shadow on textarea focus and blur only, so tabbing to a toolbar button drops the focus shadow. Put the focus shadow on the shell's `:focus-within` instead. The "dual ring" exists only in focus; at rest there is one inset ring.
- Send disabled: while the trimmed text is empty (`disabled={!text.trim()}`), at 20% opacity.
- Dark: shell `#20201e` (surface 3). The shadow stays the light value; see the gap in foundations.md.

Behavior gaps:

- The source calls the composer an "adaptive input with a dual-ring focus breathing animation". The code has no resize logic and no breathing keyframe. The textarea only sets `rows=1` with min and max heights, and focus is a 200ms shadow transition. Grow the textarea to content between 52px and 384px: set `height` from `scrollHeight` on input, or use `field-sizing: content` where the browser supports it.
- The send button has no accessible name. Add `aria-label="Send message"`.
- The disabled send button still darkens on hover. Use `enabled:hover:bg-[#cc5a36]`.
- The shell has `cursor-text`, but a click on its padding does not focus the textarea. Focus the textarea on shell click.
- The source has no Enter-to-send handling and no `onClick` on any composer button.
- The model picker label in the source is a sample ("Claude 3.7 Sonnet").

## Message bubbles

Purpose: the conversation column. User turns are right-aligned bubbles; assistant turns are plain text with a byline (L1072-1105).

```text
column            max-w-2xl mx-auto space-y-6 py-4
  user row        flex flex-col items-end
    bubble        rounded-[18px] px-4 py-2.5 text-sm leading-relaxed max-w-[85%]
                  bg-[#f0f4f8] dark:bg-white/10 text-neutral-900 dark:text-neutral-100
  assistant row   flex flex-col items-start
    message       text-[15px] leading-[1.65] text-neutral-800 dark:text-neutral-200 max-w-full
      byline      flex items-center gap-1.5 mb-2 text-xs text-neutral-400 font-medium
        spark icon xs text-clay + "Claude"
      body
```

Motion:

| Element | Animation |
| --- | --- |
| User bubble enter | `code-user-bubble-enter 0.45s cubic-bezier(.34, 1.3, .64, 1)` |
| Assistant message enter | `message-fade-up 1.1s cubic-bezier(.34, 1.56, .64, 1)` |
| Streaming text | grapheme reveal; see motion.md |

The assistant turn has no bubble, border, or fill. Only the user turn is a bubble. The sample answer in the source restates the streaming spec: each character fades in from a 6px blur over 90ms with a 30ms stagger. The story itself animates the whole answer block with `message-fade-up` and does not use the grapheme reveal. Use the block fade for a complete message and the grapheme reveal for streamed text.

Give the thread `role="log"` (or `aria-live="polite"`) so screen readers announce new turns. The source has none.

## Thinking accordion

Purpose: shows model reasoning. While thinking it shows three clay dots; when done it collapses to a summary row (L1108-1166).

```text
card              border border-neutral-200/80 dark:border-neutral-800 rounded-xl overflow-hidden
                  bg-neutral-50/50 dark:bg-neutral-900/40
  header          flex items-center justify-between px-3.5 py-2.5 cursor-pointer select-none
    label group   flex items-center gap-2 text-xs text-neutral-600 dark:text-neutral-400 font-medium
      thinking:   dots (inline-flex items-center gap-[3.5px]) + "Thinking..." in text-clay font-medium
      done:       size-1.5 rounded-full bg-neutral-400 dot + "Thought for 3 seconds"
    chevron       chevronDown xs text-neutral-400 transition-transform duration-200, rotate-180 when open
  body wrapper    grid transition-[grid-template-rows] duration-200 ease-[cubic-bezier(.19,1,.22,1)]
                  gridTemplateRows: open or thinking ? "1fr" : "0fr"
    body          min-h-0 overflow-hidden px-3.5 pb-3 pt-2 space-y-1 text-xs leading-relaxed font-mono
                  text-neutral-600 dark:text-neutral-400 border-t border-neutral-100 dark:border-neutral-800/60
      lines       each starts with "> "
```

Thinking dots: three `size-[3px] rounded-full bg-[#d97757]` spans, each `animation: 1.9s cubic-bezier(.4, 0, .2, 1) infinite timeline-quiet-dot`, with delays `0s`, `0.15s`, `0.3s`.

States:

- Thinking: body forced open; header click does nothing; no chevron.
- Done, open: chevron rotated 180 degrees.
- Done, closed: grid rows `0fr`.

Fixes the source needs:

- The header is a clickable `div`. Make it a `button` with `aria-expanded` and `aria-controls`.
- Show the elapsed time with `tabular-nums` (Constraint 05). The source hard-codes "Thought for 3 seconds".
- The source puts `pt-2 pb-3` and `border-t` on the same `min-h-0` element that must shrink. Its border box cannot go below 21px, so the collapsed card keeps a 21px strip. Runtime check on 2026-10-17: the collapsed grid row measured 21px. Put `min-h-0 overflow-hidden` on a wrapper and the padding and border on an inner child.
- The header keeps `cursor-pointer` while thinking, when a click does nothing. Use `cursor-default` in that state.
- Dots 2 and 3 show solid during their delay because the animation has no fill mode. Use negative delays (`0s`, `-1.75s`, `-1.6s`) for the same phase offsets without the flash.
- The shadow ladder assigns MD to the thinking block, but the source card has no shadow.

## Buttons

Sizes (L1173-1179), all clay primary:

| Size | Height | Classes |
| --- | --- | --- |
| XS | 24px | `h-6 px-2 rounded-md text-xs font-medium` |
| SM | 28px | `h-7 px-2.5 rounded-lg text-xs font-medium` |
| MD (default) | 32px | `h-8 px-3.5 rounded-lg text-sm font-medium` |
| LG | 40px | `h-10 px-5 rounded-xl text-sm font-medium` |

Variants (L1182-1198), all at MD height:

| Variant | Classes |
| --- | --- |
| Primary | `h-8 px-4 rounded-lg bg-clay text-white text-sm font-medium hover:bg-clay-hover shadow-xs transition-all active:scale-95 flex items-center gap-1.5` (optional spark icon xs) |
| Secondary | `h-8 px-4 rounded-lg border border-neutral-200 dark:border-neutral-700 bg-white dark:bg-neutral-900 text-neutral-800 dark:text-neutral-200 text-sm font-medium hover:bg-neutral-50 dark:hover:bg-neutral-800 transition-all active:scale-95` |
| Ghost | `h-8 px-3 rounded-lg text-neutral-600 dark:text-neutral-400 text-sm font-medium hover:bg-neutral-100 dark:hover:bg-neutral-800 transition-all` |
| Icon ghost | `size-8 rounded-lg flex items-center justify-center text-neutral-500 hover:bg-neutral-100 dark:hover:bg-neutral-800 transition-all` (icon sm) |

More button patterns from other stories:

| Pattern | Classes |
| --- | --- |
| Primary, compact text | `h-8 px-3 rounded-lg bg-clay text-white text-xs font-medium hover:bg-clay-hover flex items-center gap-1.5 shadow-xs active:scale-95 transition-all` |
| Secondary with icon | `h-8 px-3 rounded-lg border border-neutral-200 dark:border-neutral-700 bg-white dark:bg-neutral-900 text-neutral-800 dark:text-neutral-200 text-xs font-medium hover:bg-neutral-50 dark:hover:bg-neutral-800 flex items-center gap-1.5` |
| Outlined icon button | `size-8 rounded-lg border border-neutral-200 dark:border-neutral-700 bg-white dark:bg-neutral-900 text-neutral-600 dark:text-neutral-400 flex items-center justify-center`, hover `hover:text-clay` or `hover:text-red-500` (destructive) |
| Outline text button | `h-8 px-3 rounded-lg border border-neutral-300 dark:border-neutral-700 text-xs text-neutral-500 hover:bg-neutral-100 dark:hover:bg-neutral-800` |
| Neutral filled small | `h-7 px-2.5 rounded-md bg-neutral-100 hover:bg-neutral-200 dark:bg-neutral-800 dark:hover:bg-neutral-700 text-xs font-medium text-neutral-700 dark:text-neutral-300` |
| Neutral filled toggle | `px-3 py-1.5 rounded-lg text-xs font-medium bg-neutral-200 dark:bg-neutral-800 hover:bg-neutral-300 dark:hover:bg-neutral-700 text-neutral-800 dark:text-neutral-200` |
| Clay small | `h-7 px-2.5 rounded-md bg-clay text-white text-xs font-medium hover:bg-clay-hover` |
| Hero press button | `px-6 py-3.5 rounded-xl bg-clay text-white text-sm font-medium hover:bg-clay-hover shadow-lg flex items-center gap-2` |
| Disabled | `disabled:opacity-50` (lab buttons) or `disabled:opacity-20 disabled:cursor-not-allowed` (send) |

Inconsistencies in the source, to settle per project:

- The MD size uses `px-3.5`; the Primary and Secondary variants at the same height use `px-4`; Ghost uses `px-3`.
- Only Primary and Secondary have `active:scale-95`. Ghost, icon-only, and the size ladder have no pressed state. Only Primary has `shadow-xs`.
- The icon-only ghost button has no dark text variant (`text-neutral-500` in both modes).
- Buttons use Tailwind's default 150ms transition, while the motion tokens give 60ms for button micro-scale.

Press feedback: `active:scale-95` on CSS buttons. For spring-driven presses, scale to 0.88 on press and spring back to 1 with the Button preset (motion.md).

Missing in the source: no `focus-visible` style on any button. Add a visible ring, for example `focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-clay/50 focus-visible:ring-offset-2`. That ring is a skill recommendation.

## Pill toggle and inline segmented switches

Toggle pill (L508-517):

| State | Classes |
| --- | --- |
| Base | `px-3 py-1 rounded-full text-xs font-medium border transition-all` |
| On | `bg-clay/10 border-clay/30 text-clay font-bold` |
| Off | `bg-neutral-100 dark:bg-neutral-800 border-neutral-300 dark:border-neutral-700 text-neutral-600 dark:text-neutral-400` |

Bordered toggle (duotone switch): `h-7 px-2.5 rounded-lg text-[11px] font-medium border`; on `bg-clay/10 border-clay/40 text-clay font-bold`; off `border-neutral-200 dark:border-neutral-700 text-neutral-500`.

Inline segmented switch (alignment mode, wrap mode, icon size, lab tabs):

| Part | Classes |
| --- | --- |
| Track | `flex items-center gap-1 p-1 rounded-lg bg-neutral-100 dark:bg-neutral-800 text-xs` |
| Item | `px-2.5 py-1 rounded transition-colors` (`px-2 py-0.5` and `text-[11px]` in dense toolbars; `px-3 py-1 rounded-md` in lab tabs) |
| Active item | `bg-white dark:bg-neutral-700 text-clay font-bold shadow-xs` (lab tabs: `text-neutral-900 dark:text-white font-semibold`) |
| Inactive item | `text-neutral-500`, optional `hover:text-neutral-800` |

## Segmented control

Purpose: top-level view switch (Chat, Projects, Artifacts, Settings) with a sliding pill thumb (L1205-1278).

| Part | Classes and values |
| --- | --- |
| Track | `relative h-9 p-[2px] rounded-full bg-neutral-200/80 dark:bg-neutral-800/90 flex items-stretch select-none`, `container-type: inline-size` |
| Thumb | `absolute top-[2px] bottom-[2px] rounded-full bg-white dark:bg-neutral-700 pointer-events-none transition-all duration-150 ease-[cubic-bezier(.32,.72,0,1)]` |
| Thumb position | `left: calc(2px + i * (100% - 4px) / n)`; `width: calc((100% - 4px) / n)` |
| Thumb shadow | `inset 0 0 0 1px rgba(0,0,0,0.06), 0 1px 2px 0 rgba(0,0,0,0.08)` |
| Item | `relative z-10 flex-1 h-full flex items-center justify-center gap-1.5 text-xs font-[470] tracking-tight rounded-full transition-colors duration-150 truncate` |
| Active item | `text-neutral-900 dark:text-white font-semibold`; icon `text-clay` |
| Inactive item | `text-neutral-500 hover:text-neutral-800 dark:hover:text-neutral-300`; icon `text-neutral-400` |
| Icons | xs: `chat`, `layers`, `fileCode`, `settings` |

Spec from the caption: radius 999px, track padding 2px, height 36px. The thumb geometry is pure arithmetic, so it fits the track exactly at any width.

Compact mode: below 254px of track width, inactive items hide their label and keep the icon; the active item keeps both. The caption names `@container (max-width: 254px)`, but the code checks the width in JavaScript. Implement it with a real container query on the track.

Defects to avoid:

- The thumb uses 150ms, while the motion token for segmented sliders is `--cds-dur-snap` (120ms). Pick one; the token is the documented value.
- Use `role="tablist"`, `role="tab"`, and `aria-selected`, with arrow-key navigation. The source has none. In compact mode, icon-only tabs need an `aria-label`.
- The caption's `max-width: 254px` matches at 254px; the code's `< 254` does not. Pick one boundary.
- The thumb's `shadow-sm` class is dead; the inline shadow overrides it.
- The active item carries both `font-[470]` and `font-semibold`. Set only one weight per state.

## Attachment tiles

Purpose: files queued in the composer. New tiles squeeze in horizontally (L1281-1326).

```text
row               flex items-center gap-3 overflow-x-auto py-2
  tile            w-32 h-24 rounded-xl border border-neutral-200 dark:border-neutral-800
                  bg-white dark:bg-neutral-900 p-2.5 flex flex-col justify-between flex-shrink-0 shadow-xs overflow-hidden
                  animation: file-thumbnail-grow-in 0.25s cubic-bezier(.165, .84, .44, 1)
    top row       flex justify-between items-start
      type badge  text-[10px] font-mono uppercase bg-neutral-100 dark:bg-neutral-800 px-1 py-0.5 rounded
                  text-neutral-500 flex items-center gap-1  (fileCode icon xs + "CODE")
      remove      "×" text-xs text-neutral-400 hover:text-red-500 cursor-pointer
    file name     text-xs font-medium text-neutral-800 dark:text-neutral-200 truncate
```

Tile size is 128 x 96px. The grow-in animates `max-width` from 0 to 8rem, which equals the tile width, so neighbors slide aside.

The source defines `file-thumbnail-upload-pulse` (opacity to 0.45 at 50%) for the uploading state but never applies it, and gives no duration. The remove control is a `span`. Make it a `button` with `aria-label="Remove <file name>"`.

Other source defects:

- New files are named `document-${files.length + 1}.ts` and keyed by name. After a removal, a new file can repeat a name, and the remove filter then deletes both tiles. Key tiles by a unique id.
- Every tile shows the "CODE" badge with the `fileCode` icon, even for JSON and CSS files. Derive the badge from the file type.
- The add, clear, and remove controls have no `transition-*` class.
- The nav description promises a "breathing pulse", but no tile uses one.

## Code block with copy

Purpose: code output with a copy action and a timed check mark (L1329-1366). The block is dark in both themes.

```text
block             max-w-2xl mx-auto rounded-xl overflow-hidden border border-neutral-200 dark:border-neutral-800
                  bg-neutral-900 text-neutral-200 font-mono text-xs shadow-md
  header          flex items-center justify-between px-4 py-2 bg-neutral-950/70 border-b border-neutral-800
    language      text-neutral-400 flex items-center gap-1.5 (fileCode icon xs + "typescript")
    copy button   flex items-center gap-1.5 text-xs text-neutral-400 hover:text-neutral-200 transition-colors
      idle:       copy icon xs + "Copy code"
      copied:     text-green-400, check icon xs + "Copied"
                  animation: saved-check-decay 0.3s ease-out 2.5s forwards
  pre             p-4 overflow-x-auto leading-relaxed
```

Timing defect: the source resets the copied state at 2500ms, the same moment the 0.3s fade starts. The fade never shows. Reset after 2800ms (2.5s delay plus 0.3s fade), or reset on `animationend`.

Add `aria-live="polite"` to the copied label so screen readers hear the confirmation.

Other source defects:

- `navigator.clipboard.writeText` is not awaited or caught, so "Copied" shows even when the write fails. Await it and show a failure state.
- The reset timer is not cleared on a repeat click or unmount. Clear it before you set a new one.
- The header says "typescript" for any content, and there is no syntax highlighting.

## Cards, tiles, and panels

| Pattern | Classes |
| --- | --- |
| Standard card | `p-4 rounded-xl border border-neutral-200 dark:border-neutral-800 bg-white dark:bg-neutral-900` |
| Constraint panel | `p-5 rounded-2xl bg-white dark:bg-neutral-900 border border-neutral-200 dark:border-neutral-800 space-y-4` |
| Inset demo well | `p-4 rounded-xl bg-neutral-100 dark:bg-neutral-950 border border-neutral-200 dark:border-neutral-800` (or `bg-neutral-50`) |
| Info strip | `p-3 rounded-xl border border-neutral-200/80 dark:border-neutral-800 bg-neutral-50 dark:bg-neutral-900/50` |
| Metric tile | `p-3 rounded-lg bg-neutral-50 dark:bg-neutral-950 border border-neutral-200/60 dark:border-neutral-800/60`; label `text-[10px] font-mono text-neutral-400 uppercase`; value `text-xs font-mono font-semibold` |
| Selectable card | `p-3 rounded-xl text-left border transition-all`; selected `border-clay bg-clay/5 dark:bg-clay/10 shadow-xs` with a `size-2 rounded-full bg-clay` dot; idle `border-neutral-200 dark:border-neutral-800 bg-white dark:bg-neutral-900 hover:border-neutral-300` |
| Hoverable tile | `cursor-pointer p-4 rounded-xl border ... hover:border-clay/60 hover:shadow-md transition-all active:scale-95`; the source uses a clickable `div`, so make it a `button` |
| Popover card (spring demo) | `w-full max-w-xs p-3.5 rounded-xl border border-clay/30 bg-clay/5 dark:bg-clay/10 shadow-md flex items-center gap-3`; leading `size-8 rounded-lg bg-clay text-white` icon square |
| Color swatch card | `p-4 rounded-xl border ... flex items-center gap-3.5`; swatch `size-11 rounded-lg shadow-inner` |
| Gray chip | `p-2 rounded-lg border ... text-center`; color bar `h-8 rounded border border-black/10 dark:border-white/10 mb-1.5` |

## Badges, chips, and inline code

| Pattern | Classes |
| --- | --- |
| Clay badge | `text-[11px] font-mono px-2 py-0.5 rounded bg-clay/10 text-clay font-medium` |
| Clay pill tag | `text-[10px] font-medium px-2 py-0.5 rounded-full bg-clay/10 text-clay` |
| Neutral tag | `text-[11px] px-1.5 py-0.5 rounded bg-neutral-100 dark:bg-neutral-800 text-neutral-600 dark:text-neutral-300` |
| Mono readout chip | `text-[11px] font-mono text-neutral-500 bg-neutral-100 dark:bg-neutral-800 px-2 py-0.5 rounded` |
| Inline code token | `font-mono text-clay`, optionally `bg-neutral-100 dark:bg-neutral-800 px-1 py-0.5 rounded` |
| Code value pill | `text-xs font-mono text-clay bg-neutral-100 dark:bg-neutral-800 px-2 py-1 rounded w-fit` |

## Inputs

| Pattern | Classes |
| --- | --- |
| Small search field | `h-7 px-2.5 text-xs rounded-lg border border-neutral-200 dark:border-neutral-700 bg-neutral-50 dark:bg-neutral-800 text-neutral-800 dark:text-neutral-200 outline-none focus:border-clay` |
| Sidebar search | `w-full text-xs rounded-lg px-2.5 py-1.5 outline-none border transition-colors`; light `bg-[#f0f0ee] border-neutral-300/80 focus:border-neutral-500 placeholder-neutral-400`; dark `bg-[#242424] text-neutral-200 border-neutral-700/60 focus:border-neutral-500 placeholder-neutral-500` |
| Plain text field | `w-full p-2 rounded-lg border border-neutral-200 dark:border-neutral-800 bg-transparent text-sm` |
| Select | `border text-xs rounded-md px-2 py-1 outline-none`; light `bg-white border-neutral-200 text-neutral-700`; dark `bg-[#282828] border-neutral-700/60 text-neutral-300` |
| Range | `accent-clay`; label row `flex justify-between text-xs`, value `font-mono text-clay font-bold` |
| Form label | `text-xs font-semibold text-neutral-500 uppercase` |

## App shell

These patterns come from the Storybook's own chrome (L2271-2527). The source does not label them as CDS components, but they use the same tokens.

| Region | Classes and values |
| --- | --- |
| Root | `flex h-full w-full transition-colors duration-200`; light `bg-[#f8f9fa] text-neutral-900`; dark `bg-[#121212] text-neutral-100` |
| Sidebar | `w-64 border-r flex flex-col flex-shrink-0 select-none`; light `bg-[#fcfcfb] border-neutral-200`; dark `bg-[#181818] border-neutral-800` |
| Sidebar header | `p-4 border-b`; logo square `size-6 rounded-md bg-[#d97757] text-white shadow-sm` with spark 14px; title `text-xs font-bold tracking-wide`; subtitle `text-[10px] text-neutral-500 font-mono` |
| Nav | `flex-1 overflow-y-auto p-2 space-y-4`; group label `px-2 py-1 text-[10px] font-bold text-neutral-500 tracking-wider font-mono uppercase` |
| Nav item | `w-full flex items-center gap-2.5 px-2.5 py-1.5 rounded-lg text-xs font-medium transition-colors text-left`, label `truncate` |
| Nav item active | light `bg-neutral-200/80 text-neutral-900 font-semibold shadow-xs`; dark `bg-[#2a2a2a] text-white font-semibold` |
| Nav item idle | light `text-neutral-600 hover:text-neutral-900 hover:bg-neutral-100`; dark `text-neutral-400 hover:text-neutral-200 hover:bg-[#202020]` |
| Sidebar footer | `p-3 border-t text-[11px] flex justify-between items-center`; accent value `text-clay font-mono font-medium` |
| Top bar | `h-12 border-b px-4 flex items-center justify-between flex-shrink-0`; light `bg-white border-neutral-200`; dark `bg-[#1e1e1e] border-neutral-800` |
| Breadcrumb | `flex items-center gap-2 text-xs`; root `text-neutral-400 font-mono`; separator `/`; current `font-semibold` |
| Toolbar switch | track `flex items-center p-0.5 rounded-lg border`, light `bg-neutral-100 border-neutral-200`, dark `bg-[#282828] border-neutral-700/60`; item `px-2.5 py-1 text-xs rounded-md font-medium`; active light `bg-white text-neutral-900 shadow-xs`, dark `bg-[#383838] text-white` |
| Toolbar divider | `h-4 w-px bg-neutral-200 dark:bg-neutral-800` |
| Canvas stage | `flex-1 overflow-auto p-4 sm:p-8 flex items-center justify-center`; light `bg-[#eef1f5]` with `radial-gradient(#cbd5e1 1.2px, transparent 1.2px)`; dark `bg-[#0f0f0f]` with `radial-gradient(#262626 1px, transparent 1px)`; `background-size: 18px 18px` |
| Canvas card | `rounded-2xl overflow-hidden transition-all duration-300`, `min-height: 560px`; light `bg-[#ffffff] border border-neutral-200/90 shadow-xl`; dark `bg-[#151515] border border-neutral-800 shadow-2xl` |
| Card header | `px-6 py-4 flex justify-between items-center border-b`; light `border-neutral-200/90 bg-neutral-50/80`; dark `border-neutral-800 bg-neutral-900/40`; body `p-4 sm:p-6` |
| Viewport widths | mobile `w-[390px]`, tablet `w-[768px]`, desktop `w-[1024px]`, responsive `w-full max-w-5xl` |
| Docs panel | `p-4 bg-neutral-100 dark:bg-neutral-950 rounded-xl space-y-2 font-mono text-xs`; heading `text-clay font-bold` |
| Scrollbar | 6px; transparent track; thumb `rgba(150,150,150,0.25)`, hover `0.4`, radius 999px |

Responsive grids use a container query on the canvas card (`container-type: inline-size; container-name: canvas-stage`):

| Grid | 520px or less | 521px to 820px | 821px or more |
| --- | --- | --- | --- |
| Surfaces | 1 column | 2 | 4 |
| Clay | 1 | 3 | 3 |
| Grays | 5 | 5 | 10 |
| Radius, heights, shadows, motion tokens | 1 | 2 | 3 |

Icon catalog grid: `grid-template-columns: repeat(auto-fill, minmax(130px, 1fr)); gap: 0.75rem`.

Container query notes: the bands `max-width: 520px` and `min-width: 521px` (and 820px and 821px) leave fractional widths unmatched. Use range syntax (`width <= 520px`, `width > 520px`). The `!important` flags have no competing rule, and `.cds-grid-heights` is never applied.

Shell defects to avoid:

- The Storybook nav uses emoji as item icons. Use Phosphor icons in product UI instead.
- The sidebar header is about 64px tall while the top bar is 48px, so their bottom borders do not line up. Give both the same height.
- "Responsive (100%)" is capped by `max-w-5xl` (1024px), the same as "Desktop".
- The viewport presets change only the card width. `sm:` classes still follow the real browser width; only the container queries respond.
- The theme is not persisted and ignores `prefers-color-scheme`.
- The scrollbar style is WebKit-only. Add `scrollbar-width: thin` and `scrollbar-color` for Firefox.
- The dot grid switches instantly while the stage color transitions over 200ms.
- Nav items need `aria-current`, the theme buttons need `aria-pressed`, the search field and select need labels, and the select has `outline-none` with no replacement focus style.
- `placeholder-neutral-*` is deprecated in Tailwind v3; use `placeholder:text-neutral-400`.

## Motion lab UI patterns

These patterns visualize springs and can be reused for any progress or physics readout (L1785-2264).

| Pattern | Classes and values |
| --- | --- |
| Track | `relative h-11 rounded-lg bg-neutral-100 dark:bg-neutral-950 p-1 flex items-center overflow-hidden border border-neutral-200/60 dark:border-neutral-800` (custom track `h-10`) |
| Target line | at `left: 72%`, `border-r-2 border-dashed border-[#d97757]/60`; label `text-[9px] font-mono font-semibold text-clay px-1 bg-white/80 dark:bg-neutral-900/80 rounded` |
| Overshoot zone | from 72% to the right edge, `bg-[#d97757]/5 border-l border-clay/20`; label `text-[9px] font-mono text-clay/70` |
| Runner chip | `absolute h-9 rounded-md bg-white dark:bg-neutral-800 border border-neutral-200 dark:border-neutral-700 shadow-md flex items-center gap-1.5 px-2.5`; spark xs clay plus `text-xs font-mono font-semibold tabular-nums` percent; `left = max(0, value * 72)%` |
| Drag stage | `relative h-56 rounded-xl bg-neutral-100/70 dark:bg-neutral-950/70 border border-dashed border-neutral-300 dark:border-neutral-800`; crosshair lines `absolute w-full h-[1px]` and `absolute h-full w-[1px]`, `bg-neutral-200 dark:bg-neutral-800`; center target `w-8 h-8 rounded-full border border-neutral-300 dark:border-neutral-800` with a `size-1.5 rounded-full bg-neutral-400` dot |
| Draggable card | `absolute z-10 w-44 p-3 rounded-xl bg-white dark:bg-neutral-900 border-2 border-clay/80 shadow-xl`; `cursor: grab`, `grabbing` while dragging; `touch-action: none`; moved with `translate3d` |
| Response chart | SVG `viewBox="0 0 540 155"`, `w-full h-40`; baseline at y 140 (opacity 0.1); midline y 85 dashed `4 4` (opacity 0.06); target line `#d97757` opacity 0.6 dashed `3 3` with a "1.0 Target" label; curve stroke `#d97757` width 2.5, round caps; y labels "0.0" and "1.0"; time labels 0.0s, 0.4s, 0.8s, 1.4s; labels 9px to 10px monospace at 40% opacity |

The source comment says the target sits at 74% of the track; the code uses 72%. The code value renders.

Demo layout widths that keep story content from collapsing: height sample chip `min-w-[120px] px-3 rounded-md`; alignment ruler `min-w-[320px]`; grapheme stagger slider `min-w-[200px]`.
