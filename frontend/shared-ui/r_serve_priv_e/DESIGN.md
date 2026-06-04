# Design System Specification: The Sommelier’s Editorial

## 1. Overview & Creative North Star
**Creative North Star: "The Digital Cellar"**

This design system moves away from the sterile, modular nature of standard PWAs and instead embraces the tactile, high-end feel of a vintage editorial magazine. The objective is to evoke the sensory experience of wine—the weight of the glass, the texture of the cork, and the depth of a vintage red.

To achieve this, we reject the "box-and-line" layout. Instead, we use **Intentional Asymmetry** and **Tonal Depth**. Elements should feel "placed" rather than "slotted," with large serif typography breaking the grid to create a sense of bespoke craftsmanship. We lean into breathing room (white space) not as "empty space," but as a luxury asset.

---

## 2. Colors & Surface Architecture

The palette is rooted in the organic tones of viticulture. We utilize a sophisticated "No-Line" rule: **1px solid borders are strictly prohibited for sectioning.**

### The Color Palette (Material Design Mapping)
*   **Primary (#4A0E0E):** The Deep Burgundy. Used for moments of high brand impact and core CTAs.
*   **Surface/Background (#F5F5DC):** Aged Cork Cream. The foundation of the experience, providing a warmer, more premium feel than pure white.
*   **Secondary (#536161):** Slate Charcoal. Used for functional elements that require a neutral, grounding presence.

### Surface Hierarchy & Nesting
Depth is created through "Tonal Stacking." To separate a section, do not draw a line; shift the surface token.
*   **Base Layer:** `surface` (#FBFBE2)
*   **Low-Level Section:** `surface_container_low` (#F5F5DC)
*   **Elevated Card:** `surface_container_lowest` (#FFFFFF) – creates a "bright" lift on the cream background.
*   **Nesting:** A `surface_container_highest` element should only ever sit atop a `surface` or `surface_container_low` to ensure a soft, natural transition of depth.

### The "Glass & Gradient" Rule
To add "soul," use subtle linear gradients (e.g., `primary` to `primary_container`) for large buttons or hero overlays. For floating navigation or modals, employ **Glassmorphism**:
*   **Background:** `surface` at 70% opacity.
*   **Effect:** `backdrop-filter: blur(12px)`.
*   **Result:** A frosted-glass effect that allows the rich burgundy and cream tones to bleed through, softening the interface.

---

## 3. Typography: Editorial Authority

We pair a high-contrast Serif with a modern, functional Sans-Serif to balance heritage with utility.

*   **The Hero (Noto Serif):** Used for all `display` and `headline` roles. This font should be treated like a masthead. Use tight letter-spacing (-0.02em) for large displays to increase the "expensive" feel.
*   **The Utility (Manrope):** Used for `title`, `body`, and `label`. Manrope provides a clean, technical counterpoint to the serif, ensuring legibility in functional PWA tasks like inventory management or wine notes.

**Hierarchy Intent:**
*   **Display LG (3.5rem):** For vintage names or featured collections.
*   **Title MD (1.125rem):** For navigation and interactive labels.
*   **Body LG (1rem):** Optimized for long-form tasting notes with generous line-height (1.6).

---

## 4. Elevation & Depth: The Layering Principle

We define hierarchy through **Ambient Light**, not structural barriers.

*   **Layering over Lines:** To separate content, use vertical white space or a subtle shift from `surface` to `surface_variant`. 
*   **Ambient Shadows:** For floating elements (like a "Add to Cellar" FAB), use an extra-diffused shadow:
    *   `box-shadow: 0 12px 32px rgba(74, 14, 14, 0.08);` (Note the use of a tinted burgundy shadow instead of grey).
*   **The "Ghost Border" Fallback:** If a boundary is required for accessibility, use the `outline_variant` token at **15% opacity**. It should be felt, not seen.
*   **Roundedness:** Adhere to the `lg` (0.5rem) scale for cards and `full` for buttons. Avoid sharp 0px corners, which feel too aggressive for this organic palette.

---

## 5. Components

### Buttons: The Touchpoints
*   **Primary:** `primary` background with `on_primary` text. Apply a subtle 10-degree gradient.
*   **Secondary:** `surface_container_highest` background. No border.
*   **Tertiary:** Text-only in `primary` with an icon. 
*   **Interaction:** On hover, shift the background to `primary_container`.

### Cards: The Tasting Flight
*   **Constraint:** No borders, no heavy shadows.
*   **Styling:** Use `surface_container_lowest` for the card body against a `surface_container_low` background. 
*   **Spacing:** Increase internal padding to `1.5rem` (24px) to allow the typography to breathe.

### Input Fields: The Ledger
*   **Style:** Underline-only or subtle "Ghost" containers.
*   **State:** When focused, the label should transform into `primary` burgundy.
*   **Error:** Use `error` (#BA1A1A) with a `surface_container_lowest` background to ensure the error "pops" without breaking the elegance.

### Signature Component: The "Vintage Picker"
*   A horizontal scrolling chip-set using `secondary_container` for unselected years and `primary` for the active selection. Use `surface_dim` for the container to give it a "recessed" feel in the UI.

---

## 6. Do's and Don'ts

### Do:
*   **Do** use asymmetrical layouts. Place an image offset to the right with a `display-lg` headline overlapping its edge by 10%.
*   **Do** use the `primary_fixed_dim` for subtle accent backgrounds in dark mode transitions.
*   **Do** lean into `on_surface_variant` for secondary metadata—it provides a sophisticated "faded ink" look.

### Don't:
*   **Don't** use 1px dividers between list items. Use 16px of `surface_container_low` padding instead.
*   **Don't** use pure black (#000) for text. Always use `on_surface` (#1B1D0E) to maintain the "Aged Paper" warmth.
*   **Don't** use "Pop" colors like bright blue or neon green for success states. Use a muted teal or the `secondary` slate.