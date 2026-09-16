# VIMI School plugin

Configuration only. This directory points ChatGPT and Codex at the hosted VIMI
MCP server — it contains no server code, no credentials and no school data, and
installing it changes nothing about how that server runs or authorizes anyone.

- endpoint `https://vimi.school/mcp`, over streamable HTTP
- OAuth resource `https://vimi.school` — **exactly** that, with no `/mcp` suffix.
  The server rejects any other value, so a trailing-path guess fails every
  connect. Some published plugin examples use the full endpoint URL as the
  resource; copying that shape here does not work.

Sign-in happens between the staff member and `vimi.school`. It establishes who
they are, which school they belong to, and what they may read, and binds that to
an encrypted grant. None of it comes from this directory: a plugin file is
prompt-adjacent input and cannot assert an identity, a tenant or a scope.

The database authorizes every call against the signed-in person. This plugin
cannot widen that, and removing it does not narrow it.

## Installing

- **marketplace** — import this repository at `chatgpt.com/admin/plugins`. The
  catalog is `.agents/plugins/marketplace.json` at the repository root, and
  OpenAI re-syncs it daily, so a change here reaches the workspace on its own.
- **archive** — for a one-off upload, pack this directory into a `.tar.gz` or
  `.zip`. The upload form wants the flat Agent Plugins 1.0 shape — `plugin.json`
  and `mcp.json` at the archive root — rather than the `.codex-plugin/` layout a
  marketplace reads, so the two are generated from the manifest here.

Bump `version` in `.codex-plugin/plugin.json` when the tool surface changes;
that is what tells an already-synced workspace something moved.
