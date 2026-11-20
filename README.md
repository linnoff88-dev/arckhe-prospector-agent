# Prospector Agent

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


Esteira **semi-autÃ´noma** de prospecÃ§Ã£o e venda de sites: descobre negÃ³cios bem avaliados com site ruim, redesenha a pÃ¡gina, publica e envia a proposta â€” operada por um **bot de Telegram**, com um **orquestrador** determinÃ­stico e **IA (`claude -p`) sÃ³ onde hÃ¡ julgamento/criaÃ§Ã£o**.

> **Status:** **v1 (nÃºcleo determinÃ­stico) construÃ­do e testado â€” 47 testes verdes.**
> As integraÃ§Ãµes externas (Google Maps, `claude -p`, Resend, Telegram, deploy GitHub Pages) estÃ£o implementadas atrÃ¡s de interfaces, mas **rodar ao vivo depende das suas credenciais** (ver "O que falta").
> Design: [`docs/superpowers/specs/2026-07-14-prospector-agent-design.md`](docs/superpowers/specs/2026-07-14-prospector-agent-design.md) Â· Planos: [`docs/superpowers/plans/`](docs/superpowers/plans/)

## A esteira

```
descoberto â†’ qualificado â†’ redesenhado â†’ publicado â†’ [PORTÃƒO] â†’ enviado â†’ respondido â†’ fechado
```

| Fase | O que faz | Como | Milestone |
|---|---|---|---|
| **Descoberta** | acha candidatos no Maps (nota â‰¥ 4.7, com site) | provider plugÃ¡vel (`fake` / `navegador` Playwright) | M2 |
| **QualificaÃ§Ã£o** | julga "site ruim?" + extrai e-mail/WhatsApp | `claude -p` injetÃ¡vel | M2 |
| **CriaÃ§Ã£o** | redesenha a pÃ¡gina premium + capa antes/depois | `claude -p` injetÃ¡vel | M3 |
| **PublicaÃ§Ã£o** | sobe o site + calcula a URL | `git push` â†’ GitHub Pages | M3 |
| **PortÃ£o** | pede aprovaÃ§Ã£o antes de enviar (ou pula com `--auto`) | Telegram | M4 |
| **Envio** | manda a proposta (opt-out, supressÃ£o, 1-por-lead) | Resend injetÃ¡vel | M4 |
| **Orquestrador** | conduz o lead pela mÃ¡quina de estados, aplica o portÃ£o | nÃºcleo determinÃ­stico | M4 |

## Como rodar (v1)

Requer Python 3.11+ e `pytest`.

```bash
# 1. suÃ­te completa (nÃºcleo determinÃ­stico, sem rede)
python3 -m pytest -q                       # 47 passed

# 2. esteira fake ponta a ponta (dry-run, nÃ£o toca disco/rede)
python3 -m prospector demo --dry-run --auto        # â†’ ... â†’ enviado
python3 -m prospector demo --dry-run               # â†’ ... â†’ aguardando_aprovacao

# 3. prospecÃ§Ã£o com provider fake (sem credenciais)
python3 -m prospector prospectar nutricionistas Bauru --provider fake --db /tmp/pa.db
```

Comandos que dependem de credenciais reais (ver abaixo): `prospectar --provider navegador`, `redesenhar <slug>`, `publicar <slug>`, `aprovar <slug>`, `atender`.

## O que falta (ligar as credenciais)

O nÃºcleo estÃ¡ testado; as **pontas externas** precisam do seu ambiente para rodar ao vivo:

- **`ANTHROPIC_API_KEY`** â€” `claude -p` (qualificar + redesenhar). Requer o Claude Code CLI instalado.
- **Google Maps** â€” `--provider navegador` usa Playwright (`pip install playwright && playwright install chromium`); captcha exige vocÃª por perto (v1 local).
- **`RESEND_API_KEY`** + domÃ­nio de envio dedicado (SPF/DKIM) â€” envio da proposta.
- **`TELEGRAM_BOT_TOKEN`** + **`TELEGRAM_CHAT_ID`** â€” bot do portÃ£o/notificaÃ§Ãµes.
- **Repo Pages** (monorepo `prospector-sites`) + **`GITHUB_TOKEN`** â€” publicaÃ§Ã£o.

Copie `.env.example` â†’ `.env` e preencha. Nada disso vai para o git (`.gitignore`).

## PrincÃ­pios

- **Controle determinÃ­stico em cÃ³digo; IA sÃ³ onde precisa.** Fila, mÃ¡quina de estados e portÃ£o sÃ£o Python testÃ¡vel; o modelo entra sÃ³ em *qualificar* e *redesenhar*.
- **Limites externos injetÃ¡veis** â€” browser, `claude -p`, Resend, Telegram e `git push` entram por interface, entÃ£o o nÃºcleo Ã© 100% testÃ¡vel sem rede/credenciais.
- **Providers plugÃ¡veis** â€” descoberta troca de browser para API sem reescrever.
- **PortÃ¡vel** â€” roda local (A) â†’ hÃ­brido VPS (B) â†’ tudo VPS (C) sÃ³ trocando config.
- **Autonomia configurÃ¡vel** â€” padrÃ£o com portÃ£o de aprovaÃ§Ã£o; `--auto` deixa rodar sozinho.

## Roadmap

| VersÃ£o | Onde roda | Descoberta | Status |
|---|---|---|---|
| **v1** | local | browser (vocÃª resolve captcha) | nÃºcleo âœ… Â· integraÃ§Ãµes dependem de credenciais |
| **v1.x** | hÃ­brido VPS | browser local â†’ VPS | planejado |
| **v2** | tudo VPS | Places API / terceiro | planejado |

## ReferÃªncia

Baseado no plugin Claude Code `prospector-de-sites` (em `../vendasite`), usado sÃ³ como referÃªncia.

---

_Guia landing: https://inematds.github.io/prospector-agent/guia/_

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>