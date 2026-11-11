# HorizonX — Engenharia reversa estática 01
Data: 2026-11-10/11. Escopo: repositório público [Mdx2025/horizonx-drops](https://github.com/Mdx2025/horizonx-drops). Não é análise da aplicação privada completa.

## Fontes examinadas
- `package.json`, `registry.json`, `LICENSE`
- `scripts/sync-from-horizonx.mjs`
- `registry/drops/plasma-orb/plasma-orb.tsx`
- `registry/drops/plasma-orb/plasma-orb.engine.js`
- `registry/drops/ember-sun/ember-sun.engine.js`
- `registry/drops/opal-loop/opal-loop.engine.js`
- `registry/drops/elastic-mesh/elastic-mesh.engine.js`

## Descobertas confirmadas
1. A sincronização oficial mapeia cinco engines de `horizonx-frontend/src/lib/tools` para os arquivos públicos `registry/drops/*/*.engine.js`. O script copia o corpo do engine sem alterações e registra hash de origem. Não temos o repositório privado `horizonx-frontend`.
2. O pacote de distribuição é React 19 + Vite 8 + TypeScript + Three.js 0.186.1, e o registry usa o esquema shadcn. **Isso caracteriza o pacote Drops, não prova a stack da plataforma HorizonX completa.**
3. Plasma Orb usa WebGL2, shader GLSL ES 3.0, textura de ruído 3D gerada na GPU, ray marching de plasma, vidro refrativo com IOR 1.45, reflexos internos, bloom e tone mapping. Qualidade high: 36 steps / maxSide 1100; balanced: 22 / 640.
4. Wrapper Plasma Orb: React useEffect/useRef, estados `idle/listening/thinking/speaking/error`, configuração atualizada por `engine.current.update`, desmontagem chamando função de cleanup.
5. Ember Sun usa fragment shader procedural, chamas, crosta com Voronoi 3D e interação por ponteiro.
6. Opal Loop carrega Three.js dinamicamente; geometria procedural e materiais físicos com transmissão, iridescência e dispersão.
7. Elastic Mesh é campo de altura amortecido com Three.js e licença MIT.
8. Nos motores Ember Sun, Opal Loop e Elastic Mesh foram identificados `requestAnimationFrame`, `ResizeObserver`, `IntersectionObserver`, `visibilitychange` e `dispose`. Falta análise de fluxo para confirmar os casos exatos de pausa e cleanup.
9. Licença Drops 1.0 restringe redistribuição como biblioteca/kit; Elastic Mesh é MIT. Reimplementação independente é a via indicada para nosso catálogo.

## Modelo arquitetural parcial
```
React wrapper -> configuração/estado -> engine.js -> canvas/WebGL ou Three.js -> GPU
       |                 |                 |
       +-- props         +-- update        +-- resize, input, RAF, cleanup
```
Este modelo descreve os Drops analisados, **não** os editores completos do HorizonX.

## Limitações
- Sem CDP/navegador conectado ao REA, não foi possível inspecionar DOM, rotas, requests, editor, presets ou exports da plataforma em execução.
- Nenhuma captura de GPU, teste visual comparativo ou medição de FPS foi realizada.
- Nenhuma implementação, alteração no site ou deploy foi feito nesta etapa.

## Próximos experimentos
1. Traçar `buildPlasmaOrb` e funções de update/cleanup até chamadas WebGL, registrando recursos criados e liberados.
2. Extrair inventário de shaders, uniforms, buffers, passes, texturas e eventos por engine.
3. Validar estática local com AST e testes isolados, sem redistribuir código sob licença restrita.
4. Quando houver CDP: REA para DOM/eventos, fluxo do editor, mudanças de presets, exportação, rede e desempenho; Spector.js para frame WebGL.
5. Implementar prova de conceito independente somente depois de requisitos e licença aprovados.
