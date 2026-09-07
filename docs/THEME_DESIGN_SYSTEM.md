# SAHAKARI SEVA — THEME & DESIGN SYSTEM SPECIFICATION

**Updated**: September 6, 2026
**Lead UI/UX Architect**: Senior Mobile Design Systems Engineer
**Objective**: Define the centralized visual tokens, design principles, component specs, and styling standards for the Sahakari Seva Mobile Application to ensure a cohesive, accessible, and premium Indian cooperative identity — now with a complete **Light / Dark theme system** and animated switching.

---

## 1. Visual Philosophy & Brand Identity

Sahakari Seva embodies **"Empowerment through Cooperation"**. The design system marries modern minimalist fintech aesthetics with an elegant Indian cooperative heritage — **Ivory & Royal Indigo** by day, **Midnight Indigo** by night:

- **Royal Indigo (`#4f46e5`)**: Represents trust, federation governance, and dignified digital service — the cooperative's authoritative primary.
- **Antique Gold (`#a16207`)**: Represents the dignity of labor, warmth, and the welfare corpus — a champagne-gold secondary.
- **Warm Ivory (`#faf7f2`)**: Anti-glare warm canvas for indoor and outdoor field usage in light mode.
- **Midnight Navy (`#0c101f`)**: Deep, comfortable dark canvas that preserves contrast and reduces eye strain at night.

---

## 2. Design Tokens

### 2.1 Color Tokens (Dual Theme)

Every screen consumes tokens through `useTheme()` — the palette swaps instantly app-wide when the theme changes, with a branded cross-fade overlay animation.

```typescript
// LIGHT — Ivory canvas, Royal Indigo, Antique Gold
export const lightColors: Palette = {
  primary: '#4f46e5',        // Royal Indigo
  primaryLight: '#e0e7ff',   // Soft Indigo Tint
  primaryDark: '#4338ca',    // Deep Royal Indigo

  secondary: '#a16207',      // Antique Gold
  secondaryLight: '#fef3c7', // Soft Gold Tint
  secondaryDark: '#854d0e',  // Deep Bronze Gold

  background: '#faf7f2',     // Warm Ivory canvas
  surface: '#ffffff',        // Pure white cards
  surfaceSubtle: '#f4efe6',  // Warm sand containers
  border: '#e7dfd2',         // Taupe dividers
  borderFocus: '#4f46e5',

  textPrimary: '#1c1917',    // Warm stone ink
  textSecondary: '#57534e',  // Muted warm gray
  textMuted: '#a8a29e',      // Fine captions
  textInverse: '#ffffff',

  success: '#059669',  warning: '#d97706',  danger: '#e11d48',  info: '#0369a1',
  violet: '#7c3aed',   star: '#f59e0b',
  // ...each with Light/Dark tint pairs
};

// DARK — Midnight Indigo with luminous accents
export const darkColors: Palette = {
  primary: '#a5b4fc',        // Luminous Indigo
  primaryLight: '#312e81',   // Indigo fill (dark tint)
  primaryDark: '#c7d2fe',    // Pale indigo text-on-fill

  secondary: '#fbbf24',      // Champagne Gold
  secondaryLight: '#451a03', // Deep amber fill
  secondaryDark: '#fcd34d',  // Pale gold text-on-fill

  background: '#0c101f',     // Deep midnight navy
  surface: '#151b2e',        // Card surface
  surfaceSubtle: '#1d2438',  // Raised containers
  border: '#2b3452',         // Muted indigo dividers
  borderFocus: '#a5b4fc',

  textPrimary: '#eef1f9',    // Near-white ink
  textSecondary: '#a9b1c9',  // Soft slate
  textMuted: '#6e7690',      // Dim captions
  textInverse: '#0c101f',
  // ...semantic tints inverted for dark surfaces
};
```

### 2.2 Theme Provider & Animated Switching

