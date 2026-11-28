# Nievo-Skills (Nievo-AI)

Generic global AI skills, directives, workflows, and rule orchestrations for **NIEVO** projects.

This repository is designed to be consumed as a Git submodule (`Nievo-AI`) across multiple projects, providing a single source of truth for AI agent directives, skills, and coding standards.

---

## 1. Submodule Integration

To include this repository in a new or existing NIEVO project:

> [!IMPORTANT]
> **Regra Obrigatória (`branch = develop`)**:
> Em todos os projetos consumidores, o submódulo `Nievo-AI` **DEVE** sempre apontar e rastrear exclusivamente a branch **`develop`** (`branch = develop` no `.gitmodules` e checkout local na branch `develop` — nunca `main` ou outra branch).

```bash
# Add submodule into ./Nievo-AI directory tracking branch develop (mandatory)
git submodule add -b develop https://github.com/pedrovtof/Nievo-Skills.git ./Nievo-AI/

# Initialize submodules and ensure Nievo-AI is on branch develop
git submodule update --init --recursive
git -C Nievo-AI checkout develop
```

> [!NOTE]
> When cloning an existing project that already includes `Nievo-AI`, initialize submodules and ensure the `develop` branch is active:
> ```bash
> git submodule update --init --recursive
> git -C Nievo-AI checkout develop
> ```

### Enforcing `develop` on an Existing Submodule

If `Nievo-AI` was previously added without `-b develop`, is on `main`, or is in `HEAD detached` state, enforce the `develop` branch tracking with:

```bash
git submodule set-branch --branch develop Nievo-AI
git submodule sync -- Nievo-AI
git -C Nievo-AI fetch origin develop
git -C Nievo-AI checkout develop || git -C Nievo-AI checkout -b develop origin/develop
git -C Nievo-AI pull --ff-only origin develop
```

### Updating the Submodule to the Latest `develop`

```bash
git -C Nievo-AI checkout develop && git -C Nievo-AI pull --ff-only origin develop
```

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
    [ -d "$skill" ] && ln -sfn "../../$skill" ".agents/skills/$(basename "$skill")"
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
  postman -> ../../Nievo-AI/.agents/skills/postman
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

### Submodule Branch Verification (Mandatory `develop`)

Run on Linux/macOS or Windows (PowerShell) in the host project root — **both commands must output `develop`**:

```bash
# Verify Nievo-AI submodule tracks and is checked out on branch develop
git config -f .gitmodules --get submodule.Nievo-AI.branch
git -C Nievo-AI branch --show-current
```

---

## 6. MCP Server (Docker MCP Gateway + Stitch) & Skill `setup`

All NIEVO MCP configurations are **strictly project-scoped (never global)** and live inside the consumer repository under `.agents/plugins/nievo/` and `mcp_secrets.env` (protected by `.gitignore`).

- **`nievo_ai`**: **Docker MCP Gateway** running servers from the official Docker MCP catalog (`https://desktop.docker.com/mcp/catalog/v3/catalog.yaml`) with `--servers brave,github-official,postman,gemini-api-docs,docker-docs,javadocs,terraform` and project-local `--secrets "<PROJECT_ROOT>/mcp_secrets.env"`.
- **`stitch`**: Remote **Streamable HTTP** MCP server (`https://stitch.googleapis.com/mcp`) for UI/design workflows with Google Stitch.

> [!IMPORTANT]
> **Why `--catalog` + `--servers` + `--secrets` Instead of `--profile` + `--secrets`**:
> In `docker-mcp v0.44.3+`, `--profile` is **mutually exclusive** with `--secrets`, `--catalog`, and `--servers` (`cannot use --profile with ... --secrets ...`). Using `--catalog` + `--servers` + `--secrets` ensures 100% project-scoped secrets on **Linux (without Docker Desktop / `docker pass`)** and **Windows**, and already includes `github-official` (no separate GitHub MCP entry needed).

### Automated Full Onboarding (`setup` Skill)

Use the `setup` skill ([AI/GEMINI/skills/setup/setup.md](AI/GEMINI/skills/setup/setup.md) / `.agents/skills/setup/SKILL.md`) to interactively configure the full NIEVO environment on **Linux** or **Windows**. Its workflow **always starts by creating the project-scoped MCP mold**:

