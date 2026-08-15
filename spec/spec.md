# Specta Product Reconstruction Specification

## 1. Objective

Build **Specta**, a single-page, Notion-inspired knowledge workspace that combines:

1. A collapsible document library on the left.
2. A distraction-free rich-text document editor in the center.
3. A document-aware chat/agent window on the right.

The product should feel calm, editorial, and highly focused. It is a working front-end prototype: documents and chats are stored locally in the browser, while the agent response is simulated and intentionally ready to be replaced by a real backend.

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
- Browser `localStorage` for prototype persistence of documents and chats
- During local `vinext dev`, saving the `spec` document also writes `spec/spec.md` on disk

Do not use an external database, authentication system, or live AI API in this version.

The Node.js engine must support version 22.13 or newer.

## 3. App Structure

Implement the experience as one client-rendered page.

`app/layout.tsx` should provide page metadata, Open Graph metadata, X/Twitter card metadata, and the shared global stylesheet. Build the absolute `og.png` URL from the incoming request host and forwarded protocol.

## 4. Product Layout

### 4.1 Desktop

Fill the entire viewport with a three-column CSS grid:

```text
┌──────────────────────┬────────────────────────────────┬────────────────────────────┐
│ Document library     │ Document editor                │ Specta agent                │
│ Light warm gray      │ Warm ivory paper              │ Dark charcoal              │
│ 262 px default       │ Flexible, minimum 470 px       │ 360 px                     │
│ resizable 180–520    │                                │                            │
│ 64 px collapsed      │                                │                            │
└──────────────────────┴────────────────────────────────┴────────────────────────────┘
```

Default grid columns:

```css
grid-template-columns: var(--library-width, 262px) minmax(470px, 1fr) 360px;
```

Collapsed grid columns:

```css
grid-template-columns: 64px minmax(470px, 1fr) 360px;
```

While the library is expanded, the user can resize it:

- Default width is 262 px.
- Drag a vertical handle on the library’s right edge.
- Clamp between 180 px and 520 px, and also so the center and agent columns can keep their minimums.
- Double-click the handle to reset to 262 px.
- ArrowLeft and ArrowRight on the handle nudge the width by 16 px.
- Hide the handle while the library is collapsed.
- While dragging, do not animate the grid.

Animate other column-width changes over about 180 ms. The page body must not scroll on desktop; each major panel controls its own overflow.

### 4.2 Medium widths

At widths up to 1080 px:

- Left panel: the current `--library-width` with a 230 px fallback, or 64 px collapsed.
- Center: at least 420 px.
- Right panel: 320 px.
- Reduce the document canvas side margins.

At widths up to 820 px:

- Force the document rail to the compact 64 px presentation even if the explicit collapsed state is false.
- Hide the resize handle.
- Show only document icons in the left rail.
- Hide the brand word, library heading, search, new-document button, document labels, filenames, overflow menu, and profile.
- Keep the center at least 360 px and the agent panel at 300 px.

### 4.3 Mobile

At widths up to 680 px:

- Use a two-column layout: 54 px document rail plus the main content column.
- Keep the document rail sticky and viewport-height.
- Stack the editor and chat vertically in the second grid column.
- Give the editor a minimum height of approximately 520 px.
- Give the chat a minimum height of approximately 540 px.
- Allow the page body to scroll.
- Remove the chat’s left border and replace it with a top border.

## 5. Visual System

### 5.1 Colors

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
- Right chat panel: `#252722`
- Chat secondary surface: around `#30322d`
- Dark separators: around `#3a3c36`
- Saved-state green: around `#68806a`

### 5.2 Typography

- Use IBM Plex Sans, with Arial/Helvetica as fallback, for interface chrome, navigation, labels, buttons, and chat.
- Use IBM Plex Serif, with Georgia/Times New Roman as fallback, for the document title and document body.
- The large title is bold, 38–54 px using `clamp`, with tight negative letter spacing.
- Document body text is approximately 17 px with a 1.72 line-height.
- Use compact, uppercase, letter-spaced 10 px labels for metadata and section headings.

### 5.3 Shape and finish

