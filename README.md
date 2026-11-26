ï»¿# maestro-roteador

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="48" />
  <p><strong>Arckhé AI</strong></p>
</div>


![Skill orienta, orquestrador decide, agente executa](guia/assets/hero-maestro.jpg)

Skill de **triagem de modelo e esforêÂ§o** para Claude Code: recebe um problema bruto e decide qual modelo (haiku / sonnet / opus / fable) e qual esforêÂ§o de raciocêÂ­nio (low ê¢â â max) usar em cada parte do trabalho, antes de despachar subagentes ou workflows.

**Guia completo (landing + passo a passo):** https://inematds.github.io/maestro-roteador/guia/
**Tira-dêÂºvidas para iniciantes (analogias, FAQ e passo a passo):** [docs/guia-explicativo.md](docs/guia-explicativo.md)
**ReferêÂªncia de dados de esforêÂ§o (Opus 5.5 e GPT-6 Astra, com grêÂ¡ficos):** https://inematds.github.io/modelos/guia/esforco/ ê¢â¬â evidêÂªncia de terceiros que reforêÂ§a a escada; nêÂ£o altera o procedimento.
**English version:** [README.en.md](README.en.md) êÂ· [SKILL.en.md](skills/maestro-roteador/SKILL.en.md)

---

## Por que essa skill existe

Sem regra nenhuma, agentes colapsam qualquer tarefa em "sonnet + medium" ê¢â¬â o meio-termo genêÂ©rico. Isso erra nas duas direêÂ§êÂµes ao mesmo tempo:

- **Paga demais** em tarefas mecêÂ¢nicas (renomear 80 arquivos nêÂ£o precisa de sonnet, nem de medium);
- **Paga de menos** em tarefas com risco real (migraêÂ§êÂ£o de produêÂ§êÂ£o precisa de mais verificaêÂ§êÂ£o, nêÂ£o de "medium com cuidado") e em tarefas de gosto (copy com voz de marca precisa de repertêÂ³rio, que sonnet nêÂ£o tem).

A skill substitui o meio-termo por **duas perguntas independentes**, respondidas separadamente.

## A ideia central: dois eixos que nêÂ£o se convertem

| Eixo | Pergunta que ele responde | O que ele compra |
|---|---|---|
| **Modelo** | Qual nêÂ­vel de repertêÂ³rio/critêÂ©rio o **pior passo** da tarefa exige? | RepertêÂ³rio (gosto, sutileza, julgamento sem gabarito) |
| **EsforêÂ§o** | Quanta deliberaêÂ§êÂ£o e verificaêÂ§êÂ£o a **ambiguidade + custo do erro** pagam? | DeliberaêÂ§êÂ£o (checar o prêÂ³prio trabalho, comparar caminhos) |

**EsforêÂ§o compra deliberaêÂ§êÂ£o, nunca repertêÂ³rio. Modelo compra repertêÂ³rio, nunca verificaêÂ§êÂ£o.** êâ° por isso que combinaêÂ§êÂµes "cruzadas" sêÂ£o legêÂ­timas e frequentes:

- `fable + low` ê¢â â roteiro com voz de marca: o pior passo êÂ© **gosto** (eixo modelo), mas a decisêÂ£o êÂ© direta (esforêÂ§o baixo).
- `sonnet + high` ê¢â â migraêÂ§êÂ£o com produêÂ§êÂ£o em risco: a capacidade exigida êÂ© **padrêÂ£o** (eixo modelo), mas o erro êÂ© caro ê¢â¬â o risco pede **verificaêÂ§êÂ£o**, nêÂ£o inteligêÂªncia.

Quem escolhe "sonnet+medium equilibrado" estêÂ¡ fugindo das duas perguntas, nêÂ£o respondendo êÂ s duas.

## O procedimento (sempre nesta ordem)