1. **`.gitignore` Guard & Secrets Mold**: Ensures `mcp_secrets.env` and `.agents/plugins/nievo/mcp_config.json` are ignored by Git before generating `mcp_secrets_example.txt` and copying it to `mcp_secrets.env`.
2. **Plugin-Scoped MCP Config**: Scaffolds `.agents/plugins/nievo/plugin.json`, `.agents/plugins/nievo/mcp_config.example.json`, and `.agents/plugins/nievo/mcp_config.json` (`nievo_ai` + `stitch`) pointing `--secrets` to the local project's `mcp_secrets.env`.
3. **Docker MCP CLI Plugin**: Guides `docker-mcp` CLI plugin installation (Linux & Windows) and optional extra server / `--config` customization.
4. **Submodule, `GEMINI.md` & Symlinks**: Initializes/enforces `./Nievo-AI/` strictly on branch `develop`, `GEMINI.md`, and cross-platform relative symbolic links (`AI/`, `.agents/skills/`, `.agents/agents/`), then validates everything with `--dry-run`.

---

### Manual Setup (Linux & Windows — Project-Scoped Only)

#### Step 6.1: Install the `docker-mcp` CLI Plugin

- **Linux (`amd64` / `arm64`)**:
  ```bash
  mkdir -p ~/.docker/cli-plugins

  # x86_64 (amd64)
  curl -fsSL https://github.com/docker/mcp-gateway/releases/download/v0.44.3/docker-mcp-linux-amd64.tar.gz \
    | tar -xz -C ~/.docker/cli-plugins/

  # Or ARM64 (arm64)
  # curl -fsSL https://github.com/docker/mcp-gateway/releases/download/v0.44.3/docker-mcp-linux-arm64.tar.gz \
  #   | tar -xz -C ~/.docker/cli-plugins/

  chmod +x ~/.docker/cli-plugins/docker-mcp
  docker mcp version
  ```

- **Windows — Option A (Recommended: Docker Desktop)**:
  1. Start **Docker Desktop for Windows**.
  2. Enable **MCP Toolkit** under **Settings > Features / Beta features**.
  3. Validate in PowerShell: `docker mcp version`.

- **Windows — Option B (Standalone PowerShell CLI)**:
  ```powershell
  New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.docker\cli-plugins" | Out-Null

  # x64 (amd64) — replace amd64 with arm64 for ARM64
  curl.exe -fsSL -L "https://github.com/docker/mcp-gateway/releases/download/v0.44.3/docker-mcp-windows-amd64.tar.gz" -o "$env:TEMP\docker-mcp.tar.gz"
  tar -xzf "$env:TEMP\docker-mcp.tar.gz" -C "$env:USERPROFILE\.docker\cli-plugins"
  Remove-Item "$env:TEMP\docker-mcp.tar.gz"
  docker mcp version
  ```

#### Step 6.2: Prepare `.gitignore`, `mcp_secrets.env` & `.agents/plugins/nievo/`

1. Add the project-local secret and machine-specific config files to `.gitignore` **before** creating `mcp_secrets.env`:
   ```gitignore
   mcp_secrets.env
   .agents/plugins/nievo/mcp_config.json
   ```

2. Copy the mold [AI/GEMINI/skills/setup/templates/mcp_secrets_example.txt](AI/GEMINI/skills/setup/templates/mcp_secrets_example.txt) to `mcp_secrets_example.txt` and `mcp_secrets.env` in your project root:
   ```bash
   # Linux (Bash)
   cp Nievo-AI/AI/GEMINI/skills/setup/templates/mcp_secrets_example.txt mcp_secrets_example.txt
   cp mcp_secrets_example.txt mcp_secrets.env
   chmod 600 mcp_secrets.env
   ```
   ```powershell
   # Windows (PowerShell)
   Copy-Item Nievo-AI\AI\GEMINI\skills\setup\templates\mcp_secrets_example.txt mcp_secrets_example.txt
   Copy-Item mcp_secrets_example.txt mcp_secrets.env
   ```

3. Create `.agents/plugins/nievo/plugin.json`:
   ```json
   {
     "name": "nievo",
     "repository": "https://github.com/pedrovtof/Nievo-Skills"
   }
   ```

