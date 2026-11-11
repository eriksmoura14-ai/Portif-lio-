# Auditoria técnica — HorizonX (fase 1, 2026-11-10)

## Objetivo
Investigar o funcionamento da ferramenta HorizonX em fases verificáveis **antes** de implementar qualquer clone, biblioteca ou experiência. Proibido preencher contagens com efeitos inventados.

## Escopo real da plataforma
- Biblioteca Explore: catálogo indexado com 482 itens no momento da inspeção: https://horizonx.so/explore
- Ferramentas: editores interativos de UI, 3D, pointer physics, scroll, carrosséis e outros: https://horizonx.so/tools/plasma-orb
- Shaders: catálogo indexado com 33 efeitos: https://horizonx.so/shaders
- Textures, Mockups e outras seções: https://horizonx.so/docs
- Drops: componentes públicos distribuídos em https://github.com/Mdx2025/horizonx-drops

Contagens representam a página na data consultada e podem mudar. **Não** representam código-fonte disponível.

## Repositório público verificado
Repositório: https://github.com/Mdx2025/horizonx-drops
- Stack demonstrativa: React 19, TypeScript, Vite e Three.js; `package.json` confirma.
- Registro shadcn: `registry.json` descreve cada drop e suas dependências.
- Caminhos: `registry/drops/<slug>/<slug>.tsx` (wrapper React), `<slug>.engine.js` (motor JS), `<slug>.engine.d.ts` (tipos).
- Componentes encontrados: Plasma Orb (WebGL2), Ember Sun (WebGL), Ember Eclipse (WebGL), Opal Loop (Three.js), Elastic Mesh (Three.js).
- Os cinco não são o código-fonte do editor do HorizonX nem de todo o site.
- Verificação: `pnpm check` (typecheck + shadcn registry build + Vite build); `scripts/smoke-install.sh`.

## Engenharia reversa: método de evidências
Para cada módulo:
1. Capturar URL oficial, controles, estados, entradas, saídas, eventos e comportamento visual.
2. Localizar código publicamente licenciado, referências técnicas e arquivos legitimamente disponíveis.
3. Extrair mapa: interface -> estado -> motor de renderização -> GPU/assets -> saída.
4. Usar REA em alvo hospedado/acessível ao próprio REA para analisar bundle JS, dependências, rotas, shaders e event handlers; registrar provas e limitações.
5. Recriar **implementação própria** baseada no comportamento observado quando não houver código licenciado para reutilização; atribuir origem e distinguir o que é original, reconstruído e apenas referenciado.
6. Validar imagem/movimento, interações com mouse/toque, performance e compatibilidade, comparando com as referências oficiais.
7. Somente depois promover código para o app e publicar no Render.

## REA — tentativa e limitação verificada
- `list_browser_targets` em CDP local (127.0.0.1:9222) respondeu `endpoint_unreachable`.
- `capture_browser_scenario` com Chromium também retornou `execution_failure`.
- Portanto **não há captura REA bem-sucedida do HorizonX nesta fase**. Não assumir que scripts ou shaders do site foram extraídos.
- Próxima condição técnica: ambiente de navegador controlado e acessível ao REA, ou artefatos JavaScript copiados para um alvo no servidor REA com proveniência verificável.

## Restrição jurídica importante
HorizonX Drops License 1.0: permite incorporar Drops em produtos finais, mas proíbe redistribuí-los como biblioteca/kit/registry. Elastic Mesh é MIT. Fonte: https://horizonx.so/licenses/drops
A galeria de Drops já publicada pode não se enquadrar como produto final autorizado: revisar arquitetura e uso **antes** de ampliá-la. Demais ativos premium não podem ser redistribuídos como código-fonte por padrão: https://horizonx.so/terms

## Plano de investigação (gates)
- **Fase 1 [parcial]** Inventário de módulos, licenças, URLs, fontes de código, restrições.
- **Fase 2 [pendente]** Captura comportamental do editor e WebGL com artefatos verificáveis (REA, CDP e medições).
- **Fase 3 [pendente]** Dossiê técnico por efeito priorizado, com diagrama, shaders/pipeline quando verificáveis, benchmarks e critérios de fidelidade.
- **Fase 4 [pendente]** Implementação independente de um protótipo, com teste visual em desktop e celular.
- **Fase 5 [pendente]** Validação, correção e publicação controlada.

## Primeiro alvo de engenharia
Plasma Orb: há código público e uma especificação funcional verificável.
URL: https://horizonx.so/tools/plasma-orb
Comportamento publicado: WebGL2, glass shell ray tracing, filaments, pointer charging, click pulse, ajuste de qualidade, pause offscreen e adaptação de resolução.
**Não afirmar que cada detalhe foi inspecionado na execução** até haver captura de comportamento no ambiente controlado.

## Política de mudanças
Não fazer mudanças no deploy com base apenas em inferências. A cada etapa, documentar evidências, lacunas, critérios de sucesso e condições de avanço.
