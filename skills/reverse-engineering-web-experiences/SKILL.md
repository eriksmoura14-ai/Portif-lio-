---
name: reverse-engineering-web-experiences
description: Investigação técnica e reconstrução independente de aplicações web imersivas, WebGL, Three.js, shaders, interações e editores, com REA, GitHub, DevTools e evidências auditáveis.
version: 1.0.0
---

# Skill: Engenharia Reversa de Experiências Web

## Missão
Investigar como uma ferramenta web funciona **antes** de implementá-la. O objetivo é produzir um modelo técnico comprovado, reconstrução independente e testes comparativos. Não confundir semelhança visual com equivalência funcional. Não criar componentes, páginas ou deploys antes dos gates de evidência.

## Ativação
Use quando o usuário pedir análise, reprodução, auditoria técnica ou reconstrução de interfaces, experiências WebGL/Three.js, shaders, editores, animações, ferramentas SaaS e bibliotecas visuais. Se a intenção for apenas criar uma interface original, esta skill não é obrigatória.

## Princípios
- Observação ≠ inferência ≠ hipótese. Marcar cada afirmação como **observado**, **inferido** ou **não verificado**.
- Código publicado ≠ código livre para redistribuir. Verificar licença, atribuição, ativos e condições de uso; nunca contornar login, DRM, assinatura ou controles de acesso.
- Não acessar contas, tokens, endpoints privados ou dados pessoais. Inspecionar somente conteúdo público ou sistemas sob autorização.
- Preferir análise passiva, repositórios oficiais e documentação. Não executar binários não confiáveis fora de sandbox.
- Usar REA somente em alvos efetivamente acessíveis ao servidor REA; não supor que ele tenha acesso ao navegador do usuário, a arquivos locais ou ao GitHub.
- Nunca afirmar que um bundle foi analisado sem identificador de evidência e resultados reais.
- Não inflar a biblioteca com efeitos falsos. Cada item deve ter prova, demonstração funcional e proveniência.

## Workflow com gates obrigatórios

### Gate 0 — Escopo e autorização
Registrar URL, versão/data, funcionalidade-alvo, objetivo de equivalência, permissões, restrições e ambiente. Distinguir: engenharia reversa comportamental, análise de código distribuído, interoperabilidade, recriação visual e reprodução de produto.

### Gate 1 — OSINT e inventário
1. Identificar domínio oficial, documentação, páginas de ferramentas, changelog, exemplos e termos.
2. Buscar GitHub por nome exato, organização, slugs, dependências e exemplos. Inspecionar README, LICENSE, package.json, lockfile, registry, scripts, releases e commits.
3. Criar tabela por módulo: URL, propósito, entradas, saídas, motor, dependências, origem, licença, status.
4. Identificar código genuinamente público versus referências visuais versus projetos não relacionados.
**Saída:** `inventory.md` e `sources.md`.
**Gate:** nenhum item sem URL/proveniência verificável.

### Gate 2 — Análise comportamental
Em navegador controlado e permitido, capturar baseline em desktop e mobile: screenshots, vídeo curto, DOM, eventos pointer/scroll/keyboard, estados, controles, responsividade, preferências de movimento e falhas.
Construir matriz **estímulo → estado → saída**: pointermove, pointerdown, click, scroll, resize, visibilitychange, mudança de preset, reset, exportação.
Usar DevTools Network/Performance/Sources; registrar HAR sanitizado, URLs de scripts públicos e chamadas observáveis sem segredos. Não repetir chamadas autenticadas fora do fluxo autorizado.
**Saída:** `behavior-matrix.md`, imagens, ambiente e limites.
**Gate:** cada comportamento prioritário reproduzível com passos e evidências.