4. Create `.agents/plugins/nievo/mcp_config.example.json` (template) and `.agents/plugins/nievo/mcp_config.json` (git-ignored, using your project's absolute path to `mcp_secrets.env` and your local Stitch API key):
   ```json
   {
     "mcpServers": {
       "nievo_ai": {
         "command": "docker",
         "args": [
           "mcp",
           "gateway",
           "run",
           "--catalog",
           "https://desktop.docker.com/mcp/catalog/v3/catalog.yaml",
           "--servers",
           "brave,github-official,postman,gemini-api-docs,docker-docs,javadocs,terraform",
           "--secrets",
           "<PROJECT_ROOT>/mcp_secrets.env"
         ]
       },
       "stitch": {
         "type": "http",
         "url": "https://stitch.googleapis.com/mcp",
         "headers": {
           "X-Goog-Api-Key": "<SUA_CHAVE_API_STITCH>"
         }
       }
     }
   }
   ```
   - On **Linux**, set `"<PROJECT_ROOT>/mcp_secrets.env"` to e.g. `"/path/to/your-project/mcp_secrets.env"`.
   - On **Windows**, set it to e.g. `"C:\\path\\to\\your-project\\mcp_secrets.env"` (or `"C:/path/to/your-project/mcp_secrets.env"`).
   - To enable extra catalog servers (e.g. `kubernetes`, `kubectl-mcp-server`, `prometheus`, `sonarqube`, `dockerhub`, `clickhouse`, `redis`, `mcp-discord`, `mcp-reddit`, `openapi-schema`), add them to `--servers` and, if local non-secret configuration is needed, pass `"--config", "<PROJECT_ROOT>/.agents/plugins/nievo/mcp_servers_config.yaml"`.

#### Step 6.3: Validate & Reload Antigravity

```bash
# Linux (Bash)
git check-ignore -v mcp_secrets.env .agents/plugins/nievo/mcp_config.json
python3 -m json.tool .agents/plugins/nievo/plugin.json >/dev/null && echo "plugin.json OK"
python3 -m json.tool .agents/plugins/nievo/mcp_config.json >/dev/null && echo "mcp_config.json OK"
docker mcp gateway run \
  --catalog https://desktop.docker.com/mcp/catalog/v3/catalog.yaml \
  --servers brave,github-official,postman,gemini-api-docs,docker-docs,javadocs,terraform \
  --secrets "$PWD/mcp_secrets.env" \
  --dry-run
```

```powershell
# Windows (PowerShell)
git check-ignore -v mcp_secrets.env .agents/plugins/nievo/mcp_config.json
Get-Content .\.agents\plugins\nievo\plugin.json -Raw | ConvertFrom-Json | Out-Null; "plugin.json OK"
Get-Content .\.agents\plugins\nievo\mcp_config.json -Raw | ConvertFrom-Json | Out-Null; "mcp_config.json OK"
docker mcp gateway run `
  --catalog https://desktop.docker.com/mcp/catalog/v3/catalog.yaml `
  --servers brave,github-official,postman,gemini-api-docs,docker-docs,javadocs,terraform `
  --secrets "$((Get-Location).Path)\mcp_secrets.env" `
  --dry-run
```

After `--dry-run` exits `0`, reload Antigravity and check **Additional Options (...) > MCP Servers** to confirm `nievo_nievo_ai` and `nievo_stitch` are active.

#### Quick Troubleshooting

| Erro observado | Causa | Solução |
| :--- | :--- | :--- |
| `cannot use --profile with ... --secrets ...` | No `docker-mcp v0.44.3+`, `--profile` é mutuamente exclusivo com `--secrets`, `--catalog` e `--servers` | Usar `--catalog https://desktop.docker.com/mcp/catalog/v3/catalog.yaml --servers ... --secrets "<PROJECT_ROOT>/mcp_secrets.env"` |
| `Reading catalog from [docker-mcp.yaml]` e `0 tools listed` | `--servers` sem `--catalog` procura `docker-mcp.yaml` local inexistente | Passar `--catalog https://desktop.docker.com/mcp/catalog/v3/catalog.yaml` |
| `docker: Error response from daemon: no command specified` | Tentativa de rodar um profile OCI com `docker run` | Usar `docker mcp gateway run --catalog ... --servers ... --secrets "<PROJECT_ROOT>/mcp_secrets.env"` |
| `docker: unknown command: docker mcp` | Plugin `docker-mcp` ausente em `cli-plugins` | Instalar seguindo o **Step 6.1** (Linux ou Windows) |
| `docker pass has not been installed` | Ambiente sem Docker Desktop / `docker pass` | Já resolvido pelo padrão do projeto passando `--catalog` + `--servers` + `--secrets "<PROJECT_ROOT>/mcp_secrets.env"` |
| `Chave ou segredo exposto em arquivo untracked` | `.gitignore` sem as regras locais | Garantir `mcp_secrets.env` e `.agents/plugins/nievo/mcp_config.json` no `.gitignore` e versionar apenas `mcp_secrets_example.txt` e `mcp_config.example.json` |
| `Submódulo Nievo-AI em main, outra branch ou HEAD detached` | Submódulo adicionado sem `-b develop` ou atualizado fora da branch obrigatória | Executar `git submodule set-branch --branch develop Nievo-AI && git submodule sync -- Nievo-AI && git -C Nievo-AI fetch origin develop && git -C Nievo-AI checkout develop && git -C Nievo-AI pull --ff-only origin develop` |

### Git Skills & Official GitHub MCP Integration

All four Git skills (`AI/GEMINI/skills/git/*.md`, exposed via `.agents/skills/git-*/SKILL.md`) are configured to work natively with the **`github-official`** server inside the project-scoped **`nievo_ai`** Docker MCP Gateway (using `gh` CLI as complement/fallback and local/Markdown fallbacks when unauthenticated):

| Skill | Official GitHub MCP (`github-official`) Role | Local-Only Git Boundary | Fallback (`gh` / Manual) |
| :--- | :--- | :--- | :--- |
| **`git-branch`** | Read-only discovery of remote branches (`list_branches`), issue/card metadata (`issue_read` / `get_issue`), and boards (`projects` when enabled) | Branch creation strictly via `git checkout -b` after user confirmation (no `create_branch` / MCP writes) | `git ls-remote`, `gh repo view`, `gh issue view`, `gh api graphql` / `gh project list` |
| **`git-commit`** | Optional read-only card (`issue_read` / `get_issue`) and author (`get_me`) verification for footers and `changelog.md` | Staging and committing strictly via local `git add` / `git commit` (no `git pull`, `git push`, or MCP file writes) | `git config user.name`, `gh issue view`, offline local commit |
| **`git-push-request`** | Remote branch/card/board validation (`list_branches`, `issue_read` / `get_issue`) and existing-PR check (`list_pull_requests`, `update_pull_request`) | Remote sync (`git pull`), `changelog.md`-only conflict auto-resolution, diff inspection, and `git push -u origin` (never MCP `push_files`) | `git ls-remote`, `gh issue view`, `gh pr list`, `gh pr edit`, formatted PR Markdown |
| **`git-pull-request`** | Duplicate PR guard (`list_pull_requests`), PR creation (`create_pull_request`), PR updates (`update_pull_request`), and review/check inspection (`pull_request_read`) | Quality gate checks and `git push -u origin` before PR creation (never auto-merges via `merge_pull_request`) | `gh pr list`, `gh pr create`, `gh pr edit`, `gh pr view` / `gh pr checks`, manual Markdown |

### Postman Skill & Postman MCP Integration

The **`postman`** skill ([AI/GEMINI/skills/documentation/postman/postman.md](AI/GEMINI/skills/documentation/postman/postman.md), exposed via `.agents/skills/postman/SKILL.md`) integrates with the **`postman`** server inside the project-scoped **`nievo_ai`** Docker MCP Gateway (using `postman.postman-api-key` from `mcp_secrets.env`) and is automatically orchestrated by `documentation-orchestrator` and `documentation-admin` whenever HTTP endpoints are created or modified:

- **Automatic Workspace & Language-Agnostic Hierarchy**: Creates the project Workspace (e.g., `Nievo-portal`) if missing and maps modules to `./<Module>/<endpoint-path>` regardless of stack (e.g., `Nievo.Portal.Core` with route `public/admin/user/v1/create` -> `./Core/public/admin/user/v1/create`).
- **`LOCAL` Environment Only & Reusable Variables**: Auto-creates (or non-destructively merges) **exclusively** the **`LOCAL`** environment in the workspace for reusable variables (`host_<module>`, `host_port_<module>`, `bearer_token_<module>`, `default_page`, `default_page_size`), referencing them with `{{...}}` while keeping endpoint-specific fields literal — **never** reading or modifying `DEV`, `QA`, `STAGING`, `PROD`, or any non-`LOCAL` Postman environment, and **never** reading project `.env` / `.env.*` / `mcp_secrets.env` files.
- **`success` Request & Non-Idempotency Note**: Creates a `{{host_*}}:{{host_port_*}}` + `cURL`-documented `success` request and explicitly notes first-run / uniqueness caveats when repeating the same payload cannot succeed (e.g., user creation).
- **`/testes/` Folder (`error_*` & `stress_*`)**: Creates one `error_<enum_name>_<expected_status>` request per application error ENUM handled by the endpoint (e.g., `error_user_already_exists_400`) alongside contextual `stress_<scenario>_<expected_status>` pressure tests.
- **Strict Creation-Only Boundary**: Creates and updates Postman artifacts (or emits Collection v2.1.0 JSON + `cURL` fallback when MCP is unavailable) **without executing requests or validating runtime responses**.

---

## 7. Custom Agents

Custom agents live canonically under `AI/GEMINI/agents/` and are exposed to the AI CLI/IDE via relative symlinks in `.agents/agents/`:

| Agent | Source | Symlink | Purpose |
| :--- | :--- | :--- | :--- |
| **`documentation-admin`** | [AI/GEMINI/agents/documentation-admin.md](AI/GEMINI/agents/documentation-admin.md) | `.agents/agents/documentation-admin.md` | Inspects branch diffs and codebase changes, searches `Documentation/config/docs/`, updates or scaffolds MkDocs docs and navigation, and orchestrates `documentation-orchestrator`, `dbdiagram-database-diagram`, `mermaid-class-diagram`, `mermaid-sequence-diagram`, `mermaid-use-case-diagram`, `mermaid-decision-flow`, and `postman` (creation-only MCP sync). |
| **`dotnet-admin`** | [AI/GEMINI/agents/dotnet-admin.md](AI/GEMINI/agents/dotnet-admin.md) | `.agents/agents/dotnet-admin.md` | Orchestrates end-to-end Python Alembic migrations (`python-alembic-gen-entity`, `python-alembic-gen-keys`, `python-alembic-gen-index`, `python-alembic-gen-insert`) and .NET backend engineering (`dotnet-gen-entity`, `dotnet-gen-controller`, `dotnet-gen-service`, `dotnet-gen-test`) while enforcing mandatory audit columns, `active = true` filtering, ORM/Dapper split, Response contracts, and full xUnit test coverage. |
| **`git-admin`** | [AI/GEMINI/agents/git-admin.md](AI/GEMINI/agents/git-admin.md) | `.agents/agents/git-admin.md` | Validates the current repository and GitHub Project/Board via Official GitHub MCP (Docker MCP Gateway) and GitHub CLI (`gh`), asks the user **only for the card**, and orchestrates `git-branch`, `git-commit`, `git-push-request`, and `git-pull-request` (skipping branch creation when the topic branch already exists). |
| **`nievo-orchestrator`** | [AI/GEMINI/agents/nievo-orchestrator.md](AI/GEMINI/agents/nievo-orchestrator.md) | `.agents/agents/nievo-orchestrator.md` | Master workflow orchestrator (`mainAgent: true`, `subagent: false`) that coordinates end-to-end delivery across `dotnet-admin`, `documentation-admin`, `git-admin`, and extensible future domain agents, mediating interactive human gates via `send_message` and verifying inter-stage quality gates. |

> [!NOTE]
> **Explicit Permission Profiles**:
> - `nievo-orchestrator` operates in **delegation-only** mode (`invoke_subagent`, `manage_subagents`, `define_subagent`, `read_file`, `send_message`, `ask_question`, `update_steps`, `manage_task`, `schedule`, `search_web`) with `inheritCustomizations: true` and `inheritMcp: true`, without direct file-write or shell execution tools.
> - `dotnet-admin`, `documentation-admin`, and `git-admin` explicitly enable `read_file`, `write_file`, `edit_file`, and `run_command` under `commandExecutionPolicy: sandbox`, alongside `send_message`, `manage_task`, `ask_question`, `update_steps`, `search_web`, `inheritCustomizations: true`, and `inheritMcp: true`.