- Use restrained rounding: generally 4–8 px, except circular avatars.
- Use borders and subtle inset/accent rules more often than large cards.
- Use the orange accent for creation actions, active-document details, and the agent icon.
- The editor surface is a continuous sheet, not a rounded card.
- Avoid gradients, excessive shadows, or dashboard-style tiles.
- Use Lucide icons instead of custom SVG markup.

## 6. Data Model

Documents have an id, title, filename, Tiptap HTML content, and a human-readable updated label. Chats have an id, title, and a list of user or assistant messages.

Filenames are unique among documents and look like `spec.md` or `untitled.md`. They must end in `.md`, `.mdx`, or `.txt`. Sanitize user-entered names: strip slashes and illegal characters, and append `.md` if there is no allowed extension. If the desired name is already taken, append `-2`, `-3`, and so on.

Generate new IDs from a prefix, current timestamp, and short random base-36 suffix.

## 7. Seed Content

### 7.1 Documents

The document library is sourced from files in the `spec/` folder. Do not seed fictional stand-ins such as Product brief, Research notes, or Weekly plan.

On first load, or whenever stored documents are missing or unusable, seed the editor with `spec/spec.md`:

- ID derived from the filename stem (`spec`)
- Filename is the source file’s name (`spec.md`)
- Title from the file’s first H1 (`Specta Product Reconstruction Specification`), or `spec` if none
- Updated label: `Just now`
- Content is the markdown of `spec/spec.md` converted to Tiptap HTML

If other `.md`, `.mdx`, or `.txt` files are present in `spec/`, list them as additional documents. `spec.md` is the default active document.

### 7.2 Chat sessions

Seed three sessions:

- `Launch positioning`
- `Interview synthesis`
- `Q3 roadmap ideas`

The initial assistant message should say:

> I’m ready to help with this document. Ask me to summarize it, challenge an idea, or draft the next section.

The launch-positioning session should also contain this exchange:

- User: `What feels strongest in this brief?`
- Assistant: `The clearest idea is the bridge between conversation and documentation. I’d make that the center of the positioning.`

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
- Right: `Saving…` while edits are settling, then a green check and `Saved`.

Below it, show a borderless title input. The title must update the active document as the user types.

### 9.2 Tiptap setup

Use the placeholder `Start writing…`. Set the editable element class to `tiptap-editor` and give it `aria-label="Document content"`.

On each editor update:

1. Get HTML with `editor.getHTML()`.
2. Replace the active document’s `content`.
3. Set `updatedAt` to `Just now`.
4. Set the save indicator to unsaved.

Keep the current active document ID in a ref so Tiptap’s update callback never writes into a previously active document because of a stale closure.

When the active document changes, use `editor.commands.setContent(document.content, { emitUpdate: false })` if the editor HTML differs. This prevents the document-switch operation from being mistaken for a user edit.

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

Use corresponding Lucide icons and reflect each command’s active state.

### 9.4 Editor typography

- H1: approximately 31 px.
- H2: approximately 23 px.
- Block quotes use a 3 px orange left border, italic text, and slightly larger type.

## 10. Right Agent Chat

The right side must be a dedicated conversation/agent window. Do not use it as a document list.

### 10.1 Header

- Dark charcoal background.
- Circular agent mark using the `Sparkles` icon in orange.
- Label: `Specta agent`.
- Subtitle: `Uses this document`.
- A plus button on the right starts a new chat and has accessible text/title `New chat` or `Start a new chat`.

### 10.2 Session selection

- Directly below the header, show a compact dark select control.
- Populate it with all chat session titles.
- Selecting an option changes the visible session.
- Overlay a small `ChevronDown` icon at the right while retaining a native accessible `<select>`.

### 10.3 Messages

- The message history is the only flexible scrolling area in this panel.
- Assistant messages align left and include a small circular `Bot` avatar.
- User messages align right in a modest charcoal bubble.
- Do not place assistant responses inside large cards.
- Give the message list `aria-live="polite"`.

### 10.4 Prompt shortcuts

Above the composer, render two small outlined buttons:

- `Summarize`, which fills the composer with `Summarize this document`.
- `Suggest edits`, which fills the composer with `What should I improve?`.