### Gate 3 — Análise estática e REA
Se houver artefatos legitimamente obtidos:
1. Identificar formatos: JS bundle, source maps públicos, wasm, assets, glTF, GLSL, WGSL.
2. Calcular SHA-256 e preservar cópia original; nunca modificar evidência bruta.
3. Analisar imports, chunks, rotas, estado, bibliotecas, APIs, workers, shaders e eventos.
4. Com REA: `list_browser_targets` → `analyze_web_bundle` para alvo CDP **já disponível**; ou `analyze_javascript_application` para diretório/ASAR presente **no servidor REA**. Guardar evidence_id, limitações e cobertura.
5. Se CDP inacessível, registrar bloqueio e usar GitHub, DevTools e análise estática local permitida; não inventar capturas.
6. Construir grafo: UI → state → engine → renderer → GPU → frame; e UI → export → artefato.
**Saída:** `architecture.md`, `evidence-ledger.csv`.
**Gate:** separar dependências verificadas de suposições.

### Gate 4 — Engenharia gráfica
Para WebGL/Three.js, investigar:
- Canvas/contexto (WebGL2, WebGPU, 2D), renderer, resize, DPR, loop RAF e lifecycle.
- Geometria, atributos, buffers, instancing, câmeras, luzes, materiais e texturas.
- Vertex/fragment shaders, uniforms (tempo, resolução, mouse, áudio), passes de pós-processamento, framebuffer, blending, tone mapping, color space.
- Física: amortecimento, springs, colisões, forças, interpolações, easing e unidades.
- Input: coordenadas normalizadas, pointer capture, touch, keyboard, scroll e accessibility.
- Performance: FPS mediano/p95, frametime, draw calls, memória, context loss, offscreen pause, DPR adaptativo e redução de movimento.
Quando autorizado, usar ferramentas de captura gráfica como Spector.js em ambiente controlado. Capturas de GPU e minificação podem ser incompletas.
**Saída:** `render-pipeline.md` com pseudocódigo original e diagramas.

### Gate 5 — Hipóteses e experimentos
Para cada efeito, registrar hipóteses testáveis e falsificáveis: por exemplo, “a distorção é deslocamento UV no fragment shader” versus “a distorção é deslocamento de vértices”. Projetar experimento que discrimine as duas opções (parallax, silhueta, resolução, câmera, inspeção de passes).
Não apresentar hipótese como fato.

### Gate 6 — Reconstrução independente
Criar especificação funcional própria e implementação limpa, sem copiar material não licenciado. Modularizar `core/renderer`, `core/input`, `effects/<id>`, `presets`, `tests`. Primeiro um protótipo vertical pequeno, sem galeria falsa. Implementar fallback WebGL e modo reduzido. Preservar licença e atribuição dos recursos realmente reutilizados.
**Gate:** protótipo cumpre requisitos de entrada, estado, saída e desempenho antes de novos módulos.

### Gate 7 — Testes comparativos
- Capturar original e reconstrução com mesmo viewport, DPR, estado, tempo e input.
- Comparar layout, forma, cores, iluminação, velocidade, interação e artefatos.
- Métricas: tolerâncias de geometria/posição, SSIM/erro perceptual quando apropriado, estabilidade, latência de input, FPS p50/p95 e console errors.
- Testar desktop, celular, touch, resize, WebGL indisponível, aba em segundo plano e reduced motion.
- Guardar evidências de falhas e correções. Não prometer “idêntico” sem validação.

### Gate 8 — Release controlado
Somente depois de aprovação dos gates: CI (typecheck, lint, build, testes), staging, teste em URL real, deploy Render, verificação de status `live` e smoke test do site publicado. Evitar deploy automático de commits de investigação no serviço de produção.

## Modelo de dossiê obrigatório
```md
# <Módulo>
- Origem e licença:
- Data/versão:
- Objetivo:
- Entradas / estados / saídas:
- Evidências (URL, hash, REA evidence_id, captura):
- Dependências confirmadas:
- Pipeline de renderização:
- Hipóteses abertas:
- Plano de experimentos:
- Critérios de aceitação:
- Limitações:
- Decisão: INVESTIGAR / PROTOTIPAR / VALIDAR / PUBLICAR
```

