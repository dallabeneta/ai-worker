# Interactive & Distributed Multi-Agent Godot Pipeline

## Descrição do Projeto
Este projeto é uma arquitetura de orquestração multi-agente local interativa para o desenvolvimento de jogos na engine Godot. Ele se conecta a repositórios Git remotos para colaboração distribuída, mas possui um diferencial poderoso: **uma interface de chat via Linha de Comando (CLI) com capacidade de auto-modificação.**

Ao iniciar o sistema, o usuário entra em um chat direto com o Orquestrador. Através deste terminal, o usuário pode pedir para que os agentes construam o jogo no Godot (puxando tarefas do repositório remoto) OU pedir para que o Orquestrador melhore o próprio código da pipeline (adicionando novas ferramentas, alterando prompts dos agentes ou otimizando a lógica de integração).

## Stack Tecnológica
*   **Interface Principal:** Chat Interativo via CLI (Linha de Comando).
*   **Engine Alvo:** Godot Engine 4.x (GDScript e `.tscn`).
*   **Orquestração de Agentes:** CrewAI / LangChain (Python).
*   **Processamento de Linguagem (LLM):** Ollama (Llama 3, DeepSeek Coder) rodando localmente.
*   **Geração de Gráficos:** ComfyUI via API local.
*   **Integração e Sincronização:** GitPython (para versionamento do jogo e do próprio orquestrador).

## Papéis dos Agentes (Agents)

1.  **Interactive Orchestrator (O Cérebro / CLI):**
    *   **Função:** Ponto de contato direto com o usuário via terminal.
    *   **Ação:** Interpreta o input do usuário em tempo real. Decide se a requisição é sobre o desenvolvimento do jogo (Godot) ou sobre a evolução da própria pipeline (Python). Delega as tarefas dinamicamente.
2.  **Meta-Developer Agent (O Engenheiro da Pipeline):**
    *   **Função:** Responsável pela auto-modificação e melhoria do sistema.
    *   **Ação:** Lê, edita e cria novos scripts na pasta `/tools` e `/agents`. Pode criar novas integrações (ex: "Adicione uma tool para ler PDFs no projeto") alterando o código-fonte do próprio orquestrador em tempo real.
3.  **Godot Programmer Agent (Programador de Jogo):**
    *   **Função:** Desenvolve o jogo.
    *   **Ação:** Escreve GDScript e estrutura cenas do Godot no repositório clonado.
4.  **Art Director Agent (Artista):**
    *   **Função:** Gera a arte do jogo.
    *   **Ação:** Usa o ComfyUI para criar sprites e os salva na pasta do projeto Godot.
5.  **Sync Agent (Gerente de Repositório):**
    *   **Função:** Lida com o Git remoto.
    *   **Ação:** Sincroniza o To-Do list, resolve merges simples e faz commits das alterações do jogo ou das melhorias feitas pelo Meta-Developer.

## Ferramentas Personalizadas (Tools)
*   **Ferramentas de Jogo (Godot):** `write_godot_files()`, `generate_local_image()`.
*   **Ferramentas de Sincronização:** `fetch_remote_tasks()`, `git_push_changes()`.
*   **Ferramentas de Meta-Programação (Novas):** 
    *   `read_orchestrator_code(filepath)`: Permite ler como os agentes e ferramentas atuais estão programados.
    *   `modify_orchestrator_code(filepath, new_code)`: Altera o código Python da própria pipeline.
    *   `hot_reload_system()`: Recarrega as configurações em memória após uma modificação para aplicar as melhorias instantaneamente sem fechar o chat.

## Fluxo de Trabalho (Workflow)
1. O usuário executa `python main.py` e o terminal abre uma interface de chat: `[Orquestrador]: Olá! O que vamos construir ou melhorar hoje?`
2. **Cenário A (Desenvolver o Jogo):** O usuário diz "Sincronize com o repo remoto e faça as tarefas de hoje". O Orquestrador aciona o Sync Agent, lê o To-Do List, e aciona o Programador Godot e o Artista.
3. **Cenário B (Auto-Modificação):** O usuário diz no chat "Crie uma nova Tool que permita ao artista aplicar filtros nas imagens geradas". 
    * O Orquestrador designa o `Meta-Developer Agent`.
    * O agente escreve uma nova função em Python na pasta `/tools/image_filters.py`.
    * O agente altera o script do Artista para importar e usar essa nova ferramenta.
    * O sistema executa um *hot reload* e avisa no chat: "Nova ferramenta criada e adicionada ao Artista com sucesso!"

## Estrutura de Diretórios Proposta
```text
/
├── .env                    # Chaves do repositório Git
├── main.py                 # Loop principal do CLI de Chat e Orquestrador
├── requirements.txt        
├── /agents                 # Scripts dos agentes (que podem ser editados pela própria IA)
├── /tools                  # Ferramentas Python (que podem ser expandidas pela própria IA)
└── /workspace              # Repositório clonado
    ├── project.godot       
    ├── todo_list.md        
    ├── /scenes             
    ├── /scripts            
    └── /assets             
```
