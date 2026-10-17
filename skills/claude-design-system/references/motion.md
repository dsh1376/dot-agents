# CDS motion

Read this when you animate anything: transitions, entry animations, streaming text, the thinking indicator, or spring physics. [foundations.md](foundations.md) owns static tokens. [components.md](components.md) shows where each animation is used. Line numbers (`L123`) point into the source snapshot.

## Duration tokens

| Token | Value | Use |
| --- | --- | --- |
| `--cds-dur-fast` | 60ms | Micro feedback, icon color change, button micro-scale |
| `--cds-dur-snap` | 120ms (0.12s) | Instant snapping, segmented slider, tab switch |
| `--cds-dur-base` | 200ms (0.2s) | Standard component transitions, menu pop-up, grid collapse and expand |
| `--cds-dur-sheet` | 300ms (0.3s) | Sidebar slide-in, artifact panel slide-out |
| `--cds-dur-slow` | 450ms (0.45s) | Large color transitions, ambient glow |

## Easing tokens

| Token | Curve | Character |
| --- | --- | --- |
| `--cds-ease-out` | `cubic-bezier(.165, .84, .44, 1)` | Smooth deceleration, close to easeOutQuart |
| `--cds-ease-snap` | `cubic-bezier(.32, .72, 0, 1)` | Fast start, decisive settle |
| `--cds-ease-overshoot` | `cubic-bezier(.34, 1.3, .64, 1)` | Natural spring with light overshoot |

The source says the overshoot curve has "1.3x" overshoot. The 1.3 is a control point. The computed peak is 1.030, a 3.0% overshoot at 67% of the duration.

The source shows the `--cds-dur-*` and `--cds-ease-*` names but never declares them; its components hard-code values. [assets/cds-tokens.css](../assets/cds-tokens.css) declares them.

Other curves the components use. The names are labels from this skill; the source uses only the literal:

| Label | Curve | Where |
| --- | --- | --- |
| expand | `cubic-bezier(.19, 1, .22, 1)` | Thinking accordion `grid-template-rows` |
| grapheme | `cubic-bezier(.215, .61, .355, 1)` | Streaming grapheme reveal |
| message | `cubic-bezier(.34, 1.56, .64, 1)` | Assistant message fade-up (computed peak 9.8% overshoot at 57%) |
| dots | `cubic-bezier(.4, 0, .2, 1)` | Thinking quiet dots |
| block | `cubic-bezier(.22, 1, .36, 1)` | First segment of `timeline-block-enter` |

## Transitions in use

| Element | Property, duration, easing |
| --- | --- |
| Composer shell shadow | `transition-all duration-200`, Tailwind default easing `cubic-bezier(0.4, 0, 0.2, 1)` (runtime check) |
| Send button | `transition-all duration-150` |
| Thinking body | `grid-template-rows` 200ms `cubic-bezier(.19,1,.22,1)` |
| Thinking chevron | `transition-transform duration-200`, `rotate-180` when open |
| Segmented thumb | `transition-all duration-150 ease-[cubic-bezier(.32,.72,0,1)]` (token says 120ms) |
| Segmented labels | `transition-colors duration-150` |
| Buttons, tiles | `transition-all` or `transition-colors` (Tailwind default 150ms), `active:scale-95` |
| Icon tile hover | `group-hover:scale-110 group-hover:text-clay` |
| Inline icon alignment demo | `vertical-align 0.15s ease` |
| Theme switch (shell) | `transition-colors duration-200` |
| Canvas card resize (shell) | `transition-all duration-300` |

Off-ladder timings in the source: the assistant message uses 1.1s, beyond the 450ms maximum. The user bubble uses 0.45s, the `slow` token, whose stated use is color transitions. Buttons use Tailwind's default 150ms, not the 60ms `fast` token. The grapheme reveal (90ms) and quiet dots (1.9s) have their own timings.

## Keyframes

All eleven keyframes in the source, with the timing the source applies. [assets/cds-tokens.css](../assets/cds-tokens.css) defines them verbatim.