## Matriz de confiança
- A: fonte oficial + código/execução observada.
- B: fonte oficial ou repositório com evidência consistente.
- C: inferência técnica plausível.
- D: hipótese ainda não testada.
Jamais chamar C ou D de engenharia reversa concluída.

## Fontes de referência
- OWASP WSTG DevTools: https://wstg.owasp.org/latest/6-Appendix/F-Leveraging_Dev_Tools/
- OWASP JS e source maps: https://owasp.github.io/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Web_Page_Content_for_Information_Leakage
- Chrome DevTools Protocol: https://chromedevtools.github.io/devtools-protocol/
- Three.js ShaderMaterial: https://threejs.org/docs/pages/ShaderMaterial.html
- Three.js WebGLRenderer: https://threejs.org/docs/pages/WebGLRenderer
- HorizonX Drops: https://github.com/Mdx2025/horizonx-drops

## Aplicação ao projeto HorizonX independente
- Não assumir que cinco Drops representam o motor da plataforma.
- A licença HorizonX Drops permite incorporação em produtos finais, mas restringe redistribuição como biblioteca; Elastic Mesh é MIT. Confirmar texto atualizado antes de reutilizar.
- Priorizar o estudo do sistema de renderização e fluxo de dados; depois investigar editores Tools, Textures, Shaders e Mockups.
- O objetivo final é biblioteca de experiências imersivas independentes com prévias reais, não um editor genérico.
- Status inicial: REA sem alvo CDP acessível; não alegar análise de bundle oficial.


## Pesquisa comparativa de repositórios de engenharia reversa (fase de ferramentas)
Repositórios confirmados no GitHub:
- `NationalSecurityAgency/ghidra`: análise estática, decompilação e referências cruzadas de executáveis; útil para binários, não como primeira opção em sites.
- `radareorg/radare2`: análise programável de binários, strings, CFG e disassembly.
- `rizinorg/rizin`: framework modular de análise de baixo nível.
- `angr/angr`: execução simbólica e análise de caminhos em programas compilados.
- `HumanSecurity/restringer`: desofuscação de JavaScript; aplicar somente a scripts que podem ser legalmente analisados.
- `BabylonJS/Spector.js`: captura e inspeção de frames WebGL, shaders, draw calls e recursos gráficos.
- `microsoft/playwright`: automação reproduzível, screenshots, traces, eventos e teste de responsividade.
- `benvanik/WebGL-Inspector`: ferramenta histórica de inspeção WebGL; conferir manutenção antes de adotar.
- `alphaSeclab/awesome-reverse-engineering`: catálogo comunitário de ferramentas; validar individualmente.

### Matriz de seleção por alvo
| Alvo | Ferramentas prioritárias | Não começar por |
|---|---|---|
| SPA React/Next/Vite | DevTools, CDP, Playwright, REA web bundle | Ghidra |
| JS minificado/ofuscado | source maps públicos, AST, REstringer, REA | angr |
| WebGL/Three.js | Spector.js, shaders, Performance, REA | Apktool |
| WebAssembly | wasm tooling, browser devtools, análise de binários compatíveis | somente CSS inspector |
| Electron/ASAR | REA JS application, inspeção ASAR, CDP | somente screenshots |
| APK Android | Apktool, JADX, REA Android quando alvo estiver disponível | WebGL inspector |
| PE/ELF/Mach-O | Ghidra, Rizin/radare2, angr quando necessário | Playwright |

### Regra de adoção de ferramentas
Para cada projeto encontrado: ler README e licença; registrar versão, manutenção, instalação, capacidades, limites, segurança, saída reproduzível e teste mínimo. Um repositório GitHub existente **não comprova** que foi executado nem que pode ser redistribuído. Só adicionar uma ferramenta à pipeline após teste de compatibilidade.

### Gate de integração com REA
REA não é sinônimo de acesso universal ao navegador, ao GitHub ou ao computador do usuário. Selecionar a capacidade específica do REA e confirmar alvo, provider e evidência antes de analisar. Se falhar, registrar erro exato e preparar alternativa técnica. Nunca afirmar captura inexistente.