- **`ThemeProvider`** wraps the app and exposes `useTheme()` → `{ colors, isDark, mode, toggleTheme, setMode }`.
- Preference persists via AsyncStorage (`sahakari_theme_v1`); first launch follows the OS appearance (`Appearance.getColorScheme()`).
- **Switching animation**: a branded indigo/gold overlay cross-fades in (150 ms), the palette swaps in a single render pass behind it, then fades out (320 ms) — no visible color jump or text flash.
- **`ThemeToggle`** (Header + Login top bar): an animated springy sun/moon pill that rotates, scales, and slides the knob with haptic-style feedback.

### 2.3 Typography Scale

Designed for clarity on mobile screens under various lighting conditions:

| Token | Size | Line Height | Weight | Usage |
|---|---|---|---|---|
| `fontDisplay` | 26px | 34px | 700 (Bold) | Hero statistics |
| `fontHeadline` | 21px | 28px | 700 (Bold) | Screen headers, modal titles |
| `fontTitle` | 17px | 24px | 600 (SemiBold) | Section headers, card titles |
| `fontSubtitle` | 14px | 22px | 600 (SemiBold) | Subheaders, list item headers |
| `fontBody` | 13px | 20px | 400 (Regular) | Primary content, descriptions |
| `fontBodySm` | 12px | 18px | 400 (Regular) | Secondary captions, metadata |
| `fontCaption` | 10px | 16px | 500 (Medium) | Badges, tags, fine print |

### 2.4 Spacing & Layout Tokens

Standard 4px/8px modular grid:

- `spacing.xxs`: 2px
- `spacing.xs`: 4px
- `spacing.sm`: 8px
- `spacing.md`: 12px
- `spacing.lg`: 16px
- `spacing.xl`: 24px
- `spacing.xxl`: 32px
- `spacing.hero`: 48px

### 2.5 Border Radii

- `radii.xs`: 4px (Small tags)
- `radii.sm`: 8px (Buttons, text inputs)
- `radii.md`: 12px (Standard cards, modals)
- `radii.lg`: 16px (Featured cards, bottom sheets)
- `radii.full`: 9999px (Pills, avatar circles)

### 2.6 Shadows & Elevations

- **`shadows.card`**: Subtle elevation for standard list cards (`0px 2px 4px rgba(0, 0, 0, 0.06)`).
- **`shadows.modal`**: Deep elevation for bottom sheets and dialogs (`0px 8px 24px rgba(0, 0, 0, 0.12)`).
- **`shadows.floating`**: Soft glow for floating action buttons and CTAs (tinted with the active theme's primary).

---

## 3. UI Atoms & Component Specifications

### 3.1 Button (`Button.tsx`)
- **Variants**:
  - `primary`: Indigo background, inverse text. Primary call-to-action.
  - `secondary`: Gold background, inverse text. Secondary highlight action.
  - `outline`: Border colored with primary, transparent background. Cancel or alternate actions.
  - `danger`: Red background for destructive operations (Reject Worker, Cancel Job).
- **States**: Default, Pressed (scale + subtle opacity), Disabled (grayed out), Loading (spinner).
- All variants derive from `colors` tokens, so they adapt to the active theme automatically.

### 3.2 Card (`Card.tsx`)
- Card surface with rounded corners (`radii.md`), subtle themed border, and soft shadow.
- Standardized padding (`spacing.lg`).

### 3.3 Badge (`Badge.tsx`)
- Pill-shaped status indicator.
- Dynamic variants: `success` (Verified, Completed), `warning` (Pending, In-Progress), `danger` (Cancelled, Rejected), `info` (Assigned).

### 3.4 RatingStars (`RatingStars.tsx`)
- Interactive or read-only 5-star display.
- Golden stars (`star` token) with numeric average and review count label.

### 3.5 EmptyState (`EmptyState.tsx`)
- Clean illustration icon, bold title, supportive explanation, and optional action button.
- Used when search yields zero results, bookings list is empty, or pending queue is clear.

---

## 4. Accessibility & Touch Targets

- All interactive touch targets are a minimum of **44x44 points** per Apple HIG and Android Material guidelines.
- Color contrast ratio exceeds **4.5:1** for all body text against both light and dark backgrounds (dark-mode tints are specifically tuned for luminous-text contrast on midnight surfaces).
- High-contrast text on all buttons uses the theme's `textInverse` token, which inverts per theme for maximum readability.