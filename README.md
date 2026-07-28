# Kimi Code

> **Uma skill. Um terminal. Um terço do custo.**

Skill do Claude Code que abre uma **nova janela de terminal** rodando o **harness completo do Claude Code** com o modelo **Kimi K3 (Moonshot AI)** via **OpenRouter** — sem tocar na sua sessão atual nem nas configurações globais.

Diga **"abre o kimi"** e pronto.

## 📖 Guia de uso

Guia completo (landing + passo a passo): **https://inematds.github.io/kimicodecc/guia/**

---

## O que ela faz

- Abre uma nova janela de terminal com o Claude Code, com todas as chamadas de API roteadas para `moonshotai/kimi-k3` no OpenRouter em vez da Anthropic
- **Harness completo** — tools, skills, edição de arquivos e bash funcionam exatamente igual
- **Escopada à sessão** — nunca escreve em `~/.claude/settings.json`. Fechou a janela, voltou tudo ao normal
- **Segura pra gravação de tela** — sua API key nunca aparece no scrollback do terminal
- Cerca de **1/3 do custo** do modelo de fronteira em builds reais (o Kimi é 2-3x mais lento em trabalho pesado — perfeito pra build em segundo plano)

---

## Instalação (2 minutos)

```bash
mkdir -p ~/.claude/skills
cp -r skills/kimi-code ~/.claude/skills/
```

Reinicie o Claude Code e diga **"abre o kimi"**.

Na primeira execução ela pede sua chave da OpenRouter (crie em <https://openrouter.ai/settings/keys> e coloque alguns dólares de crédito) e salva em `~/.config/openrouter.env` com `chmod 600`, pra você nunca mais precisar colar.

---

## Como usar

| Você diz | O que acontece |
|---|---|
| "abre o kimi" / "open kimmy" | Nova janela de terminal com o Claude Code em Kimi K3 |
| `/kimi-code here` | Só imprime o one-liner pra você rodar no shell atual |
| `/kimi-code model qwen/qwen3-coder` | Mesmo truque, com qualquer modelo do OpenRouter |
| `/kimi-code status` | Mostra qual chave + modelo seriam usados, sem lançar nada |

Você sabe que funcionou quando o banner de boas-vindas do Claude Code mostrar **`moonshotai/kimi-k3` · API Usage Billing**.

---

## Como funciona

Quatro variáveis de ambiente na frente do `claude`:

| Variável | Valor | Por quê |
|---|---|---|
| `ANTHROPIC_BASE_URL` | `https://openrouter.ai/api` | Redireciona as chamadas pro OpenRouter |
| `ANTHROPIC_AUTH_TOKEN` | a chave do OpenRouter | Autentica no OpenRouter |
| `ANTHROPIC_API_KEY` | `""` (vazio) | Impede que a chave real da Anthropic seja enviada |
| `ANTHROPIC_MODEL` | `moonshotai/kimi-k3` | Qual modelo serve a sessão |

Mais `ANTHROPIC_SMALL_FAST_MODEL` com o mesmo slug, pra que as chamadas de fundo (classe haiku) não voltem silenciosamente pra Anthropic.

O one-liner equivalente, se preferir rodar na mão:

```bash
source ~/.config/openrouter.env && \
  ANTHROPIC_BASE_URL='https://openrouter.ai/api' \
  ANTHROPIC_AUTH_TOKEN="$OPENROUTER_API_KEY" \
  ANTHROPIC_API_KEY='' \
  ANTHROPIC_MODEL='moonshotai/kimi-k3' \
  ANTHROPIC_SMALL_FAST_MODEL='moonshotai/kimi-k3' \
  claude
```

A chave só é resolvida **depois** do `source`, já dentro da nova janela — por isso o valor literal nunca entra na linha de comando.

---

## Plataformas

- **Linux** — a skill detecta o emulador disponível (`gnome-terminal`, `konsole`, `xfce4-terminal`, `kitty`, `alacritty`, `x-terminal-emulator`, `xterm`) e lança com a sintaxe certa. Sem emulador (headless/WSL sem X/container), ela cai no modo `here`.
- **macOS** — lança via AppleScript no Terminal.app.

---

## Quando usar Kimi vs Claude

- **Claude (assinatura)** — sessões interativas, trabalho de alta alavancagem, tudo onde velocidade importa.
- **Kimi K3 (esta skill)** — builds grandes de design, trabalho em lote, qualquer coisa que dá pra deixar rodando em segundo plano. Benchmark real: um build completo de slide deck custou **$10,70 no Kimi vs $35 no modelo de fronteira**, com qualidade equivalente.

---

## Segurança

- A skill **nunca** modifica `~/.claude/settings.json`.
- A chave completa **nunca** é impressa no chat — só os 4 últimos caracteres.
- `~/.config/openrouter.env` é criado com `chmod 600`.

---

## Estrutura

```
kimicodecc/
├── README.md
├── doc/
│   └── kimi-code-openrouter.md   # material de origem
└── skills/
    └── kimi-code/
        └── SKILL.md              # a skill
```
