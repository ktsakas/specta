# Svelte instead of React

Change `spec/` so a later compile implements Specta in **Svelte** rather than React. This file is a variant, not the product spec. Edit only `spec/` files; do not implement the app until compile runs.

## Stack replacements

In `spec.md` section 2 (Required Technology) and anywhere else in `spec/` that names the framework:

- Replace **React 19** with **Svelte 5**.
- Replace **Next.js 16 App Router** with **SvelteKit**.
- Replace **Lucide React** with **Lucide Svelte**.
- Replace Tiptap’s React bindings (`@tiptap/react`, `useEditor`, `EditorContent`, `BubbleMenu` from `@tiptap/react/menus`) with **Tiptap core** plus a **Svelte** editor integration that still provides a Notion-like selection bubble menu with the same commands.
- Replace App Router files such as `app/layout.tsx` and `app/page.tsx` with the SvelteKit layout and page equivalents (`src/routes/+layout.svelte`, `src/routes/+page.svelte`, or whatever SvelteKit uses in this repo once compile runs).
- If **Vinext** is React/Next-specific, replace it with SvelteKit + Vite and a Cloudflare adapter that still supports the same local-dev endpoints (`POST /api/spec`, `GET /api/spec-sources`, `GET /api/spec-review`, `POST /api/spec-commit`, `GET /api/variant-sources`, `GET /api/writing-rule-sources`). Keep TypeScript, Vite, the Sites plugin, and Node.js 22.13+.

## What must not change

Do not change product behavior, layout, persistence keys, seed rules, comments, spec review, variants, writing rules, visual system, or copy except where a React-specific API must be renamed to its Svelte counterpart. Keep Tiptap as the rich-text engine. Keep browser `localStorage`. Keep the document/comment data model.

## After editing spec/

Run the compile skill against the updated `spec/`.
