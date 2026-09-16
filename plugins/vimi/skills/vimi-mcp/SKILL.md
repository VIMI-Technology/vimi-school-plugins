---
name: vimi-mcp
description: How to read answers from the VIMI MCP server correctly. Load whenever a question touches students, classes, rosters, attendance, absences, guardians, contacts, student health, school reports, the school calendar, handbook policy, or Google Classroom for this school.
---

# Reading school records

The database, not the model, decides what is visible. Every tool runs under the
signed-in staff member's own identity, so results are already scoped to that
person's reach. Do not try to widen a result, and do not treat a narrow result
as a system failure.

## An empty result is ambiguous by construction

Anything out of the caller's scope is **absent, not refused** — so `[]` never
distinguishes "there is none" from "you cannot see it". Never report a bare
empty result. Say which of the two it might be and point the person at
`explain_access`, which states their actual grants.

The same holds for reports: a gated report answers "exists, but needs a grant",
never "no such report".

## Never guess an identity

Ambiguous names come back marked ambiguous — return them that way and ask.
Two staff who share a surname and differ by one initial in their address are two
people, not a typo in one. Do not pick the likelier one.

A person can hold more than one reach: some staff are also guardians. Tools
merge both rather than making the caller choose, so do not assume a single role.

## A schedule is not an observation

- `check_absences` reads recorded absence observations — evidence of what happened.
- `locate_student_schedule` reads a timetable and always returns
  `source: timetable, observed: false`.

Never present a scheduled location as where a child physically is.
`find_classes:room_availability` is the same kind of claim: a timetable plan,
never observed occupancy.

`read_school_calendar` rows are published schedule evidence, not a curated
school-open or school-closed assertion. Use it for current operational dates;
the academic-calendar PDF in `search_school_documents` is a static artifact and
can be out of date.

## Health is its own capability

`read_student_health` requires `health.read`, which is never implied by seeing a
student elsewhere. It defaults to a counts-only summary; named rows need an
explicit `student` or `section` operation.

**A blank field means not recorded. It never means "none".** Do not report an
absent allergy field as "no allergies".

## Guardians

A guardian's `title` is an honorific, not a family relationship. The server
returns `relationship: "not_recorded"` for a reason: the school's records hold a
salutation, and no field states how that adult is related to the child. Never
infer a relationship from a title, a name, or the order guardians appear in.

## Homerooms

`find_students:roster`, `read_student_health:section` and `query_attendance`
accept a homeroom label directly — `5-1`, `K3`, `7-M`. A homeroom is a student
attribute; only elementary homerooms correspond to a section, so do not convert
one into a section id yourself.

## Two tools change things

`manage_workspace_group` and `manage_google_classroom` are the only tools that
write. They are marked destructive, need their own consent, and act on one exact
Google target per call. Confirm the exact target with the person before calling
either. Everything else is read-only.

## Answering

Answer the fields that were asked for first. Authorized records come back
complete on purpose — that is not an invitation to dump the whole record when
someone asked one question.
