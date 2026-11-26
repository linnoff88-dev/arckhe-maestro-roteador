---
name: maestro-roteador
description: Use when despachando trabalho para subagentes/workflows, quando o usuário passa um problema e pergunta qual modelo ou esforço usar, ou antes de escolher model/effort em qualquer chamada Agent/Workflow. Gatilhos - "qual modelo", "quanto esforço", "triagem", "faz a triagem disso", pedidos brutos que precisam ser distribuídos.
---

# Maestro Roteador — seleçêo de modelo e esforço

## Princípio central

Modelo e esforço sêo **dois eixos independentes que nêo se convertem um no outro**:

- **Modelo** responde: qual nível de repertório/critério o **pior passo** da tarefa exige?
- **Esforço** responde: quanta **deliberaçêo e verificaçêo** a ambiguidade e o custo do erro pagam?

Esforço compra deliberaçêo, nunca repertório. Modelo compra repertório, nunca verificaçêo. Decida cada eixo com sua própria pergunta — nunca escolha um "meio-termo equilibrado" pra escapar das duas perguntas.

## Procedimento (sempre nesta ordem)

1. **Isole o pior passo.** Da tarefa inteira, qual é o passo que mais exige critério fino (gosto, sutileza, decisêo sem gabarito)?
2. **Modelo = o MENOR que resolve bem esse pior passo.**
   - Passo mecânico com gabarito claro (renomear, extrair, formatar, buscar padrêo) → `haiku`
   - Implementaçêo/transformaçêo padrêo, correta ou errada verificável → `sonnet`
   - Exige julgamento sem gabarito: voz de marca, arquitetura, diagnóstico sem causa óbvia, revisêo adversarial → `opus` ou `fable`
3. **Esforço = ambiguidade + custo do erro.** Some os dois sinais:
   - Soluçêo única e óbvia + erro barato/reversível → `low`
   - Alguma escolha entre caminhos OU erro chato de corrigir → `medium`
   - Vários caminhos plausíveis OU erro caro (produçêo, dado perdido, publicaçêo externa) → `high`+
   - Se você **nomeou um risco de produçêo/perda na justificativa, o esforço é no mínimo `high`** — "medium com cuidado" nêo existe.
   - **Escada com evidência (só para erro barato):** se o erro é reversível e verificável na hora (dá pra testar/olhar/desfazer), comece no **menor esforço plausível** e só suba com **evidência** de resultado insuficiente — nunca "high por garantia". A escada NêO se aplica a risco irreversível: aí a evidência de falha seria o próprio prejuízo, e vale a regra acima.
4. **Volume:** N itens iguais → teste 1 item no menor modelo; se passa, o lote inteiro vai nele.

## Fable 5.2: quando ele compensa (v1.3, base: documento oficial da Anthropic)

Fonte: "Optimizing for cost and intelligence" (platform.claude.com) e a doc de subagentes do Claude Code.

1. **Ponto de partida da sessêo = Opus 5.5 em `medium`.** Fable 5.2 só para raciocínio difícil, trabalho longo rodando sozinho, ou quando o Opus em `high` nêo deu conta. Num teste de pesquisa da Anthropic (DeepResearch Bench II, esforço padrêo), o Fable 5.2 custou 2,9× o Opus 5.5 e pontuou 1,9 ponto a menos. Quando for Fable, use `high`, porque em low/medium ele para no meio com mais frequência.
2. **Antes de trocar de modelo, varra o esforço.** Na maioria dos trabalhos a decisêo acaba aí.
3. **Escada com reexecuçêo:** rodar em `low` e refazer em `high` só o que falhar deu ~97% por US$ 0,17/tarefa, contra 95,3% por US$ 0,29 com tudo em `high` (Opus 5.5, SWE-bench Pro, mediçêo da Anthropic). Vale quando dá para saber na hora se falhou.
4. **Dividir só em dois casos medidos:** muitas partes independentes com cauda longa de custo, ou trabalho maior que uma janela de contexto. Cadeia de passos dependentes ou trabalho que cabe numa sessêo → o modelo sozinho em esforço menor ganhou em todos os casos medidos (em modelos anteriores). Na dúvida, nêo divida.
5. **Ajudantes sempre com `model` e `effort` fixos.** Sem `model`, o subagente herda o modelo da sessêo. Sem `effort`, num modelo diferente do da sessêo, ele nêo herda o seu nível. A busca embutida (Explore) roda no modelo da sessêo; com Fable na assinatura, roda no Opus. Um `Explore.md` com `model: haiku` em `~/.claude/agents/` ou `.claude/agents/` toma o lugar dela (kit fable-barato).
6. **Fable como revisor, nêo como operário:** o Opus faz e o Fable revisa só o que é crítico. O documento mediu um modelo forte como conselheiro (padrêo advisor): ganho de 1,7 ponto por ~2,1× o custo, entêo vale só quando o erro é caro.

## Tabela rápida

