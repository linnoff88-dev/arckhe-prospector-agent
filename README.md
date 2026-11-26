ï»¿# Prospector Agent

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


Esteira **semi-autêÂ´noma** de prospecêÂ§êÂ£o e venda de sites: descobre negêÂ³cios bem avaliados com site ruim, redesenha a pêÂ¡gina, publica e envia a proposta ê¢â¬â operada por um **bot de Telegram**, com um **orquestrador** determinêÂ­stico e **IA (`claude -p`) sêÂ³ onde hêÂ¡ julgamento/criaêÂ§êÂ£o**.

> **Status:** **v1 (nêÂºcleo determinêÂ­stico) construêÂ­do e testado ê¢â¬â 47 testes verdes.**
> As integraêÂ§êÂµes externas (Google Maps, `claude -p`, Resend, Telegram, deploy GitHub Pages) estêÂ£o implementadas atrêÂ¡s de interfaces, mas **rodar ao vivo depende das suas credenciais** (ver "O que falta").
> Design: [`docs/superpowers/specs/2026-07-14-prospector-agent-design.md`](docs/superpowers/specs/2026-07-14-prospector-agent-design.md) êÂ· Planos: [`docs/superpowers/plans/`](docs/superpowers/plans/)

## A esteira

```
descoberto ê¢â â qualificado ê¢â â redesenhado ê¢â â publicado ê¢â â [PORTêÆO] ê¢â â enviado ê¢â â respondido ê¢â â fechado
```

| Fase | O que faz | Como | Milestone |
|---|---|---|---|
| **Descoberta** | acha candidatos no Maps (nota ê¢â°Â¥ 4.7, com site) | provider plugêÂ¡vel (`fake` / `navegador` Playwright) | M2 |
| **QualificaêÂ§êÂ£o** | julga "site ruim?" + extrai e-mail/WhatsApp | `claude -p` injetêÂ¡vel | M2 |
| **CriaêÂ§êÂ£o** | redesenha a pêÂ¡gina premium + capa antes/depois | `claude -p` injetêÂ¡vel | M3 |
| **PublicaêÂ§êÂ£o** | sobe o site + calcula a URL | `git push` ê¢â â GitHub Pages | M3 |
| **PortêÂ£o** | pede aprovaêÂ§êÂ£o antes de enviar (ou pula com `--auto`) | Telegram | M4 |
| **Envio** | manda a proposta (opt-out, supressêÂ£o, 1-por-lead) | Resend injetêÂ¡vel | M4 |
| **Orquestrador** | conduz o lead pela mêÂ¡quina de estados, aplica o portêÂ£o | nêÂºcleo determinêÂ­stico | M4 |

## Como rodar (v1)

Requer Python 3.11+ e `pytest`.

```bash
# 1. suêÂ­te completa (nêÂºcleo determinêÂ­stico, sem rede)
python3 -m pytest -q                       # 47 passed

# 2. esteira fake ponta a ponta (dry-run, nêÂ£o toca disco/rede)
python3 -m prospector demo --dry-run --auto        # ê¢â â ... ê¢â â enviado
python3 -m prospector demo --dry-run               # ê¢â â ... ê¢â â aguardando_aprovacao

# 3. prospecêÂ§êÂ£o com provider fake (sem credenciais)
python3 -m prospector prospectar nutricionistas Bauru --provider fake --db /tmp/pa.db
```

Comandos que dependem de credenciais reais (ver abaixo): `prospectar --provider navegador`, `redesenhar <slug>`, `publicar <slug>`, `aprovar <slug>`, `atender`.

## O que falta (ligar as credenciais)

O nêÂºcleo estêÂ¡ testado; as **pontas externas** precisam do seu ambiente para rodar ao vivo:

- **`ANTHROPIC_API_KEY`** ê¢â¬â `claude -p` (qualificar + redesenhar). Requer o Claude Code CLI instalado.
- **Google Maps** ê¢â¬â `--provider navegador` usa Playwright (`pip install playwright && playwright install chromium`); captcha exige vocêÂª por perto (v1 local).
- **`RESEND_API_KEY`** + domêÂ­nio de envio dedicado (SPF/DKIM) ê¢â¬â envio da proposta.
- **`TELEGRAM_BOT_TOKEN`** + **`TELEGRAM_CHAT_ID`** ê¢â¬â bot do portêÂ£o/notificaêÂ§êÂµes.
- **Repo Pages** (monorepo `prospector-sites`) + **`GITHUB_TOKEN`** ê¢â¬â publicaêÂ§êÂ£o.

Copie `.env.example` ê¢â â `.env` e preencha. Nada disso vai para o git (`.gitignore`).

## PrincêÂ­pios

- **Controle determinêÂ­stico em cêÂ³digo; IA sêÂ³ onde precisa.** Fila, mêÂ¡quina de estados e portêÂ£o sêÂ£o Python testêÂ¡vel; o modelo entra sêÂ³ em *qualificar* e *redesenhar*.
- **Limites externos injetêÂ¡veis** ê¢â¬â browser, `claude -p`, Resend, Telegram e `git push` entram por interface, entêÂ£o o nêÂºcleo êÂ© 100% testêÂ¡vel sem rede/credenciais.
- **Providers plugêÂ¡veis** ê¢â¬â descoberta troca de browser para API sem reescrever.
- **PortêÂ¡vel** ê¢â¬â roda local (A) ê¢â â hêÂ­brido VPS (B) ê¢â â tudo VPS (C) sêÂ³ trocando config.
- **Autonomia configurêÂ¡vel** ê¢â¬â padrêÂ£o com portêÂ£o de aprovaêÂ§êÂ£o; `--auto` deixa rodar sozinho.

## Roadmap

| VersêÂ£o | Onde roda | Descoberta | Status |
|---|---|---|---|
| **v1** | local | browser (vocêÂª resolve captcha) | nêÂºcleo ê¢Åâ¦ êÂ· integraêÂ§êÂµes dependem de credenciais |
| **v1.x** | hêÂ­brido VPS | browser local ê¢â â VPS | planejado |
| **v2** | tudo VPS | Places API / terceiro | planejado |

## ReferêÂªncia

Baseado no plugin Claude Code `prospector-de-sites` (em `../vendasite`), usado sêÂ³ como referêÂªncia.

---

_Guia landing: https://linnoff88-dev.github.io/arckhe-/guia/_

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>