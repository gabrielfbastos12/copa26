# Copa do Mundo 2026 — Site de Placar

## O que é
Site mobile-first para acompanhar todos os jogos da Copa do Mundo 2026.
Hospedado em `gabrielfbastos12.github.io/copa26` (repositório GitHub `gabrielfbastos12/copa26`, arquivo `index.html`).

## Stack atual
HTML puro + CSS + JS vanilla. Zero dependências, zero build. Fonte: Barlow Condensed (Google Fonts).

## Arquivos do projeto
- `index.html` — o site completo (HTML + CSS + JS tudo num arquivo)
- `api/scores.js` — função serverless Vercel (proxy para a ESPN)
- `vercel.json` — configuração do projeto Vercel
- `CLAUDE.md` — este arquivo de contexto

## Estrutura do site
3 abas no menu inferior fixo:
- **Jogos** — lista cronológica completa com card de destaque sempre no topo (próximo jogo ou ao vivo). Filtros por data, Hoje e 🇧🇷 Brasil.
- **Mata-mata** — todos os confrontos por fase (rodada de 32, quartas, semis, 3º lugar, final). Times ainda como "Venc. Grupo X".
- **Grupos** — tabela de classificação zerada + jogos de cada grupo (A a L).

## Dados
72 jogos da fase de grupos hardcoded no JS. Times, datas e horários em UTC-3 (Belém/Brasília). Jogos do Brasil com borda verde destacada.

## Design
- Dark theme
- Fundo: `#0d1117`
- Dourado: `#f0c94a`
- Verde Brasil: `#009739`
- Vermelho ao vivo: `#f85149`
- Bandeiras via emoji
- Status "Ao vivo" pisca com CSS animation

## Grupos completos (48 seleções)
- **A:** México, Coreia do Sul, África do Sul, República Tcheca
- **B:** Canadá, Suíça, Catar, Bósnia e Herzegovina
- **C:** Brasil, Marrocos, Escócia, Haiti
- **D:** Estados Unidos, Austrália, Paraguai, Turquia
- **E:** Alemanha, Costa do Marfim, Equador, Curaçao
- **F:** Holanda, Japão, Tunísia, Suécia
- **G:** Bélgica, Egito, Irã, Nova Zelândia
- **H:** Espanha, Cabo Verde, Arábia Saudita, Uruguai
- **I:** França, Senegal, Noruega, Iraque
- **J:** Argentina, Argélia, Áustria, Jordânia
- **K:** Portugal, Uzbequistão, Colômbia, RD Congo
- **L:** Inglaterra, Croácia, Gana, Panamá

## Placares — arquitetura atual (funcionando)

### Backend (Vercel)
- URL: `https://copa26-sigma.vercel.app/api/scores`
- Arquivo: `api/scores.js` — serverless function Node.js
- Busca a ESPN server-side e devolve o JSON com `Access-Control-Allow-Origin: *`
- Cache de 60s na CDN Vercel (`s-maxage=60, stale-while-revalidate=30`)

### Endpoint da ESPN
```
GET https://site.api.espn.com/apis/site/v2/sports/soccer/fifa.world/scoreboard?limit=200&dates=20260611-20260719
```
Retorna ~104 eventos (72 grupos + 32 mata-mata). Nomes dos times em inglês.

### No index.html
- `const BACKEND = 'https://copa26-sigma.vercel.app/api/scores'` no topo do JS
- `fetchESPN()` tenta o backend primeiro, depois proxies públicos como fallback
- `const PT_EN = {...}` — dicionário PT→EN para casar nomes com a ESPN (ex: "Brasil"→"Brazil")
- `getScore(j)` usa `PT_EN` antes de fazer o match fuzzy por substring + data

### Fluxo de atualização
- O site atualiza os placares a cada 2 minutos automaticamente
- Jogo ao vivo: mostra placar + minuto piscando em vermelho
- Jogo encerrado: mostra placar final
- Jogo futuro: mostra horário (UTC-3 Belém)

## Próximos passos possíveis
- Atualizar tabela de classificação dos grupos com pontos/gols reais conforme os jogos forem terminando
- Preencher o mata-mata com os times classificados à medida que a fase de grupos terminar
- Adicionar notificações de gol ou push notifications
