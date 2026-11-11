# HorizonX — REA e análise de fluxo 03

Data: 2026-11-11. Fonte: repositório público `Mdx2025/horizonx-drops`; inspeção estática por REA e leitura direta do GitHub. **Não** representa análise da plataforma HorizonX completa.

## Evidências REA
- `ev_30027ef2053d619f2ef0e53e2e26167d55914ad41717a1d28da7808b5072f877` — primeira enumeração de funções e recursos.
- `ev_b3ae47b2c1113efd0abba0e9f5b6b001769b95daea1cfa440cb22f8d16c16dfd` — inventário compacto de exports, eventos e chamadas de descarte. Execução concluída com exit 0.

## Inventário
| Motor | Export principal | Contexto identificado | Eventos explícitos |
|---|---|---|---|
| Plasma Orb | `buildPlasmaOrb` | `webgl2` | pointerenter, pointerleave, pointerdown, visibilitychange, webglcontextlost/restored |
| Ember Sun | `buildEmberSun` | `webgl` | pointermove, pointerdown/up/cancel/leave, wheel, visibilitychange, context lost/restored |
| Ember Eclipse | `buildEmberEclipse` | `webgl` | pointermove, pointerdown/cancel/leave, wheel, visibilitychange, context lost/restored |
| Opal Loop | `buildOpalLoop` | Three.js | visibilitychange, change, context lost/restored |
| Elastic Mesh | `createElasticField`, `createElasticMesh` | Three.js | helper `listen` registra listeners dinamicamente; busca textual por evento literal é incompleta |

## Achados aprofundados
### Plasma Orb
- `createRenderer` cria contexto WebGL2 com `antialias:false` e preferência de alta performance.
- Compila programas separados para `noise`, `scene`, `down`, `up` e `comp`, formando um pipeline multi-pass de cena e bloom.
- Usa `EXT_color_buffer_float` quando disponível, com fallback para RGBA8.
- Ajusta resolução interna: se frame >25ms por mais de 0,6s, reduz escala; se frame <18,18ms por mais de 3s, aumenta escala. Limita `adapt` entre 0,5 e 1. Isso foi observado no código, não medido em GPU.
- Loop `frame` usa `dt` limitado a 50ms, suavização exponencial para energia/swirl e interrompe RAF quando estado estabiliza e não está rodando.
- `buildPlasmaOrb` monta canvas, cria renderer, oferece fallback quando WebGL2 indisponível e expõe atualização via handle.
### Elastic Mesh
- `createElasticField` usa arrays Float32 de deslocamento, velocidade, velocidade seguinte e posições.
- `step` limita dt a 1/120s; usa força elástica, amortecimento exponencial, Laplaciano dos quatro vizinhos e força gaussiana próxima ao ponteiro. Limita deslocamento a [-0,6,0,6].
- `createElasticMesh` gerencia eventos via helper `listen` com callbacks de remoção, pausa quando aba está oculta ou elemento fora do viewport, reduz movimento e usa `ResizeObserver`.
- Three.js é importado dinamicamente, com WebGLRenderer e ShaderMaterial.

## Limitações e próximos testes
- `dispose` e `cancelAnimationFrame` foram identificados, mas ainda é necessário testar ausência de vazamentos sob mount/unmount repetidos.
- A inspeção não mede fidelidade visual, FPS, draw calls nem comportamento do editor.
- Não reproduzir engines com licença restritiva em uma biblioteca redistribuída.
- Próxima etapa: teste automatizado da simulação MIT `createElasticField` em ambiente isolado, validação de invariantes e benchmark; depois captura dinâmica autorizada.