1. **Isole o pior passo.** Da tarefa inteira, qual passo mais exige critêÂ©rio fino?
2. **Modelo = o MENOR que resolve bem esse pior passo.**
   - MecêÂ¢nico com gabarito claro (renomear, extrair, formatar) ê¢â â `haiku`
   - ImplementaêÂ§êÂ£o padrêÂ£o, correta-ou-errada verificêÂ¡vel ê¢â â `sonnet`
   - Julgamento sem gabarito (voz de marca, arquitetura, diagnêÂ³stico obscuro) ê¢â â `opus`/`fable`
3. **EsforêÂ§o = ambiguidade + custo do erro.**
   - SoluêÂ§êÂ£o êÂºnica + erro barato ê¢â â `low`
   - Escolha entre caminhos OU erro chato de corrigir ê¢â â `medium`
   - VêÂ¡rios caminhos plausêÂ­veis OU erro caro (produêÂ§êÂ£o, dado perdido, publicaêÂ§êÂ£o) ê¢â â `high`+
   - **Risco nomeado = high, no mêÂ­nimo.** Se vocêÂª mesmo escreveu "produêÂ§êÂ£o em risco" na justificativa, "medium com cuidado" nêÂ£o existe.
   - **Escada com evidêÂªncia (sêÂ³ para erro barato):** se o erro êÂ© reversêÂ­vel e verificêÂ¡vel na hora, comece no menor esforêÂ§o plausêÂ­vel e **sêÂ³ suba com evidêÂªncia** de resultado insuficiente ê¢â¬â nunca "high por garantia". A escada NêÆO se aplica a risco irreversêÂ­vel: aêÂ­ a "evidêÂªncia de falha" seria o prêÂ³prio prejuêÂ­zo.
4. **Volume:** N itens iguais ê¢â â teste 1 item no menor modelo; se passa, o lote inteiro vai nele.

![Fluxo do roteador: pedido ê¢â â anêÂ¡lise ê¢â â agente certo ê¢â â resultado](guia/assets/roteador-agentes.jpg)

## Tabela rêÂ¡pida

| Tarefa | Modelo | EsforêÂ§o | Por quêÂª |
|---|---|---|---|
| Renomear/extrair/formatar em lote | haiku | low | Gabarito claro, erro barato |
| Feature clara, refactor localizado | sonnet | medium | PadrêÂ£o, verificêÂ¡vel |
| MigraêÂ§êÂ£o de dados com produêÂ§êÂ£o em risco | sonnet | high | Capacidade padrêÂ£o; o risco pede verificaêÂ§êÂ£o, nêÂ£o inteligêÂªncia |
| Roteiro/copy com voz de marca | fable | low | Pior passo êÂ© gosto (repertêÂ³rio); decisêÂ£o êÂ© direta |
| Bug misterioso, decisêÂ£o de arquitetura | fable | high | DifêÂ­cil E ambêÂ­guo |

## Armadilhas ê¢â¬â cada uma explicada

RacionalizaêÂ§êÂµes vistas em teste real, e por que estêÂ£o erradas:

1. **"sonnet+medium equilibra custo e qualidade."** Meio-termo êÂ© fugir das duas perguntas. Responda cada eixo; o resultado quase nunca êÂ© o centro da matriz.
2. **"Roteiro curto nêÂ£o exige raciocêÂ­nio pesado" ê¢â â rebaixa o modelo.** ConfusêÂ£o de eixos: nêÂ£o exigir *deliberaêÂ§êÂ£o* justifica esforêÂ§o low ê¢â¬â nêÂ£o justifica modelo menor quando o pior passo êÂ© critêÂ©rio de marca. A resposta êÂ© `fable+low`.
3. **"Medium êÂ© suficiente pra fazer com cuidado" (com produêÂ§êÂ£o em risco).** VocêÂª mesmo nomeou o risco. Cuidado êÂ© exatamente o que o esforêÂ§o compra ê¢â¬â risco nomeado = high.
4. **"Vou de modelo maior por garantia."** Se o pior passo estêÂ¡ dentro da capacidade do menor, o maior entrega o mesmo cobrando mais. Garantia se compra com esforêÂ§o/verificaêÂ§êÂ£o, nêÂ£o com modelo.
5. **"Modelo pequeno + esforêÂ§o max sai barato."** Pior combinaêÂ§êÂ£o possêÂ­vel: paga deliberaêÂ§êÂ£o pra quem nêÂ£o tem repertêÂ³rio pra usêÂ¡-la, e os tokens de raciocêÂ­nio acumulam.
6. **"Subo o esforêÂ§o pra sair mais bonito/caprichado."** EsforêÂ§o compra deliberaêÂ§êÂ£o, nêÂ£o gosto. Acabamento êÂ© eixo de **modelo** (ver "empate de gosto" abaixo). Medido em teste real com 12 nêÂ­veis de esforêÂ§o em 2 providers: de high pra max, a diferenêÂ§a foi um favicon ê¢â¬â por 2ê¢â¬â5êâ os tokens.
7. **"EsforêÂ§o a mais nêÂ£o ajuda, mas tambêÂ©m nêÂ£o atrapalha."** Atrapalha ê¢â¬â e o overthinking êÂ© o **excesso do prêÂ³prio eixo esforêÂ§o**, nêÂ£o um defeito do modelo. EsforêÂ§o compra deliberaêÂ§êÂ£o; quando sobra deliberaêÂ§êÂ£o numa tarefa simples, o modelo re-explora caminhos jêÂ¡ decididos e superdimensiona a soluêÂ§êÂ£o. O resultado pode sair **pior**, nêÂ£o sêÂ³ mais caro ê¢â¬â como quem revisa tanto a prova que troca a resposta certa pela errada. Os dois erros do eixo sêÂ£o simêÂ©tricos: esforêÂ§o de menos em tarefa ambêÂ­gua erra por falta de verificaêÂ§êÂ£o; esforêÂ§o de mais em tarefa simples erra por ruêÂ­do. O esforêÂ§o certo êÂ© o **menor que cobre o risco**.

## Anti-overhead: a triagem tambêÂ©m tem custo

- **Tarefa mais barata que a prêÂ³pria triagem nêÂ£o recebe triagem** ê¢â¬â "corrige esse typo" vai direto no default do turno, sem YAML de despacho.
- **A triagem roda inline no turno principal, nunca num subagente** ê¢â¬â despachar um agente sêÂ³ pra decidir modelo/esforêÂ§o custa mais que a decisêÂ£o vale.
- **Fragmentar tambêÂ©m tem custo:** sêÂ³ despache uma parte pra subagente se o trabalho dela superar o overhead do spawn.

## Empate de gosto ê¢â â a escolha êÂ© do usuêÂ¡rio (custo êâ qualidade)

**Erro nêÂ£o êÂ© negociêÂ¡vel; eficiêÂªncia êÂ©.** Quando a escolha do modelo depende sêÂ³ de **acabamento** (qualidade de copy, polish visual) e nêÂ£o de correêÂ§êÂ£o, a decisêÂ£o êÂ© de orêÂ§amento ê¢â¬â e orêÂ§amento êÂ© do usuêÂ¡rio. A triagem apresenta o par e nêÂ£o decide sozinha:

```yaml
opcoes:
  custo: sonnet+low        # estrutura correta, acabamento funcional
  qualidade: fable+low     # mesma estrutura, copy/polish superior
decide: usuêÂ¡rio
```

Sinal de empate: existe um template/skill que jêÂ¡ garantiu a estrutura (a correêÂ§êÂ£o vem do gabarito) e o que sobra pro modelo grande êÂ© sêÂ³ voz/acabamento. **Nunca** oferecer o par quando o risco êÂ© de correêÂ§êÂ£o ê¢â¬â aêÂ­ o modelo/esforêÂ§o que garante a correêÂ§êÂ£o êÂ© o mêÂ­nimo, nêÂ£o uma escolha.

