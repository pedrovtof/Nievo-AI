# Changelog

All notable changes to this project will be documented in this file.

### Changelog

2026-11-27 - [CHG] - Enforce mandatory develop branch tracking for Nievo-AI submodule in setup skill and README

Detalhes:

- Update README.md Section 1 (Submodule Integration), Section 5 (Verification), Section 6 (Automated Full Onboarding and Quick Troubleshooting) to require adding and keeping the Nievo-AI submodule on branch develop (branch = develop in .gitmodules and local checkout on develop) - pedrovotf - 2026-11-27 - N/A - PENDING
- Update AI/GEMINI/skills/setup/setup.md across frontmatter, overview, Mermaid diagram, pre-flight inspection (Linux/Windows), confirmation gate, Phase 5.1 submodule initialization/enforcement, Phase 6 verification, troubleshooting, safety rule #8, and verification checklist to enforce branch develop on Nievo-AI without discarding local changes - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [SEC] - Enforce LOCAL-only Postman environment reads and universal .env prohibition across all skills and agents

Detalhes:

- Restrict AI/GEMINI/skills/documentation/postman/postman.md and AI/GEMINI/agents/documentation-admin.md strictly to reading/updating only the Postman LOCAL environment (never inspecting or mutating DEV, QA, STAGING, PROD, or any non-LOCAL Postman environment) - pedrovotf - 2026-11-27 - N/A - PENDING
- Enforce explicit prohibition against reading standard project .env files (.env, .env.local, .env.development, .env.staging, .env.production, .env.*, mcp_secrets.env) across all 25 skill and agent files, AI/Rules.md, GEMINI.md, and README.md - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [CHG] - Add LOCAL environment and reusable variable standards to postman skill

Detalhes:

- Update AI/GEMINI/skills/documentation/postman/postman.md to auto-create and non-destructively merge a workspace LOCAL Postman environment (via getEnvironments, getEnvironment, createEnvironment, putEnvironment/updateEnvironment) for reusable variables (host_<module>, host_port_<module>, bearer_token_<module>, default_page, default_page_size) while keeping endpoint-specific values literal - pedrovotf - 2026-11-27 - N/A - PENDING
- Synchronize LOCAL environment and reusable variable rules across AI/GEMINI/agents/documentation-admin.md, AI/Rules.md, and README.md - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add postman skill for MCP endpoint collection sync and integrate into documentation workflow

Detalhes:

- Add canonical postman skill in AI/GEMINI/skills/documentation/postman/postman.md with creation-only Postman MCP workflow (auto-creating project workspace, language-agnostic ./<Module>/<endpoint-path> hierarchy, localhost + cURL success requests with non-idempotency notes, and /testes/ ENUM-driven error_<enum_name>_<status> and stress_<scenario>_<status> items without executing requests) - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic link .agents/skills/postman/SKILL.md -> ../../../AI/GEMINI/skills/documentation/postman/postman.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Integrate postman into documentation-orchestrator (AI/GEMINI/skills/documentation/documentation.md), documentation-admin (AI/GEMINI/agents/documentation-admin.md), nievo-orchestrator, git-pull-request, AI/Rules.md, GEMINI.md, and README.md - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [CHG] - Configure git skills and git-admin for Official GitHub MCP compatibility

Detalhes:

- Update git-branch, git-commit, git-push-request, and git-pull-request skills in AI/GEMINI/skills/git/ with Official GitHub MCP (github-official via nievo_ai Docker MCP Gateway) capability mappings, strict local-vs-MCP safety boundaries, and gh CLI / manual fallbacks - pedrovotf - 2026-11-27 - N/A - PENDING
- Align AI/GEMINI/agents/git-admin.md with canonical Official GitHub MCP tool names, nievo_ai Docker MCP Gateway references, and corrected .agents/skills/ symlink paths - pedrovotf - 2026-11-27 - N/A - PENDING
- Document Git skills Official GitHub MCP compatibility matrix in README.md - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [CHG] - Refactor setup skill for Linux/Windows project-scoped MCP mold, Docker MCP Gateway catalog + servers + secrets, and stitch

Detalhes:

- Add canonical project-scoped MCP templates in AI/GEMINI/skills/setup/templates/ (mcp_secrets_example.txt, plugin.json, and mcp_config.example.json) using --catalog + --servers + project-local --secrets and postman.postman-api-key to avoid docker-mcp v0.44.3+ --profile vs --secrets mutual exclusion - pedrovotf - 2026-11-27 - N/A - PENDING
- Update AI/GEMINI/skills/setup/setup.md to run MCP mold creation first (preparing .gitignore before copying mcp_secrets_example.txt to mcp_secrets.env, scaffolding .agents/plugins/nievo/, and configuring docker-mcp CLI with --catalog, --servers, and project-local --secrets on Linux and Windows strictly at project scope) - pedrovotf - 2026-11-27 - N/A - PENDING
- Update README.md with Linux and Windows project-scoped MCP setup (--catalog + --servers + --secrets), .gitignore protection, mcp_secrets_example.txt to mcp_secrets.env flow, .agents/plugins/nievo/ config, troubleshooting, and ln -sfn directory symlink fix - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add setup skill for full NIEVO onboarding and plugin-scoped MCP configuration

Detalhes:

- Add setup skill in AI/GEMINI/skills/setup/setup.md for interactive submodule, GEMINI.md, relative symbolic links, and project/plugin-scoped nievo_ai MCP configuration (.agents/plugins/<name>/plugin.json and mcp_config.json) with bundled GitHub MCP - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic link .agents/skills/setup/SKILL.md -> ../../../AI/GEMINI/skills/setup/setup.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Update README.md to document the setup skill, bundled GitHub MCP inside nievo_ai, and .agents/plugins/nievo-ai/ configuration - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [CHG] - Configure explicit tools, customization inheritance, and MCP permissions across custom agents

Detalhes:

- Configure delegation-only tools (invoke_subagent, manage_subagents, define_subagent, read_file, send_message, ask_question, update_steps, manage_task, schedule, search_web) plus inheritCustomizations and inheritMcp in AI/GEMINI/agents/nievo-orchestrator.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Configure file editing (write_file, edit_file), sandboxed terminal execution (run_command with commandExecutionPolicy: sandbox), inheritCustomizations, and inheritMcp in dotnet-admin.md, documentation-admin.md, and git-admin.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Document custom agent tool and MCP permission profiles in README.md - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add nievo-orchestrator custom agent for multi-agent workflow orchestration

Detalhes:

- Add nievo-orchestrator master agent definition in AI/GEMINI/agents/nievo-orchestrator.md coordinating dotnet-admin, documentation-admin, git-admin, and extensible future domain agents with interactive gate mediation - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic link .agents/agents/nievo-orchestrator.md -> ../../AI/GEMINI/agents/nievo-orchestrator.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Update README.md with nievo-orchestrator agent entry and verification example - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add dotnet-admin custom agent for .NET backend and Python Alembic orchestration

Detalhes:

- Add dotnet-admin agent definition in AI/GEMINI/agents/dotnet-admin.md orchestrating dotnet-gen-controller, dotnet-gen-entity, dotnet-gen-service, dotnet-gen-test, python-alembic-gen-entity, python-alembic-gen-index, python-alembic-gen-insert, and python-alembic-gen-keys skills - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic link .agents/agents/dotnet-admin.md -> ../../AI/GEMINI/agents/dotnet-admin.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Update README.md with dotnet-admin agent entry and verification example - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add Python Alembic skills for data inserts, indexes, and PK/FK keys

Detalhes:

- Add python-alembic-gen-insert skill for interactive Alembic seed/data insert migrations with audit column defaults and FK-aware downgrade cleanup - pedrovotf - 2026-11-27 - N/A - PENDING
- Add python-alembic-gen-index skill for interactive Alembic standard, unique, composite, partial, and concurrent index migrations with schema-qualified drops - pedrovotf - 2026-11-27 - N/A - PENDING
- Add python-alembic-gen-keys skill for interactive Alembic primary key and foreign key constraint migrations using idempotent pg_constraint guards - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic links for python-alembic-gen-insert, python-alembic-gen-index, and python-alembic-gen-keys under .agents/skills/ - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add documentation-admin custom agent for diff-driven documentation and diagram orchestration

Detalhes:

- Add documentation-admin agent definition in AI/GEMINI/agents/documentation-admin.md orchestrating documentation-orchestrator, dbdiagram-database-diagram, mermaid-class-diagram, mermaid-sequence-diagram, mermaid-use-case-diagram, and mermaid-decision-flow skills - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic link .agents/agents/documentation-admin.md -> ../../AI/GEMINI/agents/documentation-admin.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Update README.md with documentation-admin agent entry and verification example - pedrovotf - 2026-11-27 - N/A - PENDING

2026-11-27 - [NEW] - Add git-admin custom agent for Git workflow and GitHub board orchestration

Detalhes:

- Add git-admin agent definition in AI/GEMINI/agents/git-admin.md integrating official GitHub MCP (Docker MCP Gateway), GitHub CLI (gh), and the git-branch, git-commit, git-push-request, and git-pull-request skills - pedrovotf - 2026-11-27 - N/A - PENDING
- Register relative symbolic link .agents/agents/git-admin.md -> ../../AI/GEMINI/agents/git-admin.md - pedrovotf - 2026-11-27 - N/A - PENDING
- Update README.md with custom agents symbolic link setup, mandatory registration addendum, verification commands, and git-admin overview - pedrovotf - 2026-11-27 - N/A - PENDING

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