| Keyframe | Steps | Applied with | Use |
| --- | --- | --- | --- |
| `_grapheme-reveal_1plog_1` | opacity 0 and `blur(6px)` to opacity 1 and `blur(0)` | `90ms cubic-bezier(.215, .61, .355, 1) both`, delay `index * 30ms` | Streaming text, per character |
| `timeline-quiet-dot` | 0% and 8%: opacity 0, `translateY(2px)`; 22%: opacity 1, `translateY(-2.5px)`; 36% to 70%: opacity 1, rest; 90% to 100%: opacity 0 | `1.9s cubic-bezier(.4, 0, .2, 1) infinite`, delays 0s, 0.15s, 0.3s | Thinking dots |
| `message-fade-up` | opacity 0, `translateY(10px)` to opacity 1, rest | `1.1s cubic-bezier(.34, 1.56, .64, 1)` | Assistant message enter |
| `code-user-bubble-enter` | opacity 0, `scale(0.92) translateY(6px)` to opacity 1, `none` | `0.45s cubic-bezier(.34, 1.3, .64, 1)` | User bubble enter |
| `file-thumbnail-grow-in` | opacity 0, `max-width: 0`, `scale(0.8)` to opacity 1, `max-width: 8rem`, `scale(1)` | `0.25s cubic-bezier(.165, .84, .44, 1)` | Attachment tile enter |
| `saved-check-decay` | to opacity 0 | `0.3s ease-out 2.5s forwards` | Copied check mark fade |
| `timeline-block-enter` | 0%: opacity 0, `blur(4px)`, `translateY(5px) scale(0.9)`, then `cubic-bezier(.22, 1, .36, 1)`; 30%: opacity 0, `blur(4px)`, `transform: none`, then `ease-out`; 100%: opacity 1, no filter | not applied | Timeline block enter: settles position first, then fades in |
| `byline-sent-bump` | 50%: `scale(1.05)` | not applied | Byline bump on send |
| `cds-skeleton-sweep` | `translateX(-100%)` to `translateX(100%)` | not applied | Skeleton loading shimmer |
| `file-thumbnail-upload-pulse` | 50%: opacity 0.45 | not applied | Attachment uploading state |
| `click-ring` | opacity 0.95, `scale(0.22)` to opacity 0, `scale(1)` | not applied | Click ripple ring |

`timeline-block-enter` finishes its translate and scale at 30% while opacity is still 0. Only the fade and unblur from 30% to 100% are visible. To show the movement, keep the transform until opacity rises.

The keyframe name `_grapheme-reveal_1plog_1` carries a CSS-module hash. Inline animation strings break silently if it is renamed.

The source gives no duration, easing, or iteration for the five unapplied keyframes. When you use one, choose from the duration and easing tokens and say which you chose. Reasonable starting points: skeleton sweep `infinite` at about 1.2s to 1.5s linear; upload pulse `infinite` at about 1.2s; click ring once at `--cds-dur-slow` with `--cds-ease-out`. These starting points are skill recommendations, not source data.

## Streaming grapheme reveal

The source restates the spec in a sample message: each character dissolves in from a 6px Gaussian blur over 90ms, with a 30ms cascade, like ink spreading (L1075).

Implementation in the source (L1369-1434):

```jsx
{Array.from(text).map((char, index) => (
  <span
    key={index}
    className="inline-block whitespace-pre"
    style={{
      animation: "90ms cubic-bezier(.215, .61, .355, 1) both _grapheme-reveal_1plog_1",
      animationDelay: `${index * delayMs}ms`,
    }}
  >{char}</span>
))}
```

- Container: `text-base sm:text-lg leading-relaxed font-sans break-words` on `#fcfcfb` (dark `#151515`), `p-6 rounded-2xl`, `min-h-[120px]`.
- Stagger control: 10ms to 80ms, default 30ms.
- Replay: unmount, then remount after 50ms.
- `both` fill mode holds each character invisible until its delay passes.

Defects to avoid:

- `Array.from(text)` splits by code point, not grapheme. Emoji sequences and combining marks split apart. Use `Intl.Segmenter` with `granularity: "grapheme"`.
- In a live stream, compute the delay from the index within the newly arrived chunk, not the whole message. Otherwise late characters wait seconds. This is a skill recommendation; the source animates a fixed string.
- Wrapping every character in `inline-block` breaks normal line breaking between words. Keep `whitespace-pre` on spaces, or wrap words and animate characters inside them.
- Replay unmounts the text for 50ms, so a tall block collapses and grows back. The replay timer is not cleared on fast clicks or unmount.

## Thinking quiet dots

| Variant | Dot | Gap | Color |
| --- | --- | --- | --- |
| Inline (accordion header) | `size-[3px]` | `gap-[3.5px]` | `#d97757` |
| Enlarged (lab) | `size-2` (8px) | `gap-[6px]` | `#d97757` |

All dots: `1.9s cubic-bezier(.4, 0, .2, 1) infinite timeline-quiet-dot`. Dot 2 starts 0.15s late and dot 3 starts 0.3s late, which makes a clay wave that rises and falls. Each dot is visible only from 22% to 70% of the cycle, so the row reads as quiet rather than busy. Pair the dots with a "Thinking..." label in `text-clay font-medium`.

The animation has no fill mode, so dots 2 and 3 show solid during their delay on mount. Use negative delays (`0s`, `-1.75s`, `-1.6s`) to keep the offsets without the flash. The enlarged lab variant scales the dots 2.67x but keeps the same 2px to 2.5px travel, so its bounce looks smaller than the inline one.

## Spring presets

The source lists Framer Motion spring presets (L1457-1506). The nav text says six sets; the code defines four.

| Preset | stiffness | damping | mass | Framer Motion config in source | Use |
| --- | --- | --- | --- | --- | --- |
| Button and small controls | 600 | 30 | 1 | `{ type: "spring", stiffness: 600, damping: 30 }` | Button clicks, switch toggles, icon presses; crisp, settles fast |
| Card and popover reveal | 320 | 28 | 1 | `{ type: "spring", stiffness: 320, damping: 28 }` | Popovers, card expand, message rise; slight overshoot, steady landing |
| Bouncy light feedback | 380 | 18 | 1 | `{ type: "spring", duration: 0.32, bounce: 0.15 }` | Likes, copy-success check, new message nudge; two to three visible oscillations |
| High-damping panel drag | 1200 | 80 | 0.1 | `{ type: "spring", stiffness: 1200, damping: 80, mass: 0.1 }` | Sidebar and overlay drag snapping; follows the finger, locks on release |

