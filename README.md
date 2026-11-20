# maestro-roteador

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


![Skill orienta, orquestrador decide, agente executa](guia/assets/hero-maestro.jpg)

Skill de **triagem de modelo e esforÃ§o** para Claude Code: recebe um problema bruto e decide qual modelo (haiku / sonnet / opus / fable) e qual esforÃ§o de raciocÃ­nio (low â†’ max) usar em cada parte do trabalho, antes de despachar subagentes ou workflows.

**Guia completo (landing + passo a passo):** https://inematds.github.io/maestro-roteador/guia/
**Tira-dÃºvidas para iniciantes (analogias, FAQ e passo a passo):** [docs/guia-explicativo.md](docs/guia-explicativo.md)
**ReferÃªncia de dados de esforÃ§o (Opus 5.5 e GPT-6 Astra, com grÃ¡ficos):** https://inematds.github.io/modelos/guia/esforco/ â€” evidÃªncia de terceiros que reforÃ§a a escada; nÃ£o altera o procedimento.
**English version:** [README.en.md](README.en.md) Â· [SKILL.en.md](skills/maestro-roteador/SKILL.en.md)

---

## Por que essa skill existe

Sem regra nenhuma, agentes colapsam qualquer tarefa em "sonnet + medium" â€” o meio-termo genÃ©rico. Isso erra nas duas direÃ§Ãµes ao mesmo tempo:

- **Paga demais** em tarefas mecÃ¢nicas (renomear 80 arquivos nÃ£o precisa de sonnet, nem de medium);
- **Paga de menos** em tarefas com risco real (migraÃ§Ã£o de produÃ§Ã£o precisa de mais verificaÃ§Ã£o, nÃ£o de "medium com cuidado") e em tarefas de gosto (copy com voz de marca precisa de repertÃ³rio, que sonnet nÃ£o tem).

A skill substitui o meio-termo por **duas perguntas independentes**, respondidas separadamente.

## A ideia central: dois eixos que nÃ£o se convertem

| Eixo | Pergunta que ele responde | O que ele compra |
|---|---|---|
| **Modelo** | Qual nÃ­vel de repertÃ³rio/critÃ©rio o **pior passo** da tarefa exige? | RepertÃ³rio (gosto, sutileza, julgamento sem gabarito) |
| **EsforÃ§o** | Quanta deliberaÃ§Ã£o e verificaÃ§Ã£o a **ambiguidade + custo do erro** pagam? | DeliberaÃ§Ã£o (checar o prÃ³prio trabalho, comparar caminhos) |

**EsforÃ§o compra deliberaÃ§Ã£o, nunca repertÃ³rio. Modelo compra repertÃ³rio, nunca verificaÃ§Ã£o.** Ã‰ por isso que combinaÃ§Ãµes "cruzadas" sÃ£o legÃ­timas e frequentes:

- `fable + low` â†’ roteiro com voz de marca: o pior passo Ã© **gosto** (eixo modelo), mas a decisÃ£o Ã© direta (esforÃ§o baixo).
- `sonnet + high` â†’ migraÃ§Ã£o com produÃ§Ã£o em risco: a capacidade exigida Ã© **padrÃ£o** (eixo modelo), mas o erro Ã© caro â€” o risco pede **verificaÃ§Ã£o**, nÃ£o inteligÃªncia.

Quem escolhe "sonnet+medium equilibrado" estÃ¡ fugindo das duas perguntas, nÃ£o respondendo Ã s duas.

## O procedimento (sempre nesta ordem)

1. **Isole o pior passo.** Da tarefa inteira, qual passo mais exige critÃ©rio fino?
2. **Modelo = o MENOR que resolve bem esse pior passo.**
   - MecÃ¢nico com gabarito claro (renomear, extrair, formatar) â†’ `haiku`
   - ImplementaÃ§Ã£o padrÃ£o, correta-ou-errada verificÃ¡vel â†’ `sonnet`
   - Julgamento sem gabarito (voz de marca, arquitetura, diagnÃ³stico obscuro) â†’ `opus`/`fable`
