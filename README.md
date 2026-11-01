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
3. Para procedimentos especializados (git, documentação, banco de dados, linguagens), consultar as skills correspondentes em `AI/GEMINI/skills/` e `.agents/skills/`.
```

---

## 3. Symbolic Links Setup (Configuração de Links Simbólicos)

To make AI directives and skills discoverable by the AI agent CLI and extensions (such as Antigravity and Gemini CLI) without duplicating files, **relative symbolic links** must be configured in the host project root.

### Why Relative Symbolic Links?
- **Directive Resolution**: Directives in `GEMINI.md` reference `AI/...`. Creating an `AI` symlink pointing to `Nievo-AI/AI` allows these references to resolve seamlessly.
- **Skill Discovery**: The Agent system scans `.agents/skills/<skill-name>/SKILL.md` at the project root to load available skills.
- **Project Extensibility**: The host project maintains its own `.agents/skills/` directory. This allows a project to hold both shared submodule skills and project-specific custom skills without conflicts.
- **Portability**: Relative targets (such as `../../Nievo-AI/.agents/skills/...`) ensure links resolve properly regardless of where the repository is cloned, inside Docker containers, and when committed to Git.

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

## 4. Adendo Obrigatório: Registro de Novas Skills

> [!IMPORTANT]
> **Adendo: Criação de Link Simbólico para Cada Nova Skill**
>
> Como a pasta `.agents/skills/` do projeto hospedeiro é um diretório local (e não um link simbólico da pasta inteira), **sempre que uma nova skill for adicionada ou criada no submódulo `Nievo-AI`**, um novo link simbólico correspondente **DEVE** ser criado no projeto consumidor.
>
> **Exemplo Prático**:
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

---

## 5. Verification (Verificação dos Links)

To verify that all symbolic links are correctly configured and resolve without broken pointers:

- **Linux / macOS**:
  ```bash
  # Check AI link
  ls -la AI

  # Check all skill links
  ls -la .agents/skills/
  ```

  Expected output format:
  ```text
  AI -> Nievo-AI/AI
  dbdiagram -> ../../Nievo-AI/.agents/skills/dbdiagram
  documentation -> ../../Nievo-AI/.agents/skills/documentation
  ...
  ```

- **Windows (PowerShell)**:
  ```powershell
  Get-Item AI | Select-Object Name, LinkType, Target
  Get-ChildItem .agents\skills | Select-Object Name, LinkType, Target
  ```
