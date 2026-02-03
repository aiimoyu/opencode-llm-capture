# LLM Capture Plugin for OpenCode

A plugin that captures and logs all LLM API interactions for debugging and analysis purposes.

## Description

This plugin intercepts HTTP requests made by OpenCode to LLM providers (like OpenAI, Anthropic, etc.) and saves detailed logs of both requests and responses. Each session gets its own directory with timestamped JSON files containing full request/response data including headers, bodies, and timing information.

## Features

- Automatic request/response logging
- Session-based organization
- Safe body handling (avoids consuming streams)
- SSE stream detection and preview
- Custom session headers for grouping
- JSON output with metadata

## Installation

1. Clone or download this repository
2. Copy `llm-capture.ts` to your OpenCode plugins directory
3. Restart OpenCode

## Usage

The plugin automatically activates when loaded. Logs are saved to `~/.opencode/debug/` by default.

### Session Management

- If a session ID is provided, logs go to `~/.opencode/debug/{sessionID}/`
- Otherwise, logs are grouped by date: `~/.opencode/debug/{YYYY-MM-DD}/`

### File Structure

Each log file contains:
- Metadata (timestamp, duration, URL, method, response type)
- Request (headers, body snapshot)
- Response (status, headers, body snapshot)

### SSE Stream Handling

For streaming responses (SSE), the plugin captures a preview of the first 200 lines and provides metadata about the stream.

## Todos

- [ ] Custom output folder (allow users to specify custom path instead of ~/.opencode/debug)
- [ ] Visualization (add tools for merging SSE streams, generating timelines, or other analysis views)
- [ ] Dynamically enable and disable dump

## License

MIT