3. **EsforÃ§o = ambiguidade + custo do erro.**
   - SoluÃ§Ã£o Ãºnica + erro barato â†’ `low`
   - Escolha entre caminhos OU erro chato de corrigir â†’ `medium`
   - VÃ¡rios caminhos plausÃ­veis OU erro caro (produÃ§Ã£o, dado perdido, publicaÃ§Ã£o) â†’ `high`+
   - **Risco nomeado = high, no mÃ­nimo.** Se vocÃª mesmo escreveu "produÃ§Ã£o em risco" na justificativa, "medium com cuidado" nÃ£o existe.
   - **Escada com evidÃªncia (sÃ³ para erro barato):** se o erro Ã© reversÃ­vel e verificÃ¡vel na hora, comece no menor esforÃ§o plausÃ­vel e **sÃ³ suba com evidÃªncia** de resultado insuficiente â€” nunca "high por garantia". A escada NÃƒO se aplica a risco irreversÃ­vel: aÃ­ a "evidÃªncia de falha" seria o prÃ³prio prejuÃ­zo.
4. **Volume:** N itens iguais â†’ teste 1 item no menor modelo; se passa, o lote inteiro vai nele.

![Fluxo do roteador: pedido â†’ anÃ¡lise â†’ agente certo â†’ resultado](guia/assets/roteador-agentes.jpg)

## Tabela rÃ¡pida

| Tarefa | Modelo | EsforÃ§o | Por quÃª |
|---|---|---|---|
| Renomear/extrair/formatar em lote | haiku | low | Gabarito claro, erro barato |
| Feature clara, refactor localizado | sonnet | medium | PadrÃ£o, verificÃ¡vel |
| MigraÃ§Ã£o de dados com produÃ§Ã£o em risco | sonnet | high | Capacidade padrÃ£o; o risco pede verificaÃ§Ã£o, nÃ£o inteligÃªncia |
| Roteiro/copy com voz de marca | fable | low | Pior passo Ã© gosto (repertÃ³rio); decisÃ£o Ã© direta |
| Bug misterioso, decisÃ£o de arquitetura | fable | high | DifÃ­cil E ambÃ­guo |

## Armadilhas â€” cada uma explicada

RacionalizaÃ§Ãµes vistas em teste real, e por que estÃ£o erradas:

1. **"sonnet+medium equilibra custo e qualidade."** Meio-termo Ã© fugir das duas perguntas. Responda cada eixo; o resultado quase nunca Ã© o centro da matriz.
2. **"Roteiro curto nÃ£o exige raciocÃ­nio pesado" â†’ rebaixa o modelo.** ConfusÃ£o de eixos: nÃ£o exigir *deliberaÃ§Ã£o* justifica esforÃ§o low â€” nÃ£o justifica modelo menor quando o pior passo Ã© critÃ©rio de marca. A resposta Ã© `fable+low`.
3. **"Medium Ã© suficiente pra fazer com cuidado" (com produÃ§Ã£o em risco).** VocÃª mesmo nomeou o risco. Cuidado Ã© exatamente o que o esforÃ§o compra â€” risco nomeado = high.
4. **"Vou de modelo maior por garantia."** Se o pior passo estÃ¡ dentro da capacidade do menor, o maior entrega o mesmo cobrando mais. Garantia se compra com esforÃ§o/verificaÃ§Ã£o, nÃ£o com modelo.
5. **"Modelo pequeno + esforÃ§o max sai barato."** Pior combinaÃ§Ã£o possÃ­vel: paga deliberaÃ§Ã£o pra quem nÃ£o tem repertÃ³rio pra usÃ¡-la, e os tokens de raciocÃ­nio acumulam.
6. **"Subo o esforÃ§o pra sair mais bonito/caprichado."** EsforÃ§o compra deliberaÃ§Ã£o, nÃ£o gosto. Acabamento Ã© eixo de **modelo** (ver "empate de gosto" abaixo). Medido em teste real com 12 nÃ­veis de esforÃ§o em 2 providers: de high pra max, a diferenÃ§a foi um favicon â€” por 2â€“5Ã— os tokens.
7. **"EsforÃ§o a mais nÃ£o ajuda, mas tambÃ©m nÃ£o atrapalha."** Atrapalha â€” e o overthinking Ã© o **excesso do prÃ³prio eixo esforÃ§o**, nÃ£o um defeito do modelo. EsforÃ§o compra deliberaÃ§Ã£o; quando sobra deliberaÃ§Ã£o numa tarefa simples, o modelo re-explora caminhos jÃ¡ decididos e superdimensiona a soluÃ§Ã£o. O resultado pode sair **pior**, nÃ£o sÃ³ mais caro â€” como quem revisa tanto a prova que troca a resposta certa pela errada. Os dois erros do eixo sÃ£o simÃ©tricos: esforÃ§o de menos em tarefa ambÃ­gua erra por falta de verificaÃ§Ã£o; esforÃ§o de mais em tarefa simples erra por ruÃ­do. O esforÃ§o certo Ã© o **menor que cobre o risco**.

