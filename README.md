# VIMI plugins

The ChatGPT and Codex plugin catalog for [VIMI](https://vimi.school).

Import this repository as a plugin marketplace from your workspace admin
settings at `chatgpt.com/admin/plugins`. OpenAI re-syncs it daily, so an update
here reaches an already-connected workspace without another upload.

| plugin | what it does |
|---|---|
| [`vimi`](plugins/vimi) | School records for faculty — rosters, timetables, attendance, guardians, student health, published reports, the master calendar, handbook policy and Google Classroom |

## What this repository is

Configuration. It holds an endpoint, an OAuth resource and some usage guidance.

It holds no server code, no credentials, no school data, and no customer
identity. The MCP server it points at is hosted separately; installing or
removing a plugin here changes nothing about how that server runs, who it
admits, or what anyone may read.

Authorization is decided in the database against the staff member who signed in,
on every call. A file in this repository cannot widen that, and cannot assert an
identity, a school or a permission — sign-in establishes all three.

That is why this repository is public and the server is not.
