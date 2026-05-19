# Code Snippets

  Selected reusable patterns from the portfolio source, sanitized
  and generalized for reuse in any Next.js 16 project.

  | File | Purpose |
  |---|---|
  | `use-scroll-reveal.ts` | Custom IntersectionObserver hook that
  flips a boolean when an element enters the viewport. |
  | `scroll-reveal-wrapper.tsx` | `<ScrollReveal>` component using the
   hook, applies CSS transitions. |
  | `use-view-transition-router.ts` | Hook wrapping Next.js
  `router.push` in `document.startViewTransition`. Feature-detected;
  falls back to plain navigation under reduced-motion or unsupported
  browsers. |
  | `hero-shader.tsx` | Hand-written WebGL fragment shader (FBM noise)
   as a hero ambient background. Six hard mount guards,
  IntersectionObserver pause when off-screen, `visibilitychange` pause
   when tab hidden, `webglcontextlost` fallback. |
  | `split-text.tsx` | Per-letter reveal — wraps each character in an
  `inline-block` span with staggered `animation-delay`. `aria-label`
  on the parent preserves screen-reader text; chars are `aria-hidden`.
   |
  | `tech-stack-marquee.tsx` | Seamless infinite-scroll marquee —
  duplicated track + `mask-image` edge fade. Pauses on hover and when
  off-screen. Inline Simple Icons SVG paths for brand logos. |
  | `back-to-top-progress.tsx` | Floating back-to-top button with a
  scroll-progress SVG ring driven by `stroke-dashoffset`.
  rAF-throttled scroll handler. |
  | `typewriter-cycle.tsx` | Role-cycling typewriter (typing → waiting
   → deleting → next) with a state machine. Pauses when off-screen or
  tab hidden. |
  | `architecture-diagram.tsx` | SVG flow diagram component — manual
  node placement on a row/col grid, straight edges. |

  ## How to use

  Each snippet is self-contained with a doc-comment header. Drop
  into a Next.js 16 App Router project, adjust styling tokens to
  match your design, and import where needed.

  ## Conventions

  - All snippets target Next.js 16 (App Router) on React 19 with
  TypeScript.
  - Components that use browser APIs are marked `"use client"`.
  - Styling uses inline `style={{...}}` or scoped CSS via
    `dangerouslySetInnerHTML` — Tailwind is imported in `globals.css`
    for its base reset only, no utility classes in components.
  - Color tokens (`var(--accent)` etc.) are referenced; define
    these on `:root` in your global stylesheet to match.
  - All animated patterns respect `prefers-reduced-motion` and gate
    hover behaviour behind `@media (hover: hover) and (pointer: fine)`
    so touch devices never get sticky hover.
  ## Browser support notes
  
  - `use-view-transition-router.ts` — uses
  `document.startViewTransition`
    (Chrome 111+, Edge 111+). Falls back to plain `router.push` in
    Safari and Firefox. Also bails under `prefers-reduced-motion:
  reduce`
    and when modifier keys are held (so cmd-click still opens in a new
   tab).
  - `hero-shader.tsx` — requires a WebGL context. The component
    feature-detects and stays unmounted under any of: reduced-motion
    users, touch devices (`pointer: coarse`), viewports under 768 px,
    GL context acquisition failure, shader compile/link failure, and
    `webglcontextlost` events. A CSS fallback (conic-gradient orb) is
    expected to sit under the canvas in your hero markup.
    
  ## Suggested CSS variables

  The snippets reference a small set of design tokens. Add to your
  global stylesheet:
  
  ```css
  :root {
    --bg: #0a0e1a;
    --surface: #131826;
    --border: #1f2937;
    --text-1: #e5e7eb;
    --text-2: #9ca3af;
    --text-3: #6b7280;
    --accent: #00d4a1;
    --accent-dim: rgba(0, 212, 161, 0.1);
    --radius: 8px;
  }
  
  Adjust values to match your design. The snippets reference the
  names, not specific colors, so customizing the palette is a
  single-file edit.
  
  Reduced-motion baseline

  The animated snippets (split-text.tsx, tech-stack-marquee.tsx,
  back-to-top-progress.tsx, typewriter-cycle.tsx,
  scroll-reveal-wrapper.tsx) don't re-declare a reduced-motion clamp
  inside each component. They expect this global rule in your
  stylesheet:

  @media (prefers-reduced-motion: reduce) {
    *,
    *::before,
    *::after {
      transition-duration: 0.01ms !important;
      animation-duration: 0.01ms !important;
      animation-iteration-count: 1 !important;
      animation-delay: 0ms !important;
      scroll-behavior: auto !important;
    }
  }

  With this in place, every transition collapses to instant and every
  keyframe animation completes in one frame and holds at its end state
  (when animation-fill-mode: forwards is set on the consumer). The
  snippets are written assuming this is true; they add their own local
  overrides only where the global clamp can't reach (e.g., killing a
  specific hover transform).
