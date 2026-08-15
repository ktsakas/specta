# Specta Product Spec

## 1. Objective

Build **Specta**, a single-page, Notion-inspired knowledge workspace that combines:

1. A collapsible document library on the left.
2. A distraction-free rich-text document editor in the center.
3. Selection-based comments on the document, shown in a panel that opens beside the editor.

The product should feel calm, editorial, and highly focused. It is a working front-end prototype: documents and comments are stored locally in the browser. During local development, saving the `spec` document also writes `spec/spec.md` and its comment sidecar on disk.

The finished page title is **“Specta — Think, write, and work together”** and the product description is **“A focused knowledge workspace for documents and AI conversations.”**

## 2. Required Technology

Use the following stack:

- TypeScript
- React 19
- Next.js 16 App Router
- Vinext and Vite for the runtime/build pipeline
- Tiptap 3 for the rich-text editor
- Lucide React for interface icons
- Plain global CSS, optionally loaded through Tailwind CSS using `@import "tailwindcss"`
- Browser `localStorage` for prototype persistence of documents and comments
- During local `vinext dev`, saving the `spec` document also writes `spec/spec.md` and `spec/spec.comments.md` on disk

Do not use an external database, authentication system, or live AI API in this version.

The Node.js engine must support version 22.13 or newer.

## 3. App Structure

Implement the experience as one client-rendered page.

`app/layout.tsx` should provide page metadata, Open Graph metadata, X/Twitter card metadata, and the shared global stylesheet. Build the absolute `og.png` URL from the incoming request host and forwarded protocol.

## 4. Product Layout

### 4.1 Desktop

Fill the entire viewport with a two-column CSS grid:

```text
┌──────────────────────┬────────────────────────────────┐
│ Document library     │ Document editor                │
│ Light warm gray      │ Warm ivory paper              │
│ 262 px default       │ Flexible, minimum 470 px       │
│ resizable 180–520    │ Comments panel 300 px when     │
│ 64 px collapsed      │ open, inside this column       │
└──────────────────────┴────────────────────────────────┘
```

Default grid columns:

```css
grid-template-columns: var(--library-width, 262px) minmax(470px, 1fr);
```

Collapsed grid columns:

```css
grid-template-columns: 64px minmax(470px, 1fr);
```

The comments panel is not a permanent third column. It opens inside the editor column at 300 px and is closed by default.

While the library is expanded, the user can resize it:

- Default width is 262 px.
- Drag a vertical handle on the library’s right edge.
- Clamp between 180 px and 520 px, and also so the editor column can keep its 470 px minimum.
- Double-click the handle to reset to 262 px.
- ArrowLeft and ArrowRight on the handle nudge the width by 16 px.
- Hide the handle while the library is collapsed.
- While dragging, do not animate the grid.

Animate other column-width changes over about 180 ms. The page body must not scroll on desktop; each major panel controls its own overflow.

### 4.2 Medium widths

At widths up to 1080 px:

- Left panel: the current `--library-width` with a 230 px fallback, or 64 px collapsed.
- Center: at least 420 px.
- Reduce the document canvas side margins.

At widths up to 820 px:

- Force the document rail to the compact 64 px presentation even if the explicit collapsed state is false.
- Hide the resize handle.
- Show only document icons in the left rail.
- Hide the brand word, library heading, search, new-document button, document labels, filenames, overflow menu, and profile.
- Keep the center at least 360 px.

### 4.3 Mobile

At widths up to 680 px:

- Use a two-column layout: 54 px document rail plus the main content column.
- Keep the document rail sticky and viewport-height.
- When comments are open, stack the editor and comments vertically in the second grid column.
- Give the comments panel a top border instead of a left border, and cap its height at about half the viewport.
- Allow the page body to scroll.

## 5. Visual System

Colors, typography, and finish are specified in [Visual System](visual-system.md).

## 6. Data Model

Documents have an id, title, filename, Tiptap HTML content, and a human-readable updated label.

Comment threads belong to one document and have an id, the quoted text, short prefix and suffix context around that quote, an ISO created timestamp, an author name, a status of `open` or `orphaned`, a body, and a list of replies. Replies have an id, author, ISO created timestamp, and body.

Filenames are unique among documents and look like `spec.md` or `untitled.md`. They must end in `.md`, `.mdx`, or `.txt`. Sanitize user-entered names: strip slashes and illegal characters, and append `.md` if there is no allowed extension. If the desired name is already taken, append `-2`, `-3`, and so on.

Generate new IDs from a prefix, current timestamp, and short random base-36 suffix. New comment threads use the `cmt` prefix. New replies use the `cmt-r` prefix.

