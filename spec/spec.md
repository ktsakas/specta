# Atlas Product Reconstruction Specification

## 1. Objective

Build **Atlas**, a single-page, Notion-inspired knowledge workspace that combines:

1. A collapsible document library on the left.
2. A distraction-free rich-text document editor in the center.
3. A document-aware chat/agent window on the right.

The product should feel calm, editorial, and highly focused. It is a working front-end prototype: documents and chats are stored locally in the browser, while the agent response is simulated and intentionally ready to be replaced by a real backend.

The finished page title is **“Atlas — Think, write, and work together”** and the product description is **“A focused knowledge workspace for documents and AI conversations.”**

## 2. Required Technology

Use the following stack:

- TypeScript
- React 19
- Next.js 16 App Router
- Vinext and Vite for the runtime/build pipeline
- Tiptap 3 for the rich-text editor
- `@tiptap/react`
- `@tiptap/react/menus`
- `@tiptap/starter-kit`
- `@tiptap/extension-placeholder`
- `@tiptap/pm`
- Lucide React for interface icons
- Plain global CSS, optionally loaded through Tailwind CSS using `@import "tailwindcss"`
- Browser `localStorage` for prototype persistence

Do not use an external database, authentication system, or live AI API in this version.

The Node.js engine must support version 22.13 or newer.

## 3. App Structure

Implement the experience as one client-rendered page.

Recommended files:

```text
app/
  layout.tsx
  page.tsx
  globals.css
public/
  og.png
.openai/
  hosting.json
```

`app/page.tsx` should begin with `"use client"` because it owns editor state, local persistence, and user interaction.

`app/layout.tsx` should provide page metadata, Open Graph metadata, X/Twitter card metadata, and the shared global stylesheet. Build the absolute `og.png` URL from the incoming request host and forwarded protocol.

## 4. Product Layout

### 4.1 Desktop

Fill the entire viewport with a three-column CSS grid:

```text
┌──────────────────────┬────────────────────────────────┬────────────────────────────┐
│ Document library     │ Document editor                │ Atlas agent                │
│ Light warm gray      │ Warm ivory paper              │ Dark charcoal              │
│ 262 px expanded      │ Flexible, minimum 470 px       │ 360 px                     │
│ 64 px collapsed      │                                │                            │
└──────────────────────┴────────────────────────────────┴────────────────────────────┘
```

Default grid columns:

```css
grid-template-columns: 262px minmax(470px, 1fr) 360px;
```

Collapsed grid columns:

```css
grid-template-columns: 64px minmax(470px, 1fr) 360px;
```

Animate the column-width change over about 180 ms. The page body must not scroll on desktop; each major panel controls its own overflow.

### 4.2 Medium widths

At widths up to 1080 px:

- Left panel: 230 px expanded or 64 px collapsed.
- Center: at least 420 px.
- Right panel: 320 px.
- Reduce the document canvas side margins.

At widths up to 820 px:

- Force the document rail to the compact 64 px presentation even if the explicit collapsed state is false.
- Show only document icons in the left rail.
- Hide the brand word, library heading, search, new-document button, document labels, overflow icons, and profile.
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

- Use Arial/Helvetica for interface chrome, navigation, labels, buttons, and chat.
- Use Georgia/Times New Roman for the document title and document body.
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

Use these TypeScript structures:

```ts
type DocumentRecord = {
  id: string;
  title: string;
  content: string;   // Tiptap HTML
  updatedAt: string; // Human-readable prototype label
};

type ChatMessage = {
  id: string;
  role: "assistant" | "user";
  text: string;
};

type ChatRecord = {
  id: string;
  title: string;
  messages: ChatMessage[];
};
```

Generate new IDs from a prefix, current timestamp, and short random base-36 suffix.

## 7. Seed Content

### 7.1 Documents

Seed three documents.

#### Product brief

