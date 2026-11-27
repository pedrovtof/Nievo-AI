# Nievo-Skills (Nievo-AI)

Generic global AI skills, directives, workflows, and rule orchestrations for **NIEVO** projects.

This repository is designed to be consumed as a Git submodule (`Nievo-AI`) across multiple projects, providing a single source of truth for AI agent directives, skills, and coding standards.

---

## 1. Submodule Integration

To include this repository in a new or existing NIEVO project:

```bash
# Add submodule into ./Nievo-AI directory
git submodule add https://github.com/pedrovtof/Nievo-Skills.git ./Nievo-AI/

# Initialize and pull submodules
git submodule update --init --recursive
```

> [!NOTE]
> When cloning an existing project that already includes `Nievo-AI`, initialize submodules with:
> ```bash
> git submodule update --init --recursive
> ```

---

## 2. Project Directives Configuration (`GEMINI.md`)

In the root of the consumer project, create or configure the `GEMINI.md` file to load base rules and instructions:

```markdown
# Diretrizes Obrigatórias do Projeto

Em toda inicialização e execução neste projeto, o agente DEVE seguir e considerar ativas as regras e instruções dos seguintes arquivos essenciais:

## Arquivos Base e Regras Globais

@[AI/Start-here.md](AI/Start-here.md)

@[AI/Default.md](AI/Default.md)

@[AI/Rules.md](AI/Rules.md)

---

## Instruções de Inicialização (Checklist Padrão)

Toda vez que interagir no projeto:

1. Respeitar o fluxo de [AI/Default.md](AI/Default.md):
   - Solicitar um breve resumo de contexto, se necessário.
   - Perguntar se o usuário deseja uma opinião/sugestão ou uma implementação/correção direta.
   - Se a tarefa envolver design/front-end, consultar `AI/Desing/`.
2. Cumprir integralmente todas as exigências de [AI/Rules.md](AI/Rules.md) (Changelog, README, Documentação, Testes, Diagramas Mermaid/dbdiagram, políticas de commit/branch e não leitura de `.env`).
3. Para procedimentos especializados (git, documentação, banco de dados, linguagens), consultar as skills e agentes correspondentes em `AI/GEMINI/skills/`, `AI/GEMINI/agents/`, `.agents/skills/` e `.agents/agents/`.
```

---

## 3. Symbolic Links Setup (Configuração de Links Simbólicos)

To make AI directives, skills, and custom agents discoverable by the AI agent CLI and extensions (such as Antigravity and Gemini CLI) without duplicating files, **relative symbolic links** must be configured in the host project root.

### Why Relative Symbolic Links?
- **Directive Resolution**: Directives in `GEMINI.md` reference `AI/...`. Creating an `AI` symlink pointing to `Nievo-AI/AI` allows these references to resolve seamlessly.
- **Skill & Agent Discovery**: The Agent system scans `.agents/skills/<skill-name>/SKILL.md` and `.agents/agents/<agent-name>.md` at the project root to load available skills and custom agents.
- **Project Extensibility**: The host project maintains its own `.agents/skills/` and `.agents/agents/` directories. This allows a project to hold both shared submodule assets and project-specific custom skills/agents without conflicts.
- **Portability**: Relative targets (such as `../../Nievo-AI/.agents/skills/...` and `../../Nievo-AI/.agents/agents/...`) ensure links resolve properly regardless of where the repository is cloned, inside Docker containers, and when committed to Git.

---

### Step 3.1: Link the `AI/` Directory

Create a symbolic link at the project root pointing to `Nievo-AI/AI`:

- **Linux / macOS (Bash)**:
  ```bash
  ln -s Nievo-AI/AI AI
  ```

- **Windows (PowerShell - run as Administrator or with Developer Mode)**:
  ```powershell
  New-Item -ItemType SymbolicLink -Path "AI" -Target "Nievo-AI\AI"
  ```

- **Windows (CMD)**:
  ```cmd
  mklink /D AI Nievo-AI\AI
  ```

---

### Step 3.2: Initialize the `.agents/skills/` Directory