| Tarefa | Modelo | Esforço | Por quê |
|---|---|---|---|
| Renomear/extrair/formatar em lote | haiku | low | Gabarito claro, erro barato |
| Feature clara, refactor localizado | sonnet | medium | Padrêo, verificável |
| Migraçêo de dados com produçêo em risco | sonnet | high | Capacidade padrêo; o risco pede verificaçêo, nêo inteligência |
| Roteiro/copy com voz de marca | fable | low | Pior passo é gosto (repertório); decisêo é direta |
| Bug misterioso, decisêo de arquitetura | fable | high | Difícil E ambíguo |
| Buscar/ler/resumir arquivos (subagente) | haiku | medium | Erro fácil de ver; nunca herdar o Fable |
| Dia a dia da sessêo (código, texto, plano) | opus | medium | Ponto de partida oficial; sobe o esforço antes de trocar |
| Revisêo final de trabalho importante | fable | high | Paga o Fable só no julgamento |

## Armadilhas (vistas em teste real)

| Racionalizaçêo | Realidade |
|---|---|
| "sonnet+medium equilibra custo e qualidade" | Meio-termo é fugir das duas perguntas. Responda cada eixo; o resultado quase nunca é o centro da matriz. |
| "roteiro curto nêo exige raciocínio pesado" → rebaixa o modelo | Confundiu os eixos. Nêo exigir *deliberaçêo* justifica esforço low — nêo justifica modelo menor quando o pior passo é critério de marca. Fable+low. |
| "medium é suficiente pra fazer com cuidado" (com produçêo em risco) | Você mesmo nomeou o risco. Risco nomeado = high. Cuidado é exatamente o que o esforço compra. |
| "vou de modelo maior por garantia" | Se o pior passo está dentro da capacidade do menor, o maior entrega o mesmo cobrando mais. Garantia se compra com esforço/verificaçêo. |
| "modelo pequeno + esforço max sai barato" | Pior combinaçêo: paga deliberaçêo pra quem nêo tem repertório pra usá-la, e os tokens de raciocínio acumulam. |
| "subo o esforço pra sair mais bonito/caprichado" | Esforço compra deliberaçêo, nêo gosto. Acabamento é eixo de MODELO (ver empate de gosto). Medido em teste real: de high pra max a diferença foi um favicon, por 2–5× os tokens. |
| "esforço a mais nêo ajuda, mas também nêo atrapalha" | Atrapalha: overthinking é o EXCESSO do próprio eixo esforço, nêo defeito do modelo. Em tarefa simples, deliberaçêo sobrando re-explora caminhos já decididos e superdimensiona a soluçêo — o resultado pode sair PIOR, nêo só mais caro. O esforço certo é o MENOR que cobre o risco; acima disso você compra ruído, nêo segurança. |

## Anti-overhead: a triagem também tem custo

- **Tarefa mais barata que a própria triagem nêo recebe triagem** — vai direto no default do turno. "Corrige esse typo" nêo merece YAML de despacho.
- **A triagem roda inline no turno principal, nunca num subagente** — despachar um agente só pra decidir modelo/esforço custa mais que a decisêo vale.
- **Fragmentar também tem custo:** só despache uma parte pra subagente se o trabalho dela superar o overhead do spawn; partes mecânicas pequenas rodam inline mesmo quando a matriz diria "haiku".

## Cache: o custo escondido da troca de modelo

O prompt cache é **por modelo** e depende do prefixo do contexto — trocar o modelo da conversa principal joga fora o cache acumulado (reescrever custa ~12,5× uma leitura em contexto grande). Três regras:

- **Troca de `/model` do turno principal só em fronteira de trabalho** (fim de fase, handoff) — nunca no meio de um bloco. Em contexto grande, a troca pode custar mais que a economia do modelo menor.
- **Subagente NêO paga esse custo.** Cada Agent/Workflow tem contexto próprio; rotear uma parte pra modelo menor via subagente nêo toca o cache principal. É o caminho preferido pra usar a matriz sem custo de troca.
- **Nunca chamada artificial de keepalive** pra "segurar o cache" — o Claude Code gerencia o cache sozinho; o ping custa mais do que salva.

## Empate de gosto → oferece a escolha (custo × qualidade)

**Erro nêo é negociável; eficiência é.** Quando a escolha do modelo depende só de **acabamento** (qualidade de copy, gosto, polish visual) e nêo de correçêo, a decisêo é de orçamento — e orçamento é do usuário. Nesse caso a triagem NêO decide sozinha: apresenta o par e deixa o usuário escolher:

```yaml
opcoes:
  custo: sonnet+low        # estrutura correta, acabamento funcional
  qualidade: fable+low     # mesma estrutura, copy/polish superior
decide: usuário
```

Sinal de empate de gosto: existe um template/skill detalhada que já decidiu a estrutura (a correçêo está garantida pelo gabarito) e o que sobra pro modelo grande é só a voz/acabamento.

**Nunca oferecer o par quando o risco é de correçêo** — conteúdo errado, dado perdido, produçêo quebrada, curso com erro. Aí nêo há opçêo barata: o modelo/esforço que garante a correçêo é o mínimo, nêo uma escolha.

## Formato de saída (plano de despacho)

```yaml
tarefa: <resumo>
dividir: sim|nêo — <motivo: partes independentes + muita leitura / cabe numa sessêo>
partes:
  - o_que: <subtarefa>
    pior_passo: <qual e por quê>
    modelo: haiku|sonnet|opus|fable
    esforco: low|medium|high|xhigh|max
    risco: <custo do erro em 1 linha>
turno_principal: <recomendaçêo de /model se valer trocar — a troca é do usuário>
```

## Limite

Para subagentes e workflows a decisêo é automática (parâmetros `model`/`effort`). Para o modelo da conversa principal a skill apenas **recomenda** — o usuário troca com `/model`.