Comment sidecar filenames look like `spec.comments.md`. A file matching `*.comments.md`, `*.comments.mdx`, or `*.comments.txt` is a comment sidecar, not a library document.

## 7. Seed Content

### 7.1 Documents

The document library is sourced from files in the `spec/` folder. Do not seed fictional stand-ins such as Product brief, Research notes, or Weekly plan. Do not list comment sidecar files as documents.

On first load, or whenever stored documents are missing or unusable, seed the editor from `spec/`:

- ID derived from the filename stem (`spec`)
- Filename is the source file’s name (`spec.md`)
- Title from the file’s first H1 (`Specta Product Spec`), or the stem if none
- Updated label: `Just now`
- Content is the markdown converted to Tiptap HTML

If other `.md`, `.mdx`, or `.txt` files are present in `spec/` and they are not comment sidecars, list them as additional documents. `spec.md` is the default active document.

When stored documents are usable, still replace any record whose id matches a `spec/` source file with that source file’s current content, and keep user-created documents that are not in `spec/`.

When a new allowed file appears in `spec/`, add it to the library without requiring a full reload.

### 7.2 Comments

Seed comments from comment sidecar files in `spec/`. `spec.comments.md` is the sidecar for the `spec` document. Parse its threads and attach them to document id `spec`. If other sidecars exist, attach them to the document whose id is the sidecar stem (`visual-system.comments.md` → `visual-system`).

The prototype author name is `Kosta`.

## 8. Left Document Sidebar

The left side is always the document library.

### 8.1 Header

- Show a 27 px square orange brand mark containing the serif letter `S`.
- Show the word `Specta` when expanded.
- Place a collapse/expand button at the far right.
- Use `ChevronLeft` while expanded and `ChevronRight` while collapsed.
- The button must update a real `leftCollapsed` state.
- Apply a `left-collapsed` class to the outer workspace grid when true, changing the grid width from 262 px to 64 px.
- Give the control an accurate dynamic `aria-label`.

### 8.2 Expanded library

Show:

- Small uppercase `Library` label.
- Serif heading `Documents`.
- Small plus icon for document creation.
- Search field with a search icon and placeholder `Search documents`.
- Full-width orange `New document` button.
- Small `All documents` section label with a chevron.
- Scrollable document list.
- Profile row at the bottom with initials `KL`, name `Kosta`, and subtitle `Personal workspace`.

### 8.3 Document list behavior

- Filter titles and filenames case-insensitively from the search input.
- Each item has a hash icon in a small rectangular document tile.
- Each expanded item shows its title, an editable filename under the title, and a subtle overflow icon.
- The overflow icon opens a small menu whose only action is `Delete`.
- Active item uses a white background and a 2 px orange inset rule on the left.
- Clicking the title row changes the active document and loads its stored Tiptap HTML.
- Editing the filename updates that document’s filename on blur or Enter. It does not change the document title.
- When collapsed, show only centered document-icon buttons and expose the title with the native `title` attribute.

### 8.4 Creating a document

Both plus controls call the same creation function. A new document must:

- Have a generated ID.
- Be titled `Untitled`.
- Use a unique filename starting from `untitled.md`.
- Use `Just now` as its updated label.
- Start with `<p></p>` content.
- Be prepended to the document array.
- Become active immediately.

### 8.5 Deleting a document

Choosing `Delete` from the overflow menu:

- Removes that document from the array.
- Removes that document’s comments.
- If it was the active document, select `spec` if it is still present, otherwise the first remaining document.
- If the library would be empty, create one blank Untitled document and select it.

## 9. Center Document Editor

### 9.1 Canvas

- Do not render a top application bar or persistent formatting toolbar.
- The center column should be an uninterrupted warm-ivory writing surface.
- Center a document canvas with a maximum width of 760 px.
- Use generous top and bottom padding, approximately 72 px and 150 px.

At the top of the canvas, show one small inline metadata row, not a bar:

- Left: file icon plus `Working document` in orange.
- Right: a comments toggle, then `Saving…` while edits are settling, then a green check and `Saved`.

The comments toggle uses a message-square icon. If the active document has no threads, its label is `Comments`. If it has threads, show the count instead of the word. The control is pressed while the comments panel is open. Closing the panel while a draft is open cancels that draft.

Below the metadata row, show a borderless title input. The title must update the active document as the user types.

### 9.2 Tiptap setup

Use the placeholder `Start writing…`. Set the editable element class to `tiptap-editor` and give it `aria-label="Document content"`.

Include a comment mark that wraps highlighted ranges in `span[data-comment-id]` with class `comment-mark`. Convert those marks back to plain text when writing markdown; comments live in the sidecar, not inline in the document file.

On each editor update:

1. Get HTML with `editor.getHTML()`.
2. Replace the active document’s `content`.
3. Set `updatedAt` to `Just now`.
4. Set the save indicator to unsaved.
5. Refresh each thread’s quote/prefix/suffix from its current mark, or mark the thread `orphaned` if the range is gone.

Keep the current active document ID in a ref so Tiptap’s update callback never writes into a previously active document because of a stale closure.

When the active document changes, use `editor.commands.setContent(document.content, { emitUpdate: false })` if the editor HTML differs. This prevents the document-switch operation from being mistaken for a user edit. Do not replace editor content while a comment draft is open.

After loading a document, re-attach any missing comment marks by searching the document text for each thread’s quote, using the stored prefix and suffix to pick the best match.

Clicking a comment mark opens the comments panel and selects that thread. Selecting a thread also scrolls its highlight into view.

If the user clicks a link whose last path segment looks like `{stem}.md`, `{stem}.mdx`, or `{stem}.txt`, and a library document with that id exists, open that document instead of navigating away. This is how `spec.md` links such as [Visual System](visual-system.md) switch documents.

### 9.3 Selection-based formatting

Use `BubbleMenu` from `@tiptap/react/menus`. The menu appears only when the user selects text, similar to Notion. There must be no permanently visible editor toolbar.

Position the menu above the selection with roughly a 10 px offset. Style it as a compact charcoal floating strip with a subtle shadow.

Include these commands:

- Bold
- Italic
- Strikethrough
- Heading 1
- Heading 2
- Bullet list
- Numbered list
- Block quote
- Comment

Use corresponding Lucide icons and reflect each formatting command’s active state. The Comment action starts a new comment from the current selection.

### 9.4 Editor typography

- H1: approximately 31 px.
- H2: approximately 23 px.
- Block quotes use a 3 px orange left border, italic text, and slightly larger type.

## 10. Comments

Comments are a document-scoped review surface, not a chat or agent window.

### 10.1 Opening the panel

The panel is closed by default. Open it from the metadata comments toggle, from the bubble-menu Comment action, or by clicking an existing highlight. Closing it from the panel’s close button, the toggle, or by clearing the active comment hides the panel.

Switching documents clears the open draft and the selected thread.

### 10.2 Panel chrome

- Light warm paper, not the dark agent treatment.
- Header label `Comments`, with ` · {count}` when there are threads.
- Close button with `aria-label="Close comments"`.
- The panel itself has `aria-label="Comments"`.

### 10.3 Starting a comment

The bubble-menu Comment action, when the selection is non-empty and has text:

1. Capture the selected quote plus about 32 characters of prefix and suffix.
2. Generate a thread id.
3. Apply the comment mark to the selection immediately.
4. Open the panel with a draft composer.

Do nothing if the selection is empty or has no text.

The draft composer:

- Shows the quoted text.
- Uses placeholder `Write a comment…`.
- Submit button label `Comment`.
- `Cancel` removes the draft mark and closes the draft.
- Escape cancels.
- Enter submits. Shift+Enter inserts a newline.
- Ignore a blank body.

On submit, prepend an `open` thread authored by `Kosta` with an empty replies list, keep the mark, and select the new thread.

### 10.4 Thread list

Each saved thread shows:

- The quoted text in serif italics, wrapped in quotation marks.
- A `Detached` badge and fallback copy `The original text is no longer in this document.` when status is `orphaned`.
- Author `Kosta`, a relative time (`Just now`, `5m ago`, `3h ago`, or a short date), the body, and a delete control labeled `Delete comment`.
- Replies indented under the parent, each with the same author/time/body treatment and `Delete reply`.
- A reply composer with placeholder `Reply…` and submit label `Reply`. Same Enter / Shift+Enter rules as the draft composer.

The selected thread uses the same 2 px orange inset rule as the active document. Clicking a thread selects it and scrolls to the highlight.

Empty state, when there is no draft and no threads:

> Select text in the document to leave a comment.

Deleting a thread removes its mark from the editor. Deleting the last thread does not force the panel closed.

### 10.5 Sidecar format

Serialize comments as markdown. Example shape:

```markdown
# Comments for spec.md

## cmt-exampleid

- quote: quoted span
- prefix: text before
- suffix: text after
- created: 2026-08-15T14:59:02.178Z
- author: Kosta
- status: open

Comment body.

### cmt-r-exampleid

- created: 2026-08-15T15:01:00.000Z
- author: Kosta

Reply body.
```

Flatten quote/prefix/suffix to a single line. Preserve body text after the metadata block.

## 11. Client State and Persistence

Use these local-storage keys:

```text
specta-documents
specta-comments
```

`specta-comments` is a map of document id to sidecar markdown, not an array of chat sessions. Do not read or write `specta-chats`.

Hydration behavior:

- Read both values once after the component mounts.
- Wrap parsing in `try/catch` and retain seed data if parsing fails.
- Old document records may not contain `filename`; derive one from the id (`{id}.md`, sanitized).
- Start from comment sidecars bundled with `spec/`, then overlay any valid stored markdown for those document ids.
- If the stored comments value is not a string map, keep the sidecar seed.
- Do not write the seed state back before hydration completes. Gate persistence behind a `ready` flag.

Persistence behavior:

- Save the full document array whenever it changes after hydration.
- Save the comments map whenever documents or threads change after hydration.
- After a document or comment change, show `Saving…`, then switch to `Saved` after approximately 650 ms.
- Cancel the previous save-indicator timeout when another update occurs.

Local `spec.md` write-back (development only):

- After that same save debounce, if the changed document id is `spec` and no comment draft is open, `POST /api/spec` with `{ title, content, comments }`.
- Convert the document’s Tiptap HTML back to markdown, keep the title as the first H1, and write `spec/spec.md`.
- Write `comments` to `spec/spec.comments.md`. If there are no threads (empty sidecar or heading-only file), delete `spec/spec.comments.md` instead of leaving an empty file.
- Handle the write on the Vite dev host, not inside the Cloudflare worker. The worker filesystem is not the project directory.
- Untitled documents and any other library items stay in `localStorage` only. Their comments stay in `localStorage` only.
- Renaming the `spec` document’s displayed filename does not change the write path. Disk write-back is keyed by document id `spec` and always targets `spec/spec.md` and `spec/spec.comments.md`.
- Do not POST while a comment draft is open. Wait until the draft is submitted or cancelled so an in-progress mark is not flushed.
- This write-back is a local-dev convenience. It is not hosted multi-user storage.

The selected document, selected comment, comments-panel open state, comment draft, search text, collapsed state, and library width do not need to persist across reloads.

## 12. Accessibility

The remake must include:

- `aria-label="Documents"` for document navigation.
- `aria-label="Document title"` for the title input.
- `aria-label="Document content"` on the Tiptap editable surface.
- `aria-label="Filename for {title}"` for the sidebar filename input.
- `aria-label="Actions for {title}"` for the document overflow button.
- `aria-label="Resize document library"` for the library resize handle.
- `aria-label="Comments"` on the comments panel.
- `aria-label="Close comments"` on the panel close button.
- `aria-label="Comments"` or `aria-label="Comments, {count}"` on the metadata toggle.
- `aria-label="Comment"` on the bubble-menu comment action.
- `aria-label="Write a comment…"` and `aria-label="Reply…"` on the composers.
- `aria-label="Delete comment"` and `aria-label="Delete reply"` on delete controls.
- A native tooltip through `title` for collapsed document buttons.

## 13. Metadata and Social Preview

Use this metadata:

- Title: `Specta — Think, write, and work together`
- Description: `A focused knowledge workspace for documents and AI conversations.`
- Open Graph title: `Specta`
- Open Graph description: `Think, write, and work together.`
- X/Twitter card type: `summary_large_image`

The project’s `public/og.png` is a landscape editorial social card at approximately 1733 × 909. It has:

- Warm ivory paper background.
- A charcoal vertical strip on the left.
- Large serif `Specta` wordmark.
- Tagline `Think, write, and work together.`.
- Layered document and chat motifs on the right.
- Burnt-orange accent blocks.

If recreating the asset rather than reusing it, preserve the exact displayed text and the visual palette above.

## 14. Intentional Prototype Limitations

Do not accidentally imply that the following are implemented:

- A chat window, LLM, or agent backend.
- Hosted or multi-user document storage, an external database, or cloud file sync.
- Multi-user collaboration or live presence. Comments are local to this browser, authored as `Kosta`.
- Authentication or workspace membership.
- Sharing permissions.
- Folder hierarchy.
- Renaming the document title from the sidebar. The sidebar edits the filename only; the title is edited in the document canvas.
- Streaming responses.
- Attachments or file uploads.

The current app is a polished front-end prototype. Documents and comments persist in `localStorage`. During local development, saving the `spec` document also updates `spec/spec.md` and `spec/spec.comments.md` on disk.

## 15. Build and Runtime Requirements

Required package scripts should provide development, production build, and start commands through Vinext. The project must retain a Vite configuration using:

- `specWrite()` so `POST /api/spec` writes `spec/spec.md` and `spec/spec.comments.md` from the Vite host during `vinext dev`
- `vinext()`
- the Sites Vite plugin
- the Cloudflare Vite plugin
- Node.js compatibility flags

No D1 or R2 resources are required for this prototype; both bindings may remain `null` in `.openai/hosting.json`.

The final production build must succeed without TypeScript or bundling errors.
