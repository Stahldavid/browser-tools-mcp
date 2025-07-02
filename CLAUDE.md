# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Development Commands

### Build Commands
- **MCP Server**: `npm run build` in `browser-tools-mcp/` directory
- **Browser Tools Server**: `npm run build` in `browser-tools-server/` directory

### Start Commands
- **MCP Server**: `npm run start` in `browser-tools-mcp/` directory
- **Browser Tools Server**: `npm run start` in `browser-tools-server/` directory

### Development Commands
- **MCP Inspector**: `npm run inspect` to debug MCP server locally
- **Live Inspector**: `npm run inspect-live` to debug published package

### Publishing
- **Update MCP Server**: `npm run update` (builds, bumps version, publishes)
- Both packages use `prepublishOnly` hooks to build before publishing

## Architecture Overview

This is a browser monitoring and interaction tool that uses the Model Context Protocol (MCP) to enable AI-powered applications to capture and analyze browser data through a Chrome extension.

### Core Components

**Three-tier architecture:**

1. **Chrome Extension** (`chrome-extension/`)
   - Monitors XHR requests/responses and console logs
   - Tracks selected DOM elements and sends data to browser connector
   - Connects via WebSocket for screenshot capture
   - Provides DevTools panel for configuration

2. **Browser Tools Server** (`browser-tools-server/`)
   - Middleware between Chrome extension and MCP server
   - Handles log processing, screenshot capture, and audit execution
   - Runs on port 3025 (configurable via env or .port file)
   - Implements Lighthouse audits for accessibility, performance, SEO, and best practices
   - Uses Puppeteer for headless browser automation

3. **MCP Server** (`browser-tools-mcp/`)
   - Implements Model Context Protocol for AI client integration
   - Provides standardized tools for browser interaction
   - Handles server discovery and auto-reconnection
   - Compatible with Cursor, Cline, Zed, Claude Desktop, etc.

### Key Features

**Browser Data Collection:**
- Console logs and errors via `getConsoleLogs`, `getConsoleErrors`
- Network traffic via `getNetworkLogs`, `getNetworkErrors`
- DOM element selection via `getSelectedElement`
- Screenshot capture via `takeScreenshot`
- Log management via `wipeLogs`

**Lighthouse Audits:**
- Accessibility audits (`runAccessibilityAudit`)
- Performance analysis (`runPerformanceAudit`)  
- SEO optimization (`runSEOAudit`)
- Best practices (`runBestPracticesAudit`)
- NextJS-specific guidance (`runNextJSAudit`)
- Combined audit mode (`runAuditMode`)
- Debugging workflows (`runDebuggerMode`)

### Data Flow

```
MCP Client → MCP Server → Browser Tools Server → Chrome Extension
    ↑                                                      ↓
    └────────── Response with browser data ←───────────────┘
```

### Configuration

**Environment Variables:**
- `BROWSER_TOOLS_HOST`: Server host (default: 127.0.0.1)
- `BROWSER_TOOLS_PORT`: Server port (default: 3025)

**Port Discovery:**
- Reads from environment variable first
- Falls back to `.port` file in server directory
- Auto-discovery across port range 3025-3035
- Server identity verification via `/.identity` endpoint

### Installation Requirements

1. Chrome extension installation (from releases)
2. MCP server: `npx @agentdeskai/browser-tools-mcp@latest`
3. Browser tools server: `npx @agentdeskai/browser-tools-server@latest`

### Dependencies

**Key Libraries:**
- `@modelcontextprotocol/sdk`: MCP protocol implementation
- `lighthouse`: Web auditing and performance analysis
- `puppeteer-core`: Headless browser automation
- `ws`: WebSocket communication
- `express`: HTTP server framework
- `llm-cost`: Token usage estimation

### Development Notes

- TypeScript project with strict compilation
- Uses ES modules in browser-tools-server
- Supports cross-platform paths (Windows/WSL/Linux/macOS)
- Implements graceful reconnection and error handling
- All browser data stored locally, never sent to third parties