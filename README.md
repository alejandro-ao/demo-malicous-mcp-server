[No content]
# Malicious MCP Server Example

## Overview

This educational example demonstrates a critical security risk in Model Context Protocol (MCP) servers: the ability to execute system commands without the user or AI assistant being aware. The example is intentionally designed to highlight how dangerous this can be, especially for servers using the **stdio** transport.

## What is MCP?

Model Context Protocol (MCP) is an open protocol that connects AI applications (like IDEs, chatbots, or agents) to external tools and data sources. MCP servers expose **tools** (executable functions), **resources** (data), and **prompts** (templates) to AI clients. The protocol is designed for composability, security, and user control, but improper server implementations can introduce severe risks.

Learn more: [modelcontextprotocol.io](https://modelcontextprotocol.io/)

## Why is this dangerous?

- **Tool execution is model-controlled:** AI models can discover and invoke tools automatically. If a tool executes a system command (e.g., `rm -rf /`), it can do so without explicit user knowledge unless the client enforces confirmation dialogs.
- **Stdio transport risk:** When using stdio, the MCP server runs as a subprocess on the user's machine. Any tool exposed by the server can execute arbitrary code locally, potentially compromising the system.
- **Lack of visibility:** If the server is malicious or poorly designed, neither the user nor the AI assistant may realize a dangerous command is being run.

## How does MCP work?

MCP uses a client-server architecture:

- **Host:** The AI application (e.g., VS Code, Claude Desktop)
- **Client:** Connects to one MCP server
- **Server:** Exposes tools/resources/prompts

Servers declare their capabilities during initialization. Tools are listed and can be invoked by the client or model. For stdio servers, the client launches the server as a subprocess and exchanges JSON-RPC messages over stdin/stdout.

## Security Risks

- **Arbitrary code execution:** Malicious or buggy servers can expose tools that run dangerous commands.
- **No user confirmation:** If the client does not enforce confirmation dialogs, tools may be executed without user consent.
- **Session hijacking:** Attackers may hijack sessions or inject malicious payloads if session IDs are predictable or not bound to user identity.
- **Token passthrough (for HTTP servers):** Accepting tokens not intended for the server can lead to privilege escalation and confused deputy attacks.

## Best Practices

**For MCP server authors:**
- Never expose tools that execute system commands unless absolutely necessary.
- Always validate tool inputs and sanitize outputs.
- Use logging libraries that write to stderr (never stdout for stdio servers).
- Implement proper access controls and rate limiting.

**For MCP client authors:**
- Always show confirmation dialogs before executing any tool, especially those that run system commands.
- Clearly display which server and tool is being invoked.
- Log all tool executions for audit purposes.
- Validate tool results before passing to the AI model.

**For users:**
- Only connect to trusted MCP servers.
- Review the list of available tools before enabling a server.
- Be cautious with stdio servers, as they run locally and have direct access to your system.

## Educational Purpose

This repository is for educational and demonstration purposes only. Do **not** use this code in production or connect it to real AI assistants without understanding the risks.

## References

- [MCP Specification](https://modelcontextprotocol.io/specification/2025-06-18/index)
- [Security Best Practices](https://modelcontextprotocol.io/specification/2025-06-18/basic/security_best_practices)
- [Example Servers](https://modelcontextprotocol.io/examples)

---
**Warning:** MCP servers can be extremely powerful. Always review server code and tool definitions before connecting to any AI application.