Ensure the target directory exists in your host project:

- **Linux / macOS**:
  ```bash
  mkdir -p .agents/skills
  ```

- **Windows (PowerShell)**:
  ```powershell
  New-Item -ItemType Directory -Force -Path ".agents\skills"
  ```

---

### Step 3.3: Link Skills into `.agents/skills/`

Each skill directory must be linked individually into `.agents/skills/`.

#### Example: Linking `dbdiagram`
For example, to link the `dbdiagram` skill:

- **Linux / macOS (Bash)**:
  ```bash
  ln -s ../../Nievo-AI/.agents/skills/dbdiagram .agents/skills/dbdiagram
  ```

- **Windows (PowerShell)**:
  ```powershell
  New-Item -ItemType SymbolicLink -Path ".agents\skills\dbdiagram" -Target "..\..\Nievo-AI\.agents\skills\dbdiagram"
  ```

- **Windows (CMD)**:
  ```cmd
  mklink /D .agents\skills\dbdiagram ..\..\Nievo-AI\.agents\skills\dbdiagram
  ```

#### Batch Linking (All Existing Skills)
To link all skills present in `Nievo-AI/.agents/skills/` at once:

- **Linux / macOS (Bash)**:
  ```bash
  for skill in Nievo-AI/.agents/skills/*; do
    [ -d "$skill" ] && ln -sf "../../$skill" ".agents/skills/$(basename "$skill")"
  done
  ```

- **Windows (PowerShell)**:
  ```powershell
  Get-ChildItem -Directory -Path "Nievo-AI\.agents\skills" | ForEach-Object {
    New-Item -ItemType SymbolicLink -Force -Path ".agents\skills\$($_.Name)" -Target "..\..\Nievo-AI\.agents\skills\$($_.Name)"
  }
  ```

---

### Step 3.4: Initialize and Link Agents into `.agents/agents/`

Ensure `.agents/agents/` exists in your host project and link each custom agent `.md` file from `Nievo-AI/.agents/agents/`:

- **Linux / macOS (Bash)**:
  ```bash
  mkdir -p .agents/agents

  # Link an individual agent (e.g., git-admin)
  ln -sf ../../Nievo-AI/.agents/agents/git-admin.md .agents/agents/git-admin.md

  # Or batch-link all agents
  for agent in Nievo-AI/.agents/agents/*.md; do
    [ -e "$agent" ] && ln -sf "../../$agent" ".agents/agents/$(basename "$agent")"
  done
  ```

- **Windows (PowerShell - run as Administrator or with Developer Mode)**:
  ```powershell
  New-Item -ItemType Directory -Force -Path ".agents\agents"

  # Link an individual agent (e.g., git-admin)
  New-Item -ItemType SymbolicLink -Force -Path ".agents\agents\git-admin.md" -Target "..\..\Nievo-AI\.agents\agents\git-admin.md"

  # Or batch-link all agents
  Get-ChildItem -File -Filter "*.md" -Path "Nievo-AI\.agents\agents" | ForEach-Object {
    New-Item -ItemType SymbolicLink -Force -Path ".agents\agents\$($_.Name)" -Target "..\..\Nievo-AI\.agents\agents\$($_.Name)"
  }
  ```

- **Windows (CMD)**:
  ```cmd
  mkdir .agents\agents
  mklink .agents\agents\git-admin.md ..\..\Nievo-AI\.agents\agents\git-admin.md
  ```

---

## 4. Adendo Obrigatório: Registro de Novas Skills e Agentes

