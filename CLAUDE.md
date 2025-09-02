# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the Obsidian Kanban Plugin, which creates markdown-backed Kanban boards within Obsidian. The plugin allows users to create and manage Kanban boards as markdown files with YAML frontmatter, providing an interactive board view while maintaining text-based storage.

## Development Commands

### Essential Commands
- `yarn dev` - Start development build with watch mode
- `yarn build` - Create production build
- `yarn typecheck` - Run TypeScript type checking
- `yarn lint` - Run ESLint on TypeScript/TSX files
- `yarn lint:fix` - Run ESLint with auto-fix
- `yarn prettier` - Format code with Prettier
- `yarn clean` - Run prettier and lint:fix together

### Release Commands
- `yarn bump` - Bump version and generate release notes
- `yarn rlnotes` - Generate release notes from git commits
- `yarn release` - Create git commit, tag, and push

## Architecture

### Core Architecture
The plugin follows an MVC-like pattern:

- **Main Plugin Class** (`src/main.ts`) - Entry point, manages plugin lifecycle, settings, and workspace integration
- **KanbanView** (`src/KanbanView.tsx`) - Custom Obsidian view that renders the kanban interface
- **StateManager** (`src/StateManager.ts`) - Central state management for board data, handles parsing and persistence
- **Settings System** (`src/Settings.ts`) - Comprehensive settings management with both global and per-board configuration

### Key Components

#### View Management
- Uses Obsidian's `TextFileView` as base for `KanbanView`
- Supports multiple view modes: board, table, list
- Automatic switching between markdown and kanban views based on frontmatter
- Window-aware rendering for multi-window support

#### Data Flow
1. Markdown files with kanban frontmatter are parsed by `StateManager`
2. `ListFormat` parser converts markdown to internal board representation
3. React/Preact components render the interactive UI
4. Changes are serialized back to markdown via the parser
5. Files are saved through Obsidian's vault API

#### State Management
- Each file gets its own `StateManager` instance
- Multiple views can share the same state manager
- Settings cascade: per-board settings override global plugin settings
- Real-time sync across multiple views of the same board

### Component Structure
- **Kanban** - Main board container component
- **Lane** - Column/list components with drag-drop support
- **Item** - Individual card components with markdown rendering
- **Editor** - Inline markdown editing with date/time widgets
- **DND System** - Custom drag-and-drop implementation in `src/dnd/`

### Parser System
- **BaseFormat** - Abstract parser interface
- **ListFormat** - Markdown list-based parser (current implementation)
- Extensible design allows for additional board formats
- Handles frontmatter, card content, and metadata parsing

## Technology Stack

- **Framework**: Preact (aliased as React) - smaller bundle size for Obsidian plugin
- **Build System**: esbuild with custom configuration
- **Styling**: Less CSS with custom plugin-specific styles
- **Date Handling**: Custom flatpickr integration with Obsidian-aware date formatting
- **Markdown**: Integration with Obsidian's markdown rendering and editing APIs
- **Drag & Drop**: Custom implementation compatible with Obsidian's interface

## Plugin Integration

### Obsidian APIs Used
- `TextFileView` for file-based views
- `WorkspaceLeaf` management for view lifecycle
- `MetadataCache` for frontmatter and markdown parsing
- Vault API for file operations
- Editor suggestions for date/time input
- Command palette integration
- Settings tab integration

### External Plugin Support
- **Dataview Plugin**: Metadata extraction and display
- **Tasks Plugin**: Task-specific metadata handling
- **Daily Notes**: Date linking integration

## Settings Architecture

The plugin uses a sophisticated multi-level settings system:

1. **Global Settings** - Plugin-wide defaults
2. **Board Settings** - Per-board overrides stored in frontmatter
3. **View Settings** - Per-view state (like collapsed lanes)

Settings are managed through:
- `SettingsManager` - UI and validation
- `StateManager.getSetting()` - Runtime value resolution
- Automatic inheritance and override behavior

## File Structure Notes

- `src/components/` - React/Preact UI components
- `src/dnd/` - Custom drag-and-drop system
- `src/parsers/` - Markdown parsing and serialization
- `src/lang/` - Internationalization with locale files
- `src/settings/` - Settings UI components
- `docs/` - User documentation and assets

## Development Notes

- Code uses TypeScript with React JSX
- ESLint configuration allows flexible TypeScript patterns
- Prettier handles code formatting with specific import sorting
- The plugin monkey-patches some Obsidian internals for view integration
- Uses immutability-helper for state updates to prevent mutations