## Anti-overhead: a triagem tambÃ©m tem custo

- **Tarefa mais barata que a prÃ³pria triagem nÃ£o recebe triagem** â€” "corrige esse typo" vai direto no default do turno, sem YAML de despacho.
- **A triagem roda inline no turno principal, nunca num subagente** â€” despachar um agente sÃ³ pra decidir modelo/esforÃ§o custa mais que a decisÃ£o vale.
- **Fragmentar tambÃ©m tem custo:** sÃ³ despache uma parte pra subagente se o trabalho dela superar o overhead do spawn.

## Empate de gosto â†’ a escolha Ã© do usuÃ¡rio (custo Ã— qualidade)

**Erro nÃ£o Ã© negociÃ¡vel; eficiÃªncia Ã©.** Quando a escolha do modelo depende sÃ³ de **acabamento** (qualidade de copy, polish visual) e nÃ£o de correÃ§Ã£o, a decisÃ£o Ã© de orÃ§amento â€” e orÃ§amento Ã© do usuÃ¡rio. A triagem apresenta o par e nÃ£o decide sozinha:

```yaml
opcoes:
  custo: sonnet+low        # estrutura correta, acabamento funcional
  qualidade: fable+low     # mesma estrutura, copy/polish superior
decide: usuÃ¡rio
```

Sinal de empate: existe um template/skill que jÃ¡ garantiu a estrutura (a correÃ§Ã£o vem do gabarito) e o que sobra pro modelo grande Ã© sÃ³ voz/acabamento. **Nunca** oferecer o par quando o risco Ã© de correÃ§Ã£o â€” aÃ­ o modelo/esforÃ§o que garante a correÃ§Ã£o Ã© o mÃ­nimo, nÃ£o uma escolha.

## Cache: o custo escondido da troca de modelo

O prompt cache da Anthropic Ã© **por modelo** e depende de prefixo idÃªntico do contexto (`tools â†’ system â†’ messages`). Ler do cache custa ~0,1Ã— o preÃ§o de entrada; gravar custa 1,25Ã— (TTL 5 min) ou 2Ã— (TTL 1h). Perder o cache de um contexto de 100k tokens torna a prÃ³xima entrada **~12,5Ã— mais cara**. TrÃªs regras na triagem:

1. **Troca de `/model` do turno principal sÃ³ em fronteira de trabalho** (fim de fase, handoff) â€” nunca no meio de um bloco. Em contexto grande, a troca pode custar mais que a economia do modelo menor. NÃ£o alterne `opus â†’ sonnet â†’ opus` dentro do mesmo bloco.
2. **Subagente NÃƒO paga esse custo.** Cada Agent/Workflow tem contexto prÃ³prio: rotear uma parte pra um modelo menor via subagente nÃ£o toca o cache principal. Ã‰ o caminho preferido pra aplicar a matriz.
3. **Nunca keepalive artificial** ("ainda estÃ¡ aÃ­?") pra segurar o cache â€” o Claude Code gerencia o cache sozinho e o ping custa mais do que salva. Pausa longa? Handoff enxuto e deixe expirar.

Rotina que preserva cache: blocos contÃ­nuos de trabalho, comeÃ§o do contexto estÃ¡vel (nÃ£o ligar/desligar ferramentas e MCPs no meio do bloco), tarefas relacionadas concentradas na mesma sessÃ£o.

## Harness > esforÃ§o: onde o resultado nasce

O modelo Ã© um cÃ©rebro num pote â€” ferramentas, arquivos, terminal, skills e instruÃ§Ãµes (o **harness**) sÃ£o os braÃ§os dele. Num teste real com a mesma tarefa em 12 nÃ­veis de esforÃ§o e 2 providers, os resultados funcionais foram quase idÃªnticos; os nÃ­veis altos adicionaram diferenÃ§as estÃ©ticas (favicon, sombras, um donut chart) por 2â€“5Ã— os tokens.

A liÃ§Ã£o: **uma spec clara em low entrega o que o esforÃ§o max tenta adivinhar.** Antes de subir o esforÃ§o, melhore o prompt, a especificaÃ§Ã£o do "pronto" e as ferramentas disponÃ­veis.

![O agente certo para a tarefa certa, sempre](guia/assets/roteador-futuro.jpg)

