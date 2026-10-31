# Introduction

This repo is a generic global AI skills and workloads for NIEVO projects

## How-to

In the new repo, run

```shell
git submodule add https://github.com/pedrovtof/Nievo-Skills.git ./Nievo-AI/
```

From that point, create a new default AI path

GEMINI

```txt
# Diretrizes Obrigatórias do Projeto

Em toda inicialização e execução neste projeto, o agente DEVE seguir e considerar ativas as regras e instruções da seguinte base:

@[Nievo-AI/GEMINI.md](Nievo-AI/GEMINI.md)
```

Finaly, create a LINK files for your device, you should ask to the AI generate them.
Is that because you need to have a .agents in your root path, this project already has a link shortcut in .agents, you must only create a shortcut in the new project that use this as a submodule.
