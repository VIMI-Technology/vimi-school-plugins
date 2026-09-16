# VIMI plugin

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

## What it exposes

Every tool the server advertises, with no filter in between. A plugin does not
enumerate tools — the client asks the server at connect time — so this table is
documentation, not configuration, and a test pins it against the live registry.

| tool | operations |
|---|---|
| `explain_access` | — |
| `find_students` | search · profile · roster · guardian_children |
| `find_classes` | my_classes · subject_teachers · student_classes · course_sections · timetable · current_schedule · room_schedule · room_availability |
| `check_absences` | — |
| `query_attendance` | daily · exceptions · arrivals · dismissals · snapshots |
| `locate_student_schedule` | — |
| `find_guardians` | by_student · household · contact_sheet · parent_lookup |
| `read_student_health` | summary · student · section |
| `run_school_report` | list_reports · run_report |
| `read_school_calendar` | — |
| `search_school_documents` | list_documents · search · read_page |
| `read_google_classroom` | my_courses · course_roster · coursework · student_assignments · student_progress |
| `search_workspace_directory` | list_groups · list_group_members · list_member_groups · get_staff · search_staff |
| `manage_workspace_group` | **writes to Google** — add_member · remove_member · create_group |
| `manage_google_classroom` | **writes to Google** — courses · rosters · content · coursework · grades · guardians |

Which of them *answer* depends on the person, not on this file. Each one checks
its own capability against the signed-in staff member, so a teacher and a
registrar connected through the same plugin see different results from the same
question. `explain_access` states what the caller actually holds.

The last two are the only tools that change anything. They are marked
destructive, need their own scope on top of the read scope, and act on one exact
Google target per call.

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
