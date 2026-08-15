# Visual System

Colors, typography, and finish for Specta. See also the [Specta Product Spec](spec.md).

## Colors

Define these root variables:

```css
--ink: #282722;
--muted: #77746c;
--line: #e5e2da;
--paper: #fdfcf9;
--soft: #f4f2ec;
--accent: #e96a3b;
--accent-dark: #c64d24;
```

Additional important colors:

- Page surround: `#e8e6df`
- Left sidebar: `#f4f2ed`
- Comments panel: around `#f7f5ef`
- Bubble menu charcoal: `#252722`
- Saved-state green: around `#68806a`
- Comment highlight: a light orange wash with an orange underline

## Typography

- Use IBM Plex Sans, with Arial/Helvetica as fallback, for interface chrome, navigation, labels, buttons, and comment bodies.
- Use IBM Plex Serif, with Georgia/Times New Roman as fallback, for the document title, document body, and quoted comment spans.
- The large title is bold, 38–54 px using `clamp`, with tight negative letter spacing.
- Document body text is approximately 17 px with a 1.72 line-height.
- Use compact, uppercase, letter-spaced 10 px labels for metadata and section headings.

## Shape and finish

- Use restrained rounding: generally 4–8 px, except circular avatars.
- Use borders and subtle inset/accent rules more often than large cards.
- Use the orange accent for creation actions, active-document details, comment highlights, and the comments toggle when active.
- The editor surface is a continuous sheet, not a rounded card.
- The comments panel is a light companion column, not a dark chat drawer.
- Avoid gradients, excessive shadows, or dashboard-style tiles.
- Use Lucide icons instead of custom SVG markup.