The shortcut fills the input but does not automatically submit it.

### 10.5 Composer

- Use a dark bordered form containing a 3-row textarea.
- Placeholder: `Ask about this document…`.
- Enter submits.
- Shift+Enter creates a newline.
- Show `Specta can make mistakes` in the lower-left.
- Show a small orange send button in the lower-right.
- Disable the send button when the trimmed input is empty.

On submit:

1. Ignore blank input.
2. Append the user message to the active session.
3. Append a simulated assistant reply that references the active document title and explains that the prototype is ready for an agent backend.
4. If the chat was called `New conversation`, rename it to the first 34 characters of the user’s first message.
5. Clear the textarea.

The simulated response pattern may be:

```text
I’ll use “{document title}” as context. This prototype has captured your request and is ready to connect to an agent backend.
```

### 10.6 Starting a new chat

Create a new record that:

- Has a generated ID.
- Is titled `New conversation`.
- Contains a fresh copy of the welcome assistant message with a unique message ID.
- Is prepended to the chat array.
- Becomes active immediately.
- Clears the current composer input.

## 11. Client State and Persistence

Use these local-storage keys:

```text
specta-documents
specta-chats
```

Hydration behavior:

- Read both values once after the component mounts.
- Wrap parsing in `try/catch` and retain seed data if parsing fails.
- Old document records may not contain `filename`; derive one from the id (`{id}.md`, sanitized).
- Old chat records may not contain `messages`; normalize them to include the welcome message.
- Do not write the seed state back before hydration completes. Gate persistence behind a `ready` flag.

Persistence behavior:

- Save the full document array whenever it changes after hydration.
- Save the full chat array whenever it changes after hydration.
- After a document change, show `Saving…`, then switch to `Saved` after approximately 650 ms.
- Cancel the previous save-indicator timeout when another document update occurs.

Local `spec.md` write-back (development only):

- After that same save debounce, if the changed document id is `spec`, `POST /api/spec` with `{ title, content }`.
- Convert the document’s Tiptap HTML back to markdown, keep the title as the first H1, and write `spec/spec.md`.
- Handle the write on the Vite dev host, not inside the Cloudflare worker. The worker filesystem is not the project directory.
- Untitled documents and any other library items stay in `localStorage` only.
- Renaming the `spec` document’s displayed filename does not change the write path. Disk write-back is keyed by document id `spec` and always targets `spec/spec.md`.
- This write-back is a local-dev convenience. It is not hosted multi-user storage.

The selected document, selected chat, search text, composer draft, collapsed state, and library width do not need to persist across reloads.

## 12. Accessibility

The remake must include:

- `aria-label="Documents"` for document navigation.
- `aria-label="Document title"` for the title input.
- `aria-label="Document content"` on the Tiptap editable surface.
- `aria-label="Filename for {title}"` for the sidebar filename input.
- `aria-label="Actions for {title}"` for the document overflow button.
- `aria-label="Resize document library"` for the library resize handle.
- `aria-label="Current chat session"` for session selection.
- `aria-live="polite"` on the message list.
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

- A real LLM or agent backend.
- Hosted or multi-user document storage, an external database, or cloud file sync.
- Multi-user collaboration.
- Authentication or workspace membership.
- Sharing permissions.
- Folder hierarchy.
- Renaming the document title from the sidebar. The sidebar edits the filename only; the title is edited in the document canvas.
- Streaming responses.
- Attachments or file uploads.

The current app is a polished front-end prototype. Documents and chats persist in `localStorage`. During local development, saving the `spec` document also updates `spec/spec.md` on disk.

## 15. Build and Runtime Requirements

Required package scripts should provide development, production build, and start commands through Vinext. The project must retain a Vite configuration using:

- `specWrite()` so `POST /api/spec` writes `spec/spec.md` from the Vite host during `vinext dev`
- `vinext()`
- the Sites Vite plugin
- the Cloudflare Vite plugin
- Node.js compatibility flags

No D1 or R2 resources are required for this prototype; both bindings may remain `null` in `.openai/hosting.json`.

The final production build must succeed without TypeScript or bundling errors.
