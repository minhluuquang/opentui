# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Build Commands
- `bun build:prod` - Build optimized Zig components (creates platform-specific libraries in src/zig/lib/)
- `bun build:dev` - Build Zig components with debug symbols
- `bun build` - Standard Zig build

### Testing
- `bun test` - Run all tests
- `bun test <file>` - Run specific test file (e.g., `bun test src/animation/Timeline.test.ts`)

### Development
- `bun run src/examples/index.ts` - Run interactive example selector
- `bun prettier:write` - Format all code with Prettier

## Architecture Overview

OpenTUI is a TypeScript-based terminal UI framework with native Zig performance optimization. The architecture consists of:

### Core Rendering System
- **CliRenderer** (`src/index.ts`): Main renderer class managing the terminal display, event loop, and coordinate system
- **Renderable** (`src/Renderable.ts`): Base class for all visual components with hierarchical parent-child relationships and z-index sorting
- **OptimizedBuffer** (`src/buffer.ts`): High-performance buffer implementation with FFI bindings to Zig for native rendering

### Native Performance Layer
- **Zig Integration** (`src/zig/`): Native code for performance-critical operations like buffer management and ANSI sequence generation
- Platform-specific libraries are automatically loaded based on OS (darwin/linux)
- Threading support for rendering (disabled on Linux due to current issues)

### Component System
- **Hierarchical Rendering**: Components inherit position from parents, enabling relative positioning
- **Event System**: Mouse and keyboard event propagation through the component tree
- **Selection System**: Text selection support with multi-container awareness
- **Split Mode**: Experimental feature allowing TUI to occupy only part of terminal while preserving stdout

### Extended Features
- **3D/WebGPU Support** (`src/3d/`): WebGPU renderer with sprite animation and physics integration
- **Animation System** (`src/animation/Timeline.ts`): Timeline-based animation framework
- **UI Components** (`src/ui/`): Pre-built components like Input, Select, TabController
- **Styled Text** (`src/styled-text.ts`): Rich text rendering with colors and attributes

## Code Patterns

### Creating Renderables
```typescript
class MyRenderable extends Renderable {
  protected renderSelf(buffer: OptimizedBuffer): void {
    buffer.drawText("content", this.x, this.y, color)
  }
}
```

### Mouse Event Handling
```typescript
public processMouseEvent(event: MouseEvent): boolean {
  if (event.type === "click") {
    // Handle click
    event.preventDefault()
    return true
  }
  return super.processMouseEvent(event)
}
```

### Buffer Operations
- Direct buffer manipulation for performance
- FFI calls to Zig for optimized rendering
- Respect alpha channel for transparency effects

## Important Considerations

- **Bun Runtime**: This project uses Bun, not Node.js. Use `bun` commands, not `npm`/`node`
- **Platform Differences**: Threading disabled on Linux; test on target platform
- **Coordinate System**: Terminal-based (0,0 at top-left), with pixel resolution detection support
- **Memory Management**: Automatic snapshots for monitoring; Zig handles native memory
- **Event Loop**: Frame-based rendering with configurable FPS target