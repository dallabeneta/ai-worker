# Local Multi-Agent Game Dev Pipeline

## Descrição do Projeto
Este projeto é uma arquitetura de orquestração multi-agente totalmente local para o desenvolvimento autônomo de jogos. A partir de um simples arquivo de texto (To-Do List), o sistema divide as tarefas entre agentes especializados baseados em Inteligência Artificial que trabalham em conjunto para escrever o código-fonte (lógica, mecânicas) e gerar os gráficos (sprites, cenários), salvando os artefatos diretamente no disco.

O sistema foi desenhado para rodar 100% offline, utilizando apenas a CPU/GPU local, sem dependência de APIs externas pagas no momento, mas com uma arquitetura modular preparada para conectar ferramentas web no futuro.

## Stack Tecnológica Local
*   **Orquestração de Agentes:** [CrewAI](https://github.com/joaomdmoura/crewAI) (ou framework similar baseado em Python).
*   **Processamento de Linguagem (LLM):** [Ollama](https://ollama.com/) (modelos locais: Llama 3 para orquestração e DeepSeek Coder/Qwen para lógica de programação).
*   **Geração de Gráficos/Imagens:** [ComfyUI](https://github.com/comfyanonymous/ComfyUI) via API local (Stable Diffusion XL ou modelos 2D/Pixel Art).
*   **Linguagem Principal:** Python 3.10+

## Papéis dos Agentes (Agents)

1.  **Manager Agent (Gerente de Projeto):**
    *   **Função:** Lê o arquivo `todo.txt` fornecido pelo usuário.
    *   **Ação:** Analisa os requisitos, quebra a lista em tarefas granulares (código e arte) e delega para os agentes apropriados na ordem correta.
2.  **Programmer Agent (Programador):**
    *   **Função:** Responsável pela lógica do jogo.
    *   **Ação:** Escreve scripts (ex: Pygame ou Godot script), garante que os caminhos dos assets gerados estejam corretos no código e salva os arquivos no diretório `/src`.
3.  **Art Director Agent (Artista):**
    *   **Função:** Responsável pela identidade visual.
    *   **Ação:** Traduz a requisição visual em um prompt detalhado em JSON, envia a requisição para a API local do ComfyUI e salva a imagem gerada no diretório `/assets`.

## Ferramentas Personalizadas (Tools)
Os agentes interagem com o ambiente através de ferramentas customizadas em Python:
*   `read_todo_list()`: Lê as requisições iniciais.
*   `generate_local_image(prompt)`: Faz um POST para a API do ComfyUI (`http://127.0.0.1:8188/prompt`) e faz o download do resultado.
*   `save_code_to_disk(filename, code)`: Escreve o código gerado no disco de forma segura.

## Fluxo de Trabalho (Workflow)
1. O usuário edita o arquivo `todo.txt` (ex: "Criar uma janela de 800x600, um fundo azul e um sprite de nave espacial").
2. O script principal `main.py` inicia a Crew (equipe de agentes).
3. O Manager lê o `todo.txt` e cria o plano de execução.
4. O Artista solicita à API do ComfyUI a imagem da nave e a salva como `nave.png` em `/assets`.
5. O Programador escreve o código do jogo em Python carregando o asset `assets/nave.png` e salva como `game.py` em `/src`.
6. O sistema finaliza e o usuário pode executar o jogo criado.

## Estrutura de Diretórios Proposta
```text
/
├── main.py                 # Ponto de entrada que inicializa a orquestração
├── todo.txt                # Arquivo de entrada do usuário com o que deve ser feito
├── requirements.txt        # Dependências do Python (crewai, requests, etc)
├── /agents                 # Definições de persona e comportamento de cada agente
├── /tools                  # Funções em Python que os agentes usam (Salvar arquivo, Chamar ComfyUI)
├── /src                    # Código-fonte do jogo gerado pelos agentes
└── /assets                 # Imagens, sprites e gráficos gerados pelo agente de arte