## Formato de saÃ­da (plano de despacho)

```yaml
tarefa: <resumo>
partes:
  - o_que: <subtarefa>
    pior_passo: <qual e por quÃª>
    modelo: haiku|sonnet|opus|fable
    esforco: low|medium|high|xhigh|max
    risco: <custo do erro em 1 linha>
turno_principal: <recomendaÃ§Ã£o de /model se valer trocar â€” a troca Ã© do usuÃ¡rio>
```

**Limite:** para subagentes/workflows a decisÃ£o Ã© automÃ¡tica (parÃ¢metros `model`/`effort` das chamadas Agent/Workflow); para o modelo da conversa principal a skill apenas recomenda â€” a troca Ã© do usuÃ¡rio via `/model`, de preferÃªncia em fronteira de trabalho (ver Cache).

## ValidaÃ§Ã£o (TDD de skill)

A skill foi escrita contra um baseline medido: sem ela, agentes colapsam qualquer tarefa em "sonnet + medium". Com ela, os trÃªs cenÃ¡rios de teste foram para o canto certo da matriz:

| CenÃ¡rio | Sem skill | Com skill |
|---|---|---|
| Renomear 80 arquivos em lote | sonnet + medium | haiku + low |
| Roteiro com voz de marca | sonnet + medium | fable + low |
| MigraÃ§Ã£o de dados com produÃ§Ã£o em risco | sonnet + medium | sonnet + high |

## Estrutura

```
skills/
  maestro-roteador/
    SKILL.md       # a skill: princÃ­pio, procedimento, tabela, armadilhas, cache, saÃ­da
    SKILL.en.md    # traduÃ§Ã£o em inglÃªs da skill
guia/
  index.html    # landing + guia de uso (GitHub Pages)
  assets/       # imagens do guia
docs/
  guia-explicativo.md  # tira-dÃºvidas educativo: analogias, FAQ, passo a passo
```

## InstalaÃ§Ã£o

```bash
git clone https://github.com/inematds/maestro-roteador.git
ln -sfn "$(pwd)/maestro-roteador/skills/maestro-roteador" ~/.claude/skills/maestro-roteador
```

Ou copie a pasta `skills/maestro-roteador/` para `~/.claude/skills/` (skills de usuÃ¡rio) ou `.claude/skills/` de um projeto.

## Uso

PeÃ§a a triagem diretamente â€” "faz a triagem disso", "qual modelo e esforÃ§o pra isso?" â€” ou apenas peÃ§a o trabalho: ao despachar subagentes/workflows, o agente aplica a matriz e distribui cada parte com `model` e `effort` adequados.

## Changelog

- **v1.3.3** â€” Fable 5.2: Opus 5.5 medium como ponto de partida, escada lowâ†’high sÃ³ no que falhar, dividir sÃ³ nos 2 casos medidos, ajudantes com `model`/`effort` fixos, Explore no Haiku, Fable como revisor (base: documento oficial da Anthropic + mediÃ§Ãµes do [fable-barato](https://github.com/inematds/fable-barato)); campo `dividir` no plano de despacho.
- **v1.2.3** â€” versÃµes em inglÃªs da skill (SKILL.en.md) e do README (README.en.md).
- **v1.2.2** â€” armadilha do overthinking explicitada (excesso do prÃ³prio eixo esforÃ§o; o esforÃ§o certo Ã© o menor que cobre o risco) + docs/guia-explicativo.md (tira-dÃºvidas educativo com analogias, FAQ e passo a passo).
- **v1.2.1** â€” escada com evidÃªncia (erro barato comeÃ§a em low e sobe sÃ³ com evidÃªncia), 2 armadilhas novas (estÃ©tica nÃ£o Ã© esforÃ§o; overthinking piora), seÃ§Ã£o de cache (troca sÃ³ em fronteira, subagente grÃ¡tis em cache, sem keepalive); guia ganha hero com imagem + seÃ§Ãµes Cache e Harness; README educativo completo.
- **v1.1.1** â€” guia/index.html (landing+guia padrÃ£o INEMA) + regra anti-overhead na skill.
- **v1.1.0** â€” empate de gosto oferece escolha custo (sonnet+low) Ã— qualidade (fable+low).
- **v1.0.0** â€” skill maestro-roteador: triagem de modelo e esforÃ§o (matriz de dois eixos).

## LicenÃ§a

MIT â€” projeto de pesquisa/educaÃ§Ã£o INEMA.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>