> [!IMPORTANT]
> **Adendo: Criação de Link Simbólico para Cada Nova Skill ou Agente**
>
> Como as pastas `.agents/skills/` e `.agents/agents/` do projeto hospedeiro são diretórios locais (e não links simbólicos das pastas inteiras), **sempre que uma nova skill ou um novo agente customizado for adicionado no submódulo `Nievo-AI`**, um novo link simbólico correspondente **DEVE** ser criado no projeto consumidor.
>
> **Exemplo Prático (Skill)**:
> Se uma nova skill chamada `security-audit` for criada em `Nievo-AI/.agents/skills/security-audit`:
> 1. No projeto hospedeiro, execute:
>    ```bash
>    # Linux / macOS
>    ln -s ../../Nievo-AI/.agents/skills/security-audit .agents/skills/security-audit
>
>    # Windows (PowerShell)
>    New-Item -ItemType SymbolicLink -Path ".agents\skills\security-audit" -Target "..\..\Nievo-AI\.agents\skills\security-audit"
>    ```
> 2. Sem este passo, o assistente/CLI de IA **não detectará** a nova skill no projeto hospedeiro.
>
> **Exemplo Prático (Agente Customizado)**:
> Se um novo agente chamado `git-admin.md` for adicionado em `Nievo-AI/.agents/agents/git-admin.md`:
> 1. No projeto hospedeiro, execute:
>    ```bash
>    # Linux / macOS
>    ln -sf ../../Nievo-AI/.agents/agents/git-admin.md .agents/agents/git-admin.md
>
>    # Windows (PowerShell)
>    New-Item -ItemType SymbolicLink -Force -Path ".agents\agents\git-admin.md" -Target "..\..\Nievo-AI\.agents\agents\git-admin.md"
>    ```
> 2. Sem este passo, o assistente/CLI de IA **não detectará** o novo agente customizado no projeto hospedeiro.

---

## 5. Verification (Verificação dos Links)

To verify that all symbolic links are correctly configured and resolve without broken pointers:

- **Linux / macOS**:
  ```bash
  # Check AI link
  ls -la AI

  # Check all skill links
  ls -la .agents/skills/

  # Check all custom agent links
  ls -la .agents/agents/
  ```

  Expected output format:
  ```text
  AI -> Nievo-AI/AI
  dbdiagram -> ../../Nievo-AI/.agents/skills/dbdiagram
  documentation -> ../../Nievo-AI/.agents/skills/documentation
  setup -> ../../Nievo-AI/.agents/skills/setup
  ...
  documentation-admin.md -> ../../Nievo-AI/.agents/agents/documentation-admin.md
  dotnet-admin.md -> ../../Nievo-AI/.agents/agents/dotnet-admin.md
  git-admin.md -> ../../Nievo-AI/.agents/agents/git-admin.md
  nievo-orchestrator.md -> ../../Nievo-AI/.agents/agents/nievo-orchestrator.md
  ```

- **Windows (PowerShell)**:
  ```powershell
  Get-Item AI | Select-Object Name, LinkType, Target
  Get-ChildItem .agents\skills | Select-Object Name, LinkType, Target
  Get-ChildItem .agents\agents | Select-Object Name, LinkType, Target
  ```

---

## 6. MCP Server (Repositório Docker) & Skill `setup`

To run the Nievo AI MCP server via Docker, use the official image repository (which **already includes the GitHub MCP server**):

- **Image**: `docker.io/pedrovotf/nievo_ai:latest`

### Automated Full Onboarding (`setup` Skill)

Use the `setup` skill ([AI/GEMINI/skills/setup/setup.md](AI/GEMINI/skills/setup/setup.md) / `.agents/skills/setup/SKILL.md`) to interactively configure the full NIEVO environment in a project:
- Verify/initialize the `Nievo-AI` submodule and `GEMINI.md`
- Create cross-platform relative symbolic links (`AI/`, `.agents/skills/`, `.agents/agents/`)
- Scaffold project/plugin-scoped MCP configuration under `.agents/plugins/nievo-ai/` (`plugin.json` + `mcp_config.json`)
- Pull and verify `docker.io/pedrovotf/nievo_ai:latest`

### Pull & Run

```bash
# Pull the latest image
docker pull docker.io/pedrovotf/nievo_ai:latest

# Run the MCP server container (stdio)
docker run -i --rm docker.io/pedrovotf/nievo_ai:latest
```

### Project/Plugin-Scoped MCP Configuration (`.agents/plugins/nievo-ai/`)

In Antigravity project/plugin scope, create `.agents/plugins/nievo-ai/plugin.json`:

```json
{
  "name": "nievo-ai",
  "repository": "https://github.com/pedrovtof/Nievo-Skills"
}
```

And add the `nievo_ai` server entry in `.agents/plugins/nievo-ai/mcp_config.json`:

```json
{
  "mcpServers": {
    "nievo_ai": {
      "command": "docker",
      "args": [
        "run",
        "-i",
        "--rm",
        "docker.io/pedrovotf/nievo_ai:latest"
      ]
    }
  }
}
```

> [!NOTE]
> Because `docker.io/pedrovotf/nievo_ai:latest` already embeds the GitHub MCP, no separate GitHub MCP entry is needed. Additional Docker flags (such as `-e ENV_VAR` pass-throughs or `-v` volume mounts) can be added to `args` before the image name if required by your host setup—never hardcode raw secrets in committed JSON files.

---

## 7. Custom Agents

Custom agents live canonically under `AI/GEMINI/agents/` and are exposed to the AI CLI/IDE via relative symlinks in `.agents/agents/`:

| Agent | Source | Symlink | Purpose |
| :--- | :--- | :--- | :--- |
| **`documentation-admin`** | [AI/GEMINI/agents/documentation-admin.md](AI/GEMINI/agents/documentation-admin.md) | `.agents/agents/documentation-admin.md` | Inspects branch diffs and codebase changes, searches `Documentation/config/docs/`, updates or scaffolds MkDocs docs and navigation, and orchestrates `documentation-orchestrator`, `dbdiagram-database-diagram`, `mermaid-class-diagram`, `mermaid-sequence-diagram`, `mermaid-use-case-diagram`, and `mermaid-decision-flow`. |
| **`dotnet-admin`** | [AI/GEMINI/agents/dotnet-admin.md](AI/GEMINI/agents/dotnet-admin.md) | `.agents/agents/dotnet-admin.md` | Orchestrates end-to-end Python Alembic migrations (`python-alembic-gen-entity`, `python-alembic-gen-keys`, `python-alembic-gen-index`, `python-alembic-gen-insert`) and .NET backend engineering (`dotnet-gen-entity`, `dotnet-gen-controller`, `dotnet-gen-service`, `dotnet-gen-test`) while enforcing mandatory audit columns, `active = true` filtering, ORM/Dapper split, Response contracts, and full xUnit test coverage. |
| **`git-admin`** | [AI/GEMINI/agents/git-admin.md](AI/GEMINI/agents/git-admin.md) | `.agents/agents/git-admin.md` | Validates the current repository and GitHub Project/Board via Official GitHub MCP (Docker MCP Gateway) and GitHub CLI (`gh`), asks the user **only for the card**, and orchestrates `git-branch`, `git-commit`, `git-push-request`, and `git-pull-request` (skipping branch creation when the topic branch already exists). |
| **`nievo-orchestrator`** | [AI/GEMINI/agents/nievo-orchestrator.md](AI/GEMINI/agents/nievo-orchestrator.md) | `.agents/agents/nievo-orchestrator.md` | Master workflow orchestrator (`mainAgent: true`, `subagent: false`) that coordinates end-to-end delivery across `dotnet-admin`, `documentation-admin`, `git-admin`, and extensible future domain agents, mediating interactive human gates via `send_message` and verifying inter-stage quality gates. |

> [!NOTE]
> **Explicit Permission Profiles**:
> - `nievo-orchestrator` operates in **delegation-only** mode (`invoke_subagent`, `manage_subagents`, `define_subagent`, `read_file`, `send_message`, `ask_question`, `update_steps`, `manage_task`, `schedule`, `search_web`) with `inheritCustomizations: true` and `inheritMcp: true`, without direct file-write or shell execution tools.
> - `dotnet-admin`, `documentation-admin`, and `git-admin` explicitly enable `read_file`, `write_file`, `edit_file`, and `run_command` under `commandExecutionPolicy: sandbox`, alongside `send_message`, `manage_task`, `ask_question`, `update_steps`, `search_web`, `inheritCustomizations: true`, and `inheritMcp: true`.