## Cache: o custo escondido da troca de modelo

O prompt cache da Anthropic êÂ© **por modelo** e depende de prefixo idêÂªntico do contexto (`tools ê¢â â system ê¢â â messages`). Ler do cache custa ~0,1êâ o preêÂ§o de entrada; gravar custa 1,25êâ (TTL 5 min) ou 2êâ (TTL 1h). Perder o cache de um contexto de 100k tokens torna a prêÂ³xima entrada **~12,5êâ mais cara**. TrêÂªs regras na triagem:

1. **Troca de `/model` do turno principal sêÂ³ em fronteira de trabalho** (fim de fase, handoff) ê¢â¬â nunca no meio de um bloco. Em contexto grande, a troca pode custar mais que a economia do modelo menor. NêÂ£o alterne `opus ê¢â â sonnet ê¢â â opus` dentro do mesmo bloco.
2. **Subagente NêÆO paga esse custo.** Cada Agent/Workflow tem contexto prêÂ³prio: rotear uma parte pra um modelo menor via subagente nêÂ£o toca o cache principal. êâ° o caminho preferido pra aplicar a matriz.
3. **Nunca keepalive artificial** ("ainda estêÂ¡ aêÂ­?") pra segurar o cache ê¢â¬â o Claude Code gerencia o cache sozinho e o ping custa mais do que salva. Pausa longa? Handoff enxuto e deixe expirar.

Rotina que preserva cache: blocos contêÂ­nuos de trabalho, comeêÂ§o do contexto estêÂ¡vel (nêÂ£o ligar/desligar ferramentas e MCPs no meio do bloco), tarefas relacionadas concentradas na mesma sessêÂ£o.

## Harness > esforêÂ§o: onde o resultado nasce

O modelo êÂ© um cêÂ©rebro num pote ê¢â¬â ferramentas, arquivos, terminal, skills e instruêÂ§êÂµes (o **harness**) sêÂ£o os braêÂ§os dele. Num teste real com a mesma tarefa em 12 nêÂ­veis de esforêÂ§o e 2 providers, os resultados funcionais foram quase idêÂªnticos; os nêÂ­veis altos adicionaram diferenêÂ§as estêÂ©ticas (favicon, sombras, um donut chart) por 2ê¢â¬â5êâ os tokens.

A liêÂ§êÂ£o: **uma spec clara em low entrega o que o esforêÂ§o max tenta adivinhar.** Antes de subir o esforêÂ§o, melhore o prompt, a especificaêÂ§êÂ£o do "pronto" e as ferramentas disponêÂ­veis.

![O agente certo para a tarefa certa, sempre](guia/assets/roteador-futuro.jpg)

## Formato de saêÂ­da (plano de despacho)

```yaml
tarefa: <resumo>
partes:
  - o_que: <subtarefa>
    pior_passo: <qual e por quêÂª>
    modelo: haiku|sonnet|opus|fable
    esforco: low|medium|high|xhigh|max
    risco: <custo do erro em 1 linha>
turno_principal: <recomendaêÂ§êÂ£o de /model se valer trocar ê¢â¬â a troca êÂ© do usuêÂ¡rio>
```

**Limite:** para subagentes/workflows a decisêÂ£o êÂ© automêÂ¡tica (parêÂ¢metros `model`/`effort` das chamadas Agent/Workflow); para o modelo da conversa principal a skill apenas recomenda ê¢â¬â a troca êÂ© do usuêÂ¡rio via `/model`, de preferêÂªncia em fronteira de trabalho (ver Cache).

## ValidaêÂ§êÂ£o (TDD de skill)

A skill foi escrita contra um baseline medido: sem ela, agentes colapsam qualquer tarefa em "sonnet + medium". Com ela, os trêÂªs cenêÂ¡rios de teste foram para o canto certo da matriz:

| CenêÂ¡rio | Sem skill | Com skill |
|---|---|---|
| Renomear 80 arquivos em lote | sonnet + medium | haiku + low |
| Roteiro com voz de marca | sonnet + medium | fable + low |
| MigraêÂ§êÂ£o de dados com produêÂ§êÂ£o em risco | sonnet + medium | sonnet + high |

## Estrutura

```
skills/
  maestro-roteador/
    SKILL.md       # a skill: princêÂ­pio, procedimento, tabela, armadilhas, cache, saêÂ­da
    SKILL.en.md    # traduêÂ§êÂ£o em inglêÂªs da skill
guia/
  index.html    # landing + guia de uso (GitHub Pages)
  assets/       # imagens do guia
docs/
  guia-explicativo.md  # tira-dêÂºvidas educativo: analogias, FAQ, passo a passo
```

## InstalaêÂ§êÂ£o

```bash
git clone https://github.com/inematds/maestro-roteador.git
ln -sfn "$(pwd)/maestro-roteador/skills/maestro-roteador" ~/.claude/skills/maestro-roteador
```

Ou copie a pasta `skills/maestro-roteador/` para `~/.claude/skills/` (skills de usuêÂ¡rio) ou `.claude/skills/` de um projeto.

## Uso

PeêÂ§a a triagem diretamente ê¢â¬â "faz a triagem disso", "qual modelo e esforêÂ§o pra isso?" ê¢â¬â ou apenas peêÂ§a o trabalho: ao despachar subagentes/workflows, o agente aplica a matriz e distribui cada parte com `model` e `effort` adequados.

## Changelog

- **v1.3.3** ê¢â¬â Fable 5.2: Opus 5.5 medium como ponto de partida, escada lowê¢â âhigh sêÂ³ no que falhar, dividir sêÂ³ nos 2 casos medidos, ajudantes com `model`/`effort` fixos, Explore no Haiku, Fable como revisor (base: documento oficial da Anthropic + mediêÂ§êÂµes do [fable-barato](https://github.com/inematds/fable-barato)); campo `dividir` no plano de despacho.
- **v1.2.3** ê¢â¬â versêÂµes em inglêÂªs da skill (SKILL.en.md) e do README (README.en.md).
- **v1.2.2** ê¢â¬â armadilha do overthinking explicitada (excesso do prêÂ³prio eixo esforêÂ§o; o esforêÂ§o certo êÂ© o menor que cobre o risco) + docs/guia-explicativo.md (tira-dêÂºvidas educativo com analogias, FAQ e passo a passo).
- **v1.2.1** ê¢â¬â escada com evidêÂªncia (erro barato comeêÂ§a em low e sobe sêÂ³ com evidêÂªncia), 2 armadilhas novas (estêÂ©tica nêÂ£o êÂ© esforêÂ§o; overthinking piora), seêÂ§êÂ£o de cache (troca sêÂ³ em fronteira, subagente grêÂ¡tis em cache, sem keepalive); guia ganha hero com imagem + seêÂ§êÂµes Cache e Harness; README educativo completo.
- **v1.1.1** ê¢â¬â guia/index.html (landing+guia padrêÂ£o INEMA) + regra anti-overhead na skill.
- **v1.1.0** ê¢â¬â empate de gosto oferece escolha custo (sonnet+low) êâ qualidade (fable+low).
- **v1.0.0** ê¢â¬â skill maestro-roteador: triagem de modelo e esforêÂ§o (matriz de dois eixos).

## LicenêÂ§a

MIT ê¢â¬â projeto de pesquisa/educaêÂ§êÂ£o INEMA.

---

<div align="center">
  <img src="capa/logo.svg" alt="Arckhé AI" height="32" />
  <p><strong>Arckhé AI</strong></p>
</div>