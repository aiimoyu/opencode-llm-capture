# LLM Capture Plugin for OpenCode

A plugin that captures and logs all LLM API interactions for debugging and analysis purposes.

## Install

Tell OpenCode:

``` bash
Fetch and follow instructions from https://raw.githubusercontent.com/aiimoyu/opencode-llm-capture/refs/heads/main/INSTALL.md
```

For details, see [INSTALL.md](https://raw.githubusercontent.com/aiimoyu/opencode-llm-capture/refs/heads/main/INSTALL.md).

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

The plugin automatically activates when loaded. Logs are saved to `~/.config/opencode/opencode-llm-capture/llm-dump/` by default.

### Session Management

- If a session ID is provided, logs go to `~/.config/opencode/opencode-llm-capture/llm-dump/{sessionID}/`
- Otherwise, logs are grouped by date: `~/.config/opencode/opencode-llm-capture/llm-dump/{YYYY-MM-DD}/`

### File Structure

Each log file contains:

- Metadata (timestamp, duration, URL, method, response type)
- Request (headers, body snapshot)
- Response (status, headers, body snapshot)

### SSE Stream Handling

For streaming responses (SSE), the plugin captures a preview of the first 200 lines and provides metadata about the stream.

## Viewer

This project includes a web-based viewer (`viewer/viewer.html`) to explore captured sessions.

### Server Mode (Recommended)

Run the local server to browse sessions without manually selecting folders:

```bash
# Start the viewer server
bun run serve
# OR
bun viewer/server.ts
```

Open `http://localhost:3000` in your browser. The viewer will automatically load sessions from `~/.config/opencode/opencode-llm-capture/llm-dump/`.

### File Mode

Open `viewer/viewer.html` directly in your browser. You will be prompted to select a folder containing the logs (e.g., `~/.config/opencode/opencode-llm-capture/llm-dump/` or a specific session folder).

## Log Parsing

This project includes a parser to extract conversation data from captured logs.

### Parser Features

- Extracts request messages (system/user/assistant/tool roles)
- Reconstructs complete assistant responses from fragmented SSE streams
- Handles tool_calls split across multiple SSE chunks
- Outputs clean, normalized JSON structure
- Comprehensive test coverage

### CLI Usage

```bash
# Parse single file to JSON
bun cli.ts log.json

# Parse with text format output
bun cli.ts log.json -f text -o output.txt

# Parse entire directory (batch mode)
bun cli.ts sample/ses_xxx/ -o results.json

# Minified JSON output
bun cli.ts log.json --no-pretty
```

**CLI Options:**
- `-i, --input <path>`: Input log file or directory (required)
- `-o, --output <path>`: Output file (default: `<input>.parsed.<format>`)
- `-f, --format <format>`: Output format: `json` or `text` (default: `json`)
- `--no-pretty`: Minify JSON output
- `-h, --help`: Show help message

### Programmatic Usage

```typescript
import { parseLogToConversation } from "./parser";

const logData = JSON.parse(await readFile("log.json", "utf-8"));
const conversation = parseLogToConversation(logData);

// Access parsed data
console.log(conversation.request.messages);       // All historical messages
console.log(conversation.response.message);        // Assistant's response
```

### Output Format

```typescript
interface ParsedConversation {
  request: {
    messages: Message[];  // All historical messages
  };
  response: {
    message: Message;  // The assistant's response
  };
}

interface Message {
  role: "system" | "user" | "assistant" | "tool";
  content?: string;
  tool_calls?: ToolCall[];
  tool_call_id?: string;  // For tool role
  name?: string;
}
```

## Todos

- [ ] Custom output folder (allow users to specify custom path instead of ~/.config/opencode/opencode-llm-capture/llm-dump)
- [ ] Dynamically enable and disable dump

## License

MIT
