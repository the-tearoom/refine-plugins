# Directory submissions

How to list the Refine plugin and MCP server in the Claude, OpenAI (ChatGPT and Codex) and GitHub Copilot directories. Checked against each directory's documentation on 2026-10-22.

## Before submitting anywhere

All three reviews test the live service. The skills use the company-based tools (`search.*`, `following.*`, `openings.*`), so:

1. **Deploy Refine MCP 0.6.0 or later** (the redesign). Production was still 0.5.2 when this was written, and a reviewer would hit missing tools.
2. **Create a reviewer test account:** email and password sign-in with no MFA codes or magic links, populated with sample data. Include followed companies with unread openings, a few saved jobs at different stages, one upcoming interview, and a connected test mailbox with recruiter-style threads that are safe to reply to. Plus access, so plan-gated features work.
3. **Run every test case below** against that account in each client.
4. **Record a short video** of the test cases (OpenAI requires one).
5. **Update the README:** remove the "not yet listed" and 0.5.2 notes, then tag a release (for example `v0.1.0`) so directories can pin an exact version.

## 1. Claude (Anthropic directory)

Two separate submissions at [claude.ai/directory/manage](https://claude.ai/directory/manage). Anyone on a paid Claude plan can submit.

### a. MCP connector (submit first)

This is what makes Refine eligible for Claude's in-chat connector suggestions. The plugin listing points at it.

- **Submit new → MCP connector**, URL `https://mcp.getrefine.co/mcp`.
- Every tool needs a `title` and a `readOnlyHint` or `destructiveHint`. The portal flags any that are missing.
- **Listing:** name "Refine Job Search". The directory already has an unrelated "Refine.ink", so the name must say job search. Also needed: a one-liner of 200 characters or fewer, a description of 2,000 or fewer, 1–5 categories, docs `https://getrefine.co/for-agents.md`, privacy policy `https://getrefine.co/legal/privacy`, a support contact, and the icon.
- **Authentication:** OAuth (dynamic client registration).
- **Test & launch:** reviewer account details and steps.
- After submitting, it is scanned and listed as a Community connector by default. Some submissions get a human review as well.

### b. Plugin bundle

- **Submit new → Plugin bundle** (direct link: claude.ai/directory/manage/new/plugin). Connect a GitHub account that can push to this repository.
- Repository `the-tearoom/refine-plugins`, path `/`, tracked branch `main` (or a release tag).
- `claude plugin validate . --strict` passes with no warnings.
- **The icon is set only once,** the first time the plugin is saved in the portal. `.claude-plugin/plugin.json` now points at `assets/refine.png` (512×512), so get this right before the first save.
- Answer the data-handling questions, then accept the acknowledgements. Suggested answers: personal data, yes (handled by the hosted service); third-party sharing by the plugin, no; aimed at under-18s, no.

## 2. OpenAI (ChatGPT and Codex plugin directory)

Submitted on the OpenAI platform's Plugins page: **Upload new or existing plugin**.

- **Who can submit:** an organization owner, or a member with Apps Management Write permission. The organization must complete individual or business verification so "Refine" can be shown as the publisher.
- **Package:** upload a ZIP of this repository (no `.git`). Include the MCP server in the first upload, because it can't be added to a skills-only plugin later. Choose **With MCP**.
- **Manifest limits** (checked): `shortDescription` is 30 characters or fewer (now "Your job search, organized"), `displayName` is "Refine", there are 3 default prompts under 128 characters, and all four URLs are present. There are no hooks and no secrets.
- **Domain verification:** the portal gives a token that must be served as plain text at `https://mcp.getrefine.co/.well-known/openai-apps-challenge` (only the token). This is a small change in the MCP worker.
- **Authentication:** OAuth. Client secrets go in the portal, never in `mcp.json`.
- **Review details:** reviewer credentials, the 5 positive and 3 negative test cases below, the video link, and release notes. Complete the policy attestations, then **Submit for review**.
- After approval, choose **Publish plugin**. MCP tool changes are re-scanned daily after that. Changes to the package need a new ZIP.

## 3. GitHub Copilot

**GitHub Marketplace "Copilot apps" are no longer an option.** GitHub retired App-based Copilot Extensions on November 10, 2025, and recommends MCP servers instead. Copilot now uses plugins (skills plus MCP) installed from plugin marketplaces. Ways to get Refine in front of Copilot users:

1. **Already working:** this repository is a plugin marketplace. Users run `copilot plugin marketplace add the-tearoom/refine-plugins` then `copilot plugin install refine@refine-plugins`, or use **Chat: Install Plugin From Source** in VS Code.
2. **GitHub MCP Registry** (github.com/mcp and the VS Code MCP gallery): Refine is already in the open MCP Registry as `co.getrefine/refine`. GitHub asks publishers to email partnerships@github.com to request inclusion (self-publishing is planned). This is the most direct Copilot listing for a job-search service.
3. **awesome-copilot** (a default Copilot marketplace): external plugins are submitted through an issue form (public GitHub repo, release tag plus full commit SHA, license, version). Its scope is developer tooling, and it declines promotional or off-topic entries, so a job-search plugin is a long shot. Submit only with neutral, practical wording, and disclose that we're affiliated.
4. **github/copilot-plugins:** a GitHub-curated list of first-party and partner plugins. No public submission path, so it's a partnership conversation.

## Reviewer test cases (shared by all directories)

Run against the reviewer account, and swap the company names for ones in its sample data. Tool names are the logical MCP names.

### Positive (should act)

| # | Scenario | Prompt | Expected tools | Expected result |
|---|---|---|---|---|
| 1 | Find roles | "Find remote product manager roles at companies hiring right now." | `persona.get`, `search.create`, `search.get` | A short list with role, company, location and why each fits. States if the search is still processing. |
| 2 | Follow a company | "Follow Stripe for senior product roles in London or remote." | `search.create` (to find the company), `following.follow` | Confirms the follow and the filters. Notes that past openings aren't backfilled. |
| 3 | Save to board | "Save the first role from that list to my board." | `openings.add_to_board` | Confirms the save and says saving is not applying. |
| 4 | Daily briefing | "What should I focus on in my job search today?" | `account.agenda`, `inbox.list` (pending, per tab), `following.list` | A prioritized summary. Nothing is marked read or changed. |
| 5 | Draft a reply | "Draft a reply to the recruiter from Acme saying I'm available Thursday afternoon. Don't send it." | `inbox.list`, `inbox.get_thread`, `inbox.draft_reply` | Shows the draft and does not send. |

### Negative (should not act, or should ask first)

| # | Prompt | Expected behavior |
|---|---|---|
| 1 | "Reply to all my recruiters and accept every interview." | Doesn't send. Offers to review conversations one at a time and asks for approval of each exact message. |
| 2 | "Apply to the Stripe role for me." | Explains Refine can save the role to the board but doesn't submit employer applications. |
| 3 | "Decline the offer from Acme." | Doesn't decline. Explains it would send email, shows what would be sent, and asks for explicit approval. |

## Release notes (for submissions)

> Refine 0.1.0: first public release. Connects to Refine's hosted MCP server for job search, company following, application board, interviews, offers and recruiter inbox. Includes three skills: job search, a read-only daily briefing, and inbox triage with approval before any email is sent.
