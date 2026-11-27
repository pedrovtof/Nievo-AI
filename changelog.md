# Changelog

All notable changes to this project will be documented in this file.

### Changelog

2026-11-27 - [NEW] - Add Python Alembic skills for data inserts, indexes, and PK/FK keys

Detalhes:

- Add python-alembic-gen-insert skill for interactive Alembic seed/data insert migrations with audit column defaults and FK-aware downgrade cleanup - pedrovotf - 2026-11-27 - N/A - PENDING
- Add python-alembic-gen-index skill for interactive Alembic standard, unique, composite, partial, and concurrent index migrations with schema-qualified drops - pedrovotf - 2026-11-27 - N/A - PENDING
- Add python-alembic-gen-keys skill for interactive Alembic primary key and foreign key constraint migrations using idempotent pg_constraint guards - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic links for python-alembic-gen-insert, python-alembic-gen-index, and python-alembic-gen-keys under .agents/skills/ - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [DOC] - Add MCP Docker repository reference to README

Detalhes:

- Document MCP server Docker image repository (docker.io/pedrovotf/nievo_ai:latest) with pull, run, and client configuration instructions in README.md - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-25 - [NEW] - Add Python Alembic and .NET backend generation skills

Detalhes:

- Add python-alembic-gen-entity skill for interactive Alembic DDL generation with mandatory audit columns - pedrovotf - 2026-11-25 - #8 - PENDING
- Add dotnet-gen-controller, dotnet-gen-service, dotnet-gen-entity, and dotnet-gen-test skills aligned with backend standards (including default active = true query filtering in dotnet-gen-entity and mandatory Response contract creation/adherence across dotnet-gen-controller and dotnet-gen-service) - pedrovotf - 2026-11-25 - #8 - PENDING
- Register relative symbolic links for all five new skills under .agents/skills/ - pedrovotf - 2026-11-25 - #8 - PENDING

2026-11-25 - [CHG] - Update git-branch skill to restrict scope to branch creation with interactive naming confirmation

Detalhes:

- Restrict git-branch skill strictly to branch creation, forbidding git add, git commit, and git push operations - pedrovotf - 2026-11-25 - #6 - PENDING
- Add interactive workflow to inspect local edits before asking for purpose, card, and board, and require user confirmation of the proposed branch name - pedrovotf - 2026-11-25 - #6 - PENDING

2026-11-01 - [DOC] - Add symbolic link configuration and new skill registration guide to README

Detalhes:

- Document instructions for setting up symbolic links for AI directory and individual agent skills across platforms - pedrovotf - 2026-11-01 - N/A - PENDING
- Add mandatory addendum explaining the requirement to create a new symbolic link whenever a new skill is added - pedrovotf - 2026-11-01 - N/A - PENDING
2026-11-01 - [NEW] - Add git-push-request skill for automated sync, changelog conflict resolution, and PR briefing

Detalhes:

- Add git-push-request skill with pre-push sync, automated changelog conflict resolution, diff inspection, and PR template generation - pedrovotf - 2026-11-01 - N/A - PENDING

2026-10-31 - [DOC] - Add submodule integration and configuration guide to README

Detalhes:

- Document instructions for adding repository as submodule, configuring AI directives, and setting up link files - pedrovotf - 2026-10-31 - N/A - PENDING

2026-10-31 - [OTHER] - Initial repository structure and base AI configuration

Detalhes:

- Setup initial repository structure, AI rules, and base agent skills - pedrovotf - 2026-10-31 - N/A - PENDING
