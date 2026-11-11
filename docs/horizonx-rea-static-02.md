# HorizonX — REA remoto: análise estática reproduzível 02

Data: 2026-11-11. Sem uso ou modificação do computador do usuário.

## Método e evidências
Foi usado o REA remoto conectado ao ChatGPT, ferramenta `capture_process_scenario`, executando Node.js para obter arquivos públicos de `raw.githubusercontent.com/Mdx2025/horizonx-drops/main/registry/drops/` via HTTPS e extrair características estáticas por padrões. O acesso retornou HTTP 200 para os cinco arquivos.

- Execução inicial Plasma Orb: `ev_a6f6033feb710ebeb5be5224bb9314379edc87fe7e0df94eedc712ba85ceb6ce` — 27.269 caracteres, WebGL2, requestAnimationFrame, sampler3D e bloom encontrados.
- Inventário de cinco engines: `ev_40af79924a5946b19e759e780af5d81d9576b46b4f4b249bf112eac599ca59d9` — execução concluída, exit code 0.

## Inventário observado
| Engine | Caracteres | Import dinâmico Three.js | RAF | ResizeObserver | IntersectionObserver | visibilitychange |
|---|---:|---|---:|---|---|---|
| plasma-orb | 27269 | não | 2 | sim | sim | sim |
| ember-sun | 39239 | não | 2 | sim | sim | sim |
| ember-eclipse | 23938 | não | 2 | sim | sim | sim |
| opal-loop | 11884 | sim | 2 | sim | sim | sim |
| elastic-mesh | 17368 | sim | 2 | sim | sim | sim |

Observação: o campo RAF é a quantidade de ocorrências textuais de `requestAnimationFrame`, **não** número de loops ativos. A ausência de `getContext('webgl2')` em um engine não prova ausência de WebGL2, pois a grafia ou abstração pode diferir.

### Parâmetros de shader detectados (amostra)
- Plasma Orb: `uNoise`, `uRes`, `uTime`, `uDetail`, `uScene`, `uBloomK`.
- Ember Sun: `uMouse`, `uMouseVel`, `uScrollVel`, `uFlames`, `uEmbers`, `uRipples`.
- Ember Eclipse: `uMouse`, `uMouseVel`, `uScrollVel`, `uDisc`, `uFlames`, `uRipples`.

## Limitações
Esta é uma **inspeção estática automatizada dentro do REA**, não um resultado do analisador semântico de bundles nem uma captura CDP. Ainda não foi estabelecida conexão com navegador. Não foram capturados DOM, eventos em tempo real, editores, rede do HorizonX, shaders compilados na GPU ou FPS. As ocorrências são indicadores a aprofundar com AST, rastreamento de fluxo e experimentos.

## Próximo gate
1. Mapear init/update/dispose de cada engine.
2. Construir grafo de recursos GPU e fluxo de parâmetros.
3. Criar testes independentes em staging sem redistribuir código com licença restrita.
4. Capturar comportamento dinâmico com navegador autorizado quando disponível.