- ID: `product-brief`
- Title: `Product brief`
- Updated label: `Just now`
- Content should include:
  - Date: `August 8, 2026`
  - H1: `Rethinking the knowledge workspace`
  - Introductory paragraph about building a calmer place for teams to think, write, and move work forward with an AI collaborator.
  - H2: `The opportunity`
  - Paragraph explaining that knowledge work is scattered across documents, chats, and disconnected tools.
  - Block quote: `What if every conversation could become a useful artifact—and every document could start a thoughtful conversation?`
  - H2: `Principles`
  - Bulleted list with bold leads:
    - `Keep the canvas quiet.`
    - `Make context portable.`
    - `Build for momentum.`

#### Research notes

- ID: `research-notes`
- Title: `Research notes`
- Updated label: `Yesterday`
- Include an H1 and a short instruction to capture useful signals, open questions, and patterns.

#### Weekly plan

- ID: `weekly-plan`
- Title: `Weekly plan`
- Updated label: `Aug 6`
- Include an H1, an H2 called `Priorities`, and bullets for refining the editor experience and reviewing early feedback.

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

- Show a 27 px square orange brand mark containing the serif letter `A`.
- Show the word `Atlas` when expanded.
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

- Filter titles case-insensitively from the search input.
- Each item has a hash icon in a small rectangular document tile.
- Each expanded item shows its title, updated label, and a subtle overflow icon.
- Active item uses a white background and a 2 px orange inset rule on the left.
- Clicking an item changes the active document and loads its stored Tiptap HTML.
- When collapsed, show only centered document-icon buttons and expose the title with the native `title` attribute.

### 8.4 Creating a document

Both plus controls call the same creation function. A new document must:

- Have a generated ID.
- Be titled `Untitled`.
- Use `Just now` as its updated label.
- Start with `<p></p>` content.
- Be prepended to the document array.
- Become active immediately.

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

Configure Tiptap with:

```ts
useEditor({
  immediatelyRender: false,
  extensions: [
    StarterKit,
    Placeholder.configure({ placeholder: "Start writing…" }),
  ],
  content: initialDocumentHtml,
});
```

Set the editable element class to `tiptap-editor` and give it `aria-label="Document content"`.

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

- Body: Georgia, 17 px, 1.72 line height.
- H1: approximately 31 px.
- H2: approximately 23 px.
- Add comfortable vertical spacing between blocks.
- Block quotes use a 3 px orange left border, italic text, and slightly larger type.
- Empty editor placeholder is muted and implemented through Tiptap’s `data-placeholder` attribute.

## 10. Right Agent Chat

The right side must be a dedicated conversation/agent window. Do not use it as a document list.

### 10.1 Header

- Dark charcoal background.
- Circular agent mark using the `Sparkles` icon in orange.
- Label: `Atlas agent`.
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
- Show `Atlas can make mistakes` in the lower-left.
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

Maintain these state values:

- `documents`
- `activeDocumentId`
- `chats`
- `activeChatId`
- `search`
- `chatInput`
- `saved`
- `ready`
- `leftCollapsed`

Use these local-storage keys:

```text
atlas-documents
atlas-chats
```

Hydration behavior:

- Read both values once after the component mounts.
- Wrap parsing in `try/catch` and retain seed data if parsing fails.
- Old chat records may not contain `messages`; normalize them to include the welcome message.
- Do not write the seed state back before hydration completes. Gate persistence behind a `ready` flag.

Persistence behavior:

- Save the full document array whenever it changes after hydration.
- Save the full chat array whenever it changes after hydration.
- After a document change, show `Saving…`, then switch to `Saved` after approximately 650 ms.
- Cancel the previous save-indicator timeout when another document update occurs.

The selected document, selected chat, search text, composer draft, and collapsed state do not need to persist across reloads.

## 12. Accessibility

The remake must include:

- Meaningful `aria-label` values for icon-only buttons.
- `aria-label="Documents"` for document navigation.
- `aria-label="Document title"` for the title input.
- `aria-label="Document content"` on the Tiptap editable surface.
- `aria-label="Current chat session"` for session selection.
- `aria-live="polite"` on the message list.
- Native button, input, textarea, select, and navigation elements.
- A native tooltip through `title` for collapsed document buttons.
- Visible hover/focus affordances with adequate contrast.
- Disabled styling for an empty send action.

## 13. Metadata and Social Preview

Use this metadata:

- Title: `Atlas — Think, write, and work together`
- Description: `A focused knowledge workspace for documents and AI conversations.`
- Open Graph title: `Atlas`
- Open Graph description: `Think, write, and work together.`
- X/Twitter card type: `summary_large_image`

The project’s `public/og.png` is a landscape editorial social card at approximately 1733 × 909. It has:

- Warm ivory paper background.
- A charcoal vertical strip on the left.
- Large serif `Atlas` wordmark.
- Tagline `Think, write, and work together.`.
- Layered document and chat motifs on the right.
- Burnt-orange accent blocks.

If recreating the asset rather than reusing it, preserve the exact displayed text and the visual palette above.

## 14. Intentional Prototype Limitations

Do not accidentally imply that the following are implemented:

- A real LLM or agent backend.
- Server-side document storage.
- Multi-user collaboration.
- Authentication or workspace membership.
- Sharing permissions.
- Document deletion, renaming from the sidebar, or folder hierarchy.
- Streaming responses.
- Attachments or file uploads.

The current app is intentionally a polished, locally persistent front-end prototype.

## 15. Build and Runtime Requirements

Required package scripts should provide development, production build, and start commands through Vinext. The project must retain a Vite configuration using:

- `vinext()`
- the Sites Vite plugin
- the Cloudflare Vite plugin
- Node.js compatibility flags

No D1 or R2 resources are required for this prototype; both bindings may remain `null` in `.openai/hosting.json`.

The final production build must succeed without TypeScript or bundling errors.

## 16. Recommended Implementation Order

1. Create the Next/Vinext project and install Tiptap plus Lucide.
2. Define document and chat data models and seed data.
3. Build the three-column desktop shell.
4. Implement the collapsible document sidebar and document selection/search/creation.
5. Configure Tiptap and document switching.
6. Add the selection-only bubble formatting menu.
7. Implement save state and local-storage hydration/persistence.
8. Build the right-side agent header, session selector, message history, prompt shortcuts, and composer.
9. Add new-chat and simulated-send behavior.
10. Apply the exact visual tokens and responsive breakpoints.
11. Add metadata and the social image.
12. Verify all acceptance criteria below.

## 17. Acceptance Criteria

The product is successfully reconstructed when all of these are true:

- The first viewport clearly shows documents on the left, an editor in the center, and a chat agent on the right.
- The left collapse button visibly changes the document rail between expanded and compact widths and can reverse the action.
- Creating a document adds it to the top, activates it, and allows immediate title/body editing.
- Searching filters documents by title without altering stored data.
- Switching documents preserves each document’s separate title and HTML content.
- Editing causes a visible `Saving…` to `Saved` transition.
- Reloading restores documents and chats from local storage.
- No permanent formatting bar is visible.
- Selecting text opens the Tiptap bubble menu, and every formatting action works.
- The right panel contains chat controls only, not document controls.
- A user can create a new chat and switch among chat sessions.
- Enter sends a message, Shift+Enter inserts a newline, and blank messages cannot be sent.
- New chats are renamed from the first submitted prompt.
- Chat and document state remain separate.
- Desktop, compact, and mobile layouts match the behavior described above.
- All icon-only controls have accessible labels.
- The production build completes successfully.

## 18. Final Product Character

The remake should not look like a generic admin dashboard. Atlas is an editorial writing environment with a quiet center canvas, a practical document rail, and a clearly separate dark agent surface. The writing is visually primary. Navigation is compact. Controls appear close to the action that needs them, especially the text-selection formatting menu. The design should feel closer to a thoughtful modern notebook than a conventional productivity dashboard.
