# Refine plugins

Find roles, follow companies, track applications, and work through recruiter
email with your AI assistant. Your job search stays in Refine across sessions
and clients.

This repository contains plugin manifests and shared skills, not the Refine
server. It connects to **https://mcp.getrefine.co/mcp** using your Refine account
and browser-based OAuth. There is no npm package, local server, or API key to set
up. The hosted application and MCP implementation are separate from this repo.

## Beta readiness

The company-based workflows require **Refine MCP 0.6.0 or later**. The hosted
service was still 0.5.2 when this package was prepared on October 18, 2026; the
service rollout and authenticated client checks are pending. Installing a plugin
does not upgrade that service. These packages are not yet listed in the public
OpenAI, Anthropic, or Cursor directories.

## Install

Install through one path per client. If you already configured a standalone
Refine MCP in that client, disable that duplicate after the plugin is connected.
Restart the client after installation, then authorize Refine in its MCP connection
UI. Installing files alone does not sign you in.

### Codex

```sh
codex plugin marketplace add the-tearoom/refine-plugins
codex plugin add refine@refine-plugins
```

In Codex desktop, open the plugin browser, select the **Refine** marketplace,
and install **Refine**. Complete the connection prompt in your browser. Use the
plugin connection UI rather than adding a second standalone MCP server.

### GitHub Copilot CLI

```sh
copilot plugin marketplace add the-tearoom/refine-plugins
copilot plugin install refine@refine-plugins
```

Start a new session and use `/mcp` to connect Refine. If authorization is needed,
run `/mcp auth refine` using the server name shown by your client.

For Copilot in VS Code, use **Chat: Install Plugin From Source** and enter
`https://github.com/the-tearoom/refine-plugins`. Then connect the plugin's Refine
MCP server through the MCP UI. Plugin commands vary by VS Code release; see the
[VS Code plugin guide](https://code.visualstudio.com/docs/agent-customization/agent-plugins).

**Copilot cloud agent/code review is not supported by this package's normal
connection flow.** It does not support remote OAuth MCP and may run enabled tools
without approval prompts. Do not put personal mailbox tokens in repository
secrets to work around this.
[GitHub restrictions](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/mcp-and-cloud-agent).

### Claude Code

```sh
claude plugin marketplace add the-tearoom/refine-plugins
claude plugin install refine@refine-plugins
```

Start a new session, run `/mcp`, select the plugin's Refine server, and complete
browser authorization. The same skills are available as
`/refine:refine-job-search`, `/refine:refine-daily-briefing`, and
`/refine:refine-inbox-triage`.

### Cursor

For local installation, clone this repo into Cursor's local plugins directory:

```sh
git clone https://github.com/the-tearoom/refine-plugins.git ~/.cursor/plugins/local/refine
```

Restart Cursor, enable the local plugin, and authenticate its Refine MCP server.
This is a local plugin, not a claim of Cursor Marketplace approval.
[Cursor plugin guide](https://cursor.com/docs/plugins).

### Gemini CLI

```sh
gemini extensions install https://github.com/the-tearoom/refine-plugins
```

Restart Gemini CLI and run `/mcp auth refine` if needed. The extension uses
Streamable HTTP (`httpUrl`), not legacy SSE.

## Try it

- “Find remote product roles that fit what I'm looking for.”
- “What should I focus on in my job search today?”
- “Help me review my recruiter email. Draft replies, but don't send them.”

The shared skills cover job search, a read-only briefing, and inbox triage.
Some features require Plus or Student; see [pricing](https://getrefine.co/pricing).

Saving a job is not applying to an employer. Reading a briefing does not mark
anything read. Email sends require approval of the exact message; declining
outreach can send email and needs separate approval. The plugin does not enable
blanket tool auto-approval, run background hooks, or collect its own telemetry.
Refine's hosted service handles account access and operational monitoring under
its [privacy policy](https://getrefine.co/legal/privacy).

## Development and release

All clients share `skills/`. Root `plugin.json` and `mcp.json` use Agent Plugins
1.0; `.claude-plugin/plugin.json` and `.mcp.json` are Claude's adapter;
`gemini-extension.json` is Gemini's adapter. Copilot can read the Claude-style
marketplace; Codex has its own `.agents/plugins/marketplace.json`. The two
catalogs point at the same package, with no copied skills or runtime code.

Keep the plugin name, version, and hosted endpoint consistent across adapters.
Do not add credentials, an embedded backend, install scripts, or hook-based
auto-approval. Licensing these manifests and skills does not license the hosted
Refine service or grant rights to Refine's trademarks.

Before publishing a release:

1. Validate the portable files against their declared schemas and run
   `claude plugin validate . --strict` and skill frontmatter validation.
2. Verify a clean client install and OAuth consent/refresh/revocation. Check a
   read-only briefing, search pagination, and an explicitly requested board save
   using safe demo data. Test missing scope and plan restrictions too.
3. Check that drafting does not send, and that email actions preserve approval.
   Do not test sends against real recruiters.
4. Bump both plugin versions and the Gemini version together. Directory review,
   OpenAI submission authentication configuration, and gallery indexing are
   separate release tasks; they are not implied by pushing to this repository.

OAuth for direct client installs is discovered from the hosted server. Public
OpenAI submission has additional authentication metadata requirements; validate
its submission package separately rather than adding nonstandard fields to the
portable `mcp.json`.

Sources: [OpenAI packaging](https://developers.openai.com/plugins/build/plugins),
[Copilot plugins](https://docs.github.com/en/copilot/concepts/agents/about-plugins),
[Claude manifests](https://code.claude.com/docs/en/plugins-reference),
[Gemini extensions](https://geminicli.com/docs/extensions/writing-extensions/),
[Agent Plugins schemas](https://agent-plugins.org/).

For installation problems, [open an issue](https://github.com/the-tearoom/refine-plugins/issues)
with the client version and a redacted error. Never attach tokens or email
contents. For account/billing help, use [Refine](https://getrefine.co).
