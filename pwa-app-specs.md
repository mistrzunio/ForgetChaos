# ForgetChaos PWA MVP Development Plan

## Tech Stack

- **Framework**: React 18+ with TypeScript
- **Build Tool**: Vite with PWA plugin (`vite-plugin-pwa`)
- **Block Editor**: Lexical (Meta's modern, performant editor framework)
- **Storage**: IndexedDB (via `idb` library) for local-first data persistence
- **Styling**: CSS Modules or Tailwind CSS (minimal, dark theme)
- **State Management**: React Context + hooks (lightweight for MVP)
- **Keyboard Shortcuts**: Custom hook using `use-hotkeys` or native keyboard events

## Project Structure

```
forget-chaos/
├── src/
│   ├── components/
│   │   ├── Editor/
│   │   │   ├── BlockEditor.tsx          # Main Lexical editor wrapper
│   │   │   ├── BlockNode.tsx            # Custom block node component
│   │   │   ├── SlashCommand.tsx         # Slash command menu (/)
│   │   │   └── Toolbar.tsx              # Formatting toolbar (optional, minimal)
│   │   ├── Sidebar/
│   │   │   ├── Sidebar.tsx              # Collapsible sidebar
│   │   │   ├── PageTree.tsx             # Tree view of pages
│   │   │   └── PageNode.tsx             # Individual page node
│   │   ├── CommandPalette/
│   │   │   └── CommandPalette.tsx      # Cmd+K command palette
│   │   ├── Layout/
│   │   │   ├── Header.tsx               # Minimal header (breadcrumbs, search)
│   │   │   ├── StatusBar.tsx            # Bottom status bar
│   │   │   └── AppLayout.tsx            # Main layout wrapper
│   │   └── KeyboardShortcuts/
│   │       └── ShortcutHelp.tsx         # Cmd+/ help modal
│   ├── hooks/
│   │   ├── useKeyboardShortcuts.ts      # Global keyboard shortcut handler
│   │   ├── useCommandMode.ts            # Vim-like command mode state
│   │   ├── useLocalStorage.ts           # IndexedDB wrapper hook
│   │   └── useCommandPalette.ts         # Command palette state/logic
│   ├── services/
│   │   ├── storage/
│   │   │   ├── db.ts                    # IndexedDB setup & schema
│   │   │   ├── pages.ts                 # Page CRUD operations
│   │   │   └── blocks.ts                # Block CRUD operations
│   │   └── editor/
│   │       └── blockTypes.ts            # Block type definitions
│   ├── types/
│   │   ├── page.ts                      # Page data model
│   │   ├── block.ts                     # Block data model
│   │   └── commands.ts                  # Command type definitions
│   ├── utils/
│   │   ├── keyboard.ts                  # Keyboard shortcut utilities
│   │   └── commands.ts                  # Command registry
│   ├── styles/
│   │   ├── theme.css                    # Dark theme variables
│   │   └── global.css                   # Global styles
│   ├── App.tsx                          # Root component
│   └── main.tsx                         # Entry point
├── public/
│   ├── manifest.json                    # PWA manifest
│   └── icons/                           # PWA icons (various sizes)
├── vite.config.ts                       # Vite + PWA config
├── tsconfig.json
└── package.json
```

## Implementation Steps

### Phase 1: Project Setup & Foundation

1. **Initialize Vite + React + TypeScript project**

   - Set up Vite with React template
   - Configure TypeScript strict mode
   - Install core dependencies: `lexical`, `@lexical/react`, `idb`, `vite-plugin-pwa`

2. **Configure PWA**

   - Set up `vite-plugin-pwa` in `vite.config.ts`
   - Create `manifest.json` with app metadata, icons, display mode
   - Configure service worker for offline support
   - Add PWA icons (192x192, 512x512 minimum)

3. **Set up IndexedDB schema**

   - Create `db.ts` with database initialization
   - Define stores: `pages`, `blocks`, `settings`
   - Implement migration system for future schema changes

4. **Create data models**

   - `Page` type: id, title, parentId, createdAt, updatedAt
   - `Block` type: id, pageId, type, content, order, parentId (for nested blocks)
   - `Command` type: id, name, shortcut, handler

### Phase 2: Core UI Components

5. **Build AppLayout component**

   - Three-column layout: Sidebar | Main Content | (optional right panel)
   - Responsive sidebar toggle
   - Header bar (minimal, auto-hide on focus)

6. **Implement Sidebar**

   - Tree view of pages using recursive component
   - Keyboard navigation (arrow keys, Enter to open)
   - Expand/collapse with `Cmd+←`/`Cmd+→`
   - Create page button (`Cmd+N`)

7. **Create Header component**

   - Breadcrumb navigation (`Cmd+↑` to parent)
   - Search input (`Cmd+P`)
   - Command palette trigger (`Cmd+K`)
   - Auto-hide when typing (`Cmd+Shift+H`)

8. **Build StatusBar component**

   - Current mode indicator (Normal/Command/Visual)
   - Unsaved changes indicator (`*`)
   - Word count
   - Sync status (local-only for MVP)

### Phase 3: Block Editor Integration

9. **Set up Lexical editor**

   - Initialize Lexical editor instance
   - Configure plugins: RichTextPlugin, HistoryPlugin, ListPlugin
   - Create custom block node types (heading, paragraph, list, code, etc.)

10. **Implement block-based editing**

    - Render blocks as separate Lexical editors or custom nodes
    - Keyboard navigation between blocks (`↑`/`↓`)
    - Block creation (`Cmd+Enter` for new block below)
    - Block deletion (`Cmd+Delete`)

11. **Add slash command menu**

    - Trigger on `/` keypress
    - Show block type menu (heading, list, code, etc.)
    - Filter as user types
    - Insert selected block type

12. **Implement text formatting**

    - Bold (`Cmd+B`), Italic (`Cmd+I`), Underline (`Cmd+U`)
    - Code (`Cmd+Shift+E`), Code block (`Cmd+Shift+M`)
    - Link (`Cmd+K` when text selected)

### Phase 4: Keyboard Shortcut System

13. **Create keyboard shortcut handler**

    - Global `useKeyboardShortcuts` hook
    - Register shortcuts with modifiers (Cmd, Shift, etc.)
    - Context-aware shortcuts (different in editor vs sidebar)
    - Prevent browser defaults for app shortcuts

14. **Implement command mode**

    - Toggle with `Esc` or `Cmd+:`
    - Vim-style navigation (`hjkl`)
    - Visual mode (`v` in command mode)
    - Command line (`:` in command mode)

15. **Build command palette**

    - Modal overlay triggered by `Cmd+K`
    - Fuzzy search through commands
    - Keyboard navigation (`↑`/`↓`, `Enter`)
    - Recent commands at top
    - Commands: Create page, Search, Settings, Export, etc.

16. **Add keyboard shortcuts help**

    - Modal triggered by `Cmd+/`
    - Categorized list of shortcuts
    - Searchable/filterable

### Phase 5: Data Persistence & State

17. **Implement page management**

    - Create/read/update/delete pages in IndexedDB
    - Load page tree on app start
    - Auto-save current page on changes

18. **Implement block persistence**

    - Save blocks to IndexedDB on editor changes
    - Load blocks when opening page
    - Debounce saves for performance

19. **Add undo/redo**

    - Use Lexical's HistoryPlugin
    - Keyboard shortcuts (`Cmd+Z`, `Cmd+Shift+Z`)
    - Persist history to IndexedDB (optional, advanced)

### Phase 6: Styling & Polish

20. **Apply dark theme**

    - CSS variables for colors (Omarchy-inspired)
    - Subtle grays, muted accents
    - High contrast text
    - Smooth transitions

21. **Add visual feedback**

    - Brief highlight on keyboard actions
    - Smooth sidebar transitions
    - Loading states
    - Focus indicators

22. **Implement responsive design**

    - Mobile-friendly sidebar (overlay on small screens)
    - Touch support (secondary to keyboard)
    - iPad optimization

### Phase 7: PWA Features

23. **Service worker configuration**

    - Cache static assets
    - Offline fallback page
    - Background sync preparation (for future)

24. **Add install prompt**

    - Detect installability
    - Show install button in UI
    - Handle install event

## Key Files to Create

- `vite.config.ts`: Vite + PWA plugin configuration
- `src/services/storage/db.ts`: IndexedDB schema and initialization
- `src/hooks/useKeyboardShortcuts.ts`: Global keyboard handler
- `src/components/Editor/BlockEditor.tsx`: Lexical editor wrapper
- `src/components/Sidebar/Sidebar.tsx`: Page tree sidebar
- `src/components/CommandPalette/CommandPalette.tsx`: Command palette modal
- `public/manifest.json`: PWA manifest

## Dependencies

```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "lexical": "^0.12.0",
    "@lexical/react": "^0.12.0",
    "@lexical/rich-text": "^0.12.0",
    "@lexical/list": "^0.12.0",
    "@lexical/history": "^0.12.0",
    "idb": "^7.1.1"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0",
    "vite-plugin-pwa": "^0.17.0"
  }
}
```

## MVP Scope Limitations

- **Not included**: Database views, advanced sync, plugins, export formats (except basic)
- **Simplified**: Block types limited to core set (headings, lists, code, paragraph)
- **Future**: Backend sync, conflict resolution, advanced block types, collaboration