Source labels versus physics. The table below recomputes each preset with the source's own solver (step response from 0 to 1, settle within 2%). Use the parameters; treat the labels as approximate.

| Preset | Damping ratio | Overshoot label | Computed overshoot | Settle label | Computed settle (2%) |
| --- | --- | --- | --- | --- | --- |
| Button | 0.612 | +4.2% | 8.8% | 0.22s | 0.24s |
| Card | 0.783 | +1.8% | 1.9% | 0.35s | 0.20s |
| Bouncy | 0.462 | +19.4% | 19.5% | 0.58s | 0.43s |
| Drag | 3.651 (overdamped) | 0% | 0.0% | 0.14s | 0.26s |

The Bouncy row has a second mismatch: the simulation uses stiffness 380 and damping 18, but the copyable config uses `duration: 0.32, bounce: 0.15`. Those two configs do not describe the same motion. Pick one per project.

### Solver

The source solves the damped harmonic oscillator exactly (L1509-1534):

```js
const calcSpring = (cfg, t) => {
  const k = Math.max(1, cfg.stiffness);
  const c = Math.max(0.1, cfg.damping);
  const m = Math.max(0.01, cfg.mass || 1);
  const w0 = Math.sqrt(k / m);                 // natural angular frequency
  const zeta = c / (2 * Math.sqrt(m * k));     // damping ratio
  const x0 = (cfg.from ?? 0) - (cfg.to ?? 1);
  const v0 = cfg.v0 ?? 0;
  let x;
  if (zeta < 0.999) {                          // underdamped
    const wd = w0 * Math.sqrt(1 - zeta * zeta);
    x = Math.exp(-zeta * w0 * t) * (x0 * Math.cos(wd * t) + ((v0 + zeta * w0 * x0) / wd) * Math.sin(wd * t));
  } else if (zeta <= 1.001) {                  // critically damped
    x = Math.exp(-w0 * t) * (x0 + (v0 + w0 * x0) * t);
  } else {                                     // overdamped
    const s = Math.sqrt(zeta * zeta - 1);
    const r1 = -w0 * (zeta - s), r2 = -w0 * (zeta + s);
    const A = (v0 - r2 * x0) / (r1 - r2);
    x = A * Math.exp(r1 * t) + (x0 - A) * Math.exp(r2 * t);
  }
  return (cfg.to ?? 1) + x;
};
```

Regime labels: critically damped when `|zeta - 1| < 0.05`, overdamped when `zeta > 1`, otherwise underdamped.

### Interaction recipes driven by springs

| Interaction | From and to | Run window | Notes |
| --- | --- | --- | --- |
| Press and pop | scale 0.88 on press, spring 0.88 to 1 on release | 1.2s | `transition: none`; mouse and touch handlers |
| Card reveal | scale 0.3 to 1 on open, 1 to 0 on close | 1.2s | `opacity = min(1, scale * 1.2)` |
| Drag and snap back | offset follows pointer; on release progress 1 to 0 multiplies the offset | 1.3s | `translate3d`, `touch-action: none`, listeners on `window` |
| Race track | position 0 to 1 | 1.4s | runner `left = max(0, value * 72)%` |

Each runner uses `requestAnimationFrame` and cancels the previous frame before it starts. At the end of the window the value snaps to its target.

Custom tuning ranges in the lab:

| Parameter | Min | Max | Step | Default | Effect |
| --- | --- | --- | --- | --- | --- |
| Stiffness | 50 | 1500 | 10 | 450 | Pull strength; higher is faster with higher frequency |
| Damping | 5 | 100 | 1 | 25 | Resistance; too low jitters, too high feels sticky |
| Mass | 0.1 | 3.0 | 0.05 | 1 | Inertia; more mass gives more overshoot and oscillation |

Response chart: 140 samples over 1.4s; `x = 36 + (t / 1.4) * 480`; `y = 140 - (value / 1.35) * 110`. The readouts show damping ratio (3 decimals), natural frequency in rad/s (1 decimal), overshoot percent, and peak time.

Copy feedback timers: the spring config copy label resets after 2000ms; the icon catalog "copied" label resets after 1500ms; the code block resets after 2500ms (see the defect in components.md).

## Reduced motion

The source has no `prefers-reduced-motion` handling. [assets/cds-tokens.css](../assets/cds-tokens.css) adds a global rule that shortens animations and transitions to 1ms and runs each animation once. Under that rule:

- Thinking dots stop and stay visible, because their animation has no fill mode. This holds with positive or negative delays.
- Grapheme reveal still staggers by delay. For a stream, render text without the per-character animation when reduced motion is on.
- Spring-driven interactions run in JavaScript and ignore CSS. Check `matchMedia("(prefers-reduced-motion: reduce)")` and jump to the end value.
