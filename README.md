# Diachronicon Experiments

This repository collects the experiments from the second stage of the
Diachronicon project — our effort to automate the diachronic markup of
Russian idiomatic constructions with the help of large language models.
It continues our
[initial experiments](https://github.com/diachronicon/diachronicon-first-experiments).
Those showed that a model connected to the Russian National Corpus can
reconstruct the history of a construction, but tends to sample the corpus
too shallowly and to invent stages that are not there. The work collected
here is aimed at those limitations. We use the repository as a transparent
navigator through everything we do: prompts, model-generated reports, and
the manually annotated reference data. It is read-only by design, no
analytics live here. We publish the analysis in a series of papers based
on these materials.

## The data: Diachronicon

[Diachronicon](https://diachronicon.ru) is a database of historical
Russian idiomatic constructions. It records each construction across
the 18th–21st centuries and breaks its history into stages: discrete
states of the construction, each backed by examples from the
Russian National Corpus. Diachronicon currently holds about 170
constructions, some of which are still in draft.

Alongside these experiments we release our own manual annotation of the
ten new constructions that serve as the gold standard for the final
experiment below.

[The dataset](dataset/)

The annotation draws on a fixed inventory of change types and subtypes.
The full list, with a corpus example and a comment for each, is in
[types and subtypes of change](dataset/types-and-subtypes.md).

## Chat experiments

Every experiment below ran inside a regular chat with a model. We
connected the [RNC MCP server](https://github.com/futyn-maker/rncmcp) we
developed for the
[Russian National Corpus public API](https://ruscorpora.github.io/public-api/),
pasted the prompt that contains the task, the tag inventories, the formula
glosses, and the report template, and let the model generate a report for
one construction at a time. We then saved those reports and analyzed them
by hand. Where a particular series departed from this pattern, for
instance, by feeding the model a pre-extracted concordance instead of
letting it call the MCP, we say so explicitly in that series'
description.

### Manual context: feeding the model the full concordance

Reading the reports from the initial experiments, we noticed that the
models, even with RNC access, only sample the first few pages of the
corpus output and at best peek at the last one. They draw conclusions too
fast and miss stages because they never see the whole picture. We stepped
back to try handing the model the entire concordance up front.

Here we ran the search on the RNC ourselves, downloaded the full
output, kept only the columns we cared about (title, full context, year),
and pasted it into the chat together with the prompt. The MCP server was
not used here. We tested two constructions — _в точку_ and _пруд пруди_ —
across more models: Claude Opus 4.6 with extended thinking, Claude
Sonnet 4.6 with extended thinking, Gemini 3.1 Pro, Kimi K2.5, and
GPT-5.4. We ran the Claude models in the official chat and the rest in
our self-hosted LibreChat instance.

The prompt is almost identical to the one from the initial experiments.
We excluded the page-navigation instructions (the model now receives the
whole concordance in one go) and added a small note that lets the model
return several ordering variants when uncertain instead of asking for
more data.

[Prompt](experiments/manual-context/prompt.md)

| Construction | Claude Opus 4.6 ET                                                             | Claude Sonnet 4.6 ET                                                             | Gemini Pro 3.1                                                             | Kimi K2.5                                                             | GPT 5.4                                                             |
| ------------ | ------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------- |
| _в точку_    | [report](experiments/manual-context/reports/v-tochku__claude-opus-4-6-et.md)   | [report](experiments/manual-context/reports/v-tochku__claude-sonnet-4-6-et.md)   | [report](experiments/manual-context/reports/v-tochku__gemini-pro-3-1.md)   | [report](experiments/manual-context/reports/v-tochku__kimi-k2-5.md)   | [report](experiments/manual-context/reports/v-tochku__gpt-5-4.md)   |
| _пруд пруди_ | [report](experiments/manual-context/reports/prud-prudi__claude-opus-4-6-et.md) | [report](experiments/manual-context/reports/prud-prudi__claude-sonnet-4-6-et.md) | [report](experiments/manual-context/reports/prud-prudi__gemini-pro-3-1.md) | [report](experiments/manual-context/reports/prud-prudi__kimi-k2-5.md) | [report](experiments/manual-context/reports/prud-prudi__gpt-5-4.md) |

### Batched retrieval: back to the MCP server

These experiments confirmed that the bottleneck was the model's inability to walk
through many pages of the RNC by itself. Rather than keep handing it the
data manually, we extended the MCP server: we added a `max_examples`
parameter that lets a single tool call return many examples at once and
hides pagination from the model. With that fix in place we returned to the
agent-driven setup and ran 10 constructions through Claude Opus 4.6 on
the improved server.

The prompt for this round is essentially the manual-context prompt with the
page-navigation guidance updated for the new `max_examples` workflow.

[Prompt](#)

| Construction          | Claude Opus 4.6                                                                          |
| --------------------- | ---------------------------------------------------------------------------------------- |
| _в точку_             | [report](experiments/batched-retrieval/reports/v-tochku__claude-opus-4-6.md)             |
| _в ус не дуть_        | [report](experiments/batched-retrieval/reports/v-us-ne-dut__claude-opus-4-6.md)          |
| _до лампочки_         | [report](experiments/batched-retrieval/reports/do-lampochki__claude-opus-4-6.md)         |
| _конь не валялся_     | [report](experiments/batched-retrieval/reports/kon-ne-valyalsya__claude-opus-4-6.md)     |
| _на ночь глядя_       | [report](experiments/batched-retrieval/reports/na-noch-glyadya__claude-opus-4-6.md)      |
| _не дай бог_          | [report](experiments/batched-retrieval/reports/ne-day-bog__claude-opus-4-6.md)           |
| _пруд пруди_          | [report](experiments/batched-retrieval/reports/prud-prudi__claude-opus-4-6.md)           |
| _рука не поднимается_ | [report](experiments/batched-retrieval/reports/ruka-ne-podnimaetsya__claude-opus-4-6.md) |
| _суд да дело_         | [report](experiments/batched-retrieval/reports/sut-da-delo__claude-opus-4-6.md)          |
| _чуть свет_           | [report](experiments/batched-retrieval/reports/chut-svet__claude-opus-4-6.md)            |

### Stricter prompt: fixing the linguistic errors

When we analyzed the batched-retrieval reports, we found a recurring set of linguistic
problems: the models split a single change into several stages, treat
occasional or punning uses as evidence, mark trivial negations as new
stages, and sometimes mix syntactic and semantic shifts inside one entry.
We rewrote the prompt to address each of those failure modes directly.

The new prompt caps the number of corpus queries (one to three on the
main pass, sampled across pages) and allows follow-up queries only with
an explicit goal. It tells the model to check for homonymy before doing
anything else. Rare uses (fewer than three corpus examples), wordplay,
and plain negations move into a separate "special cases" section so they
no longer inflate the main list. Each entry must describe exactly one
change; if a syntactic shift co-occurs with a semantic one, they become
two stages, not one. The model also has to comment on its choice of
first and last attestation. The rest of the report structure is the same
as before.

This is the final and main experiment. We ran the stricter prompt over
ten new constructions that we annotated ourselves and that had not
appeared in any earlier round, across the full set of models: Claude Opus
4.6, GPT 5.4, Gemini 3.1 Pro, DeepSeek V4 Pro, and Kimi K2.5. These ten
constructions are the gold-standard set released in [the dataset](dataset/).

[Prompt](experiments/stricter-prompt/prompt.md)

| Construction                  | Claude Opus 4.6                                                                              | GPT 5.4                                                                              | Gemini Pro 3.1                                                                              | DeepSeek V4 Pro                                                                              | Kimi K2.5                                                                              |
| ----------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| _руки не доходят_             | [report](experiments/stricter-prompt/reports/ruki-ne-dohodyat__claude-opus-4-6.md)           | [report](experiments/stricter-prompt/reports/ruki-ne-dohodyat__gpt-5-4.md)           | [report](experiments/stricter-prompt/reports/ruki-ne-dohodyat__gemini-pro-3-1.md)           | [report](experiments/stricter-prompt/reports/ruki-ne-dohodyat__deepseek-v4-pro.md)           | [report](experiments/stricter-prompt/reports/ruki-ne-dohodyat__kimi-k2-5.md)           |
| _ни гугу_                     | [report](experiments/stricter-prompt/reports/ni-gugu__claude-opus-4-6.md)                    | [report](experiments/stricter-prompt/reports/ni-gugu__gpt-5-4.md)                    | [report](experiments/stricter-prompt/reports/ni-gugu__gemini-pro-3-1.md)                    | [report](experiments/stricter-prompt/reports/ni-gugu__deepseek-v4-pro.md)                    | [report](experiments/stricter-prompt/reports/ni-gugu__kimi-k2-5.md)                    |
| _эпизодически_                | [report](experiments/stricter-prompt/reports/epizodicheski__claude-opus-4-6.md)              | [report](experiments/stricter-prompt/reports/epizodicheski__gpt-5-4.md)              | [report](experiments/stricter-prompt/reports/epizodicheski__gemini-pro-3-1.md)              | [report](experiments/stricter-prompt/reports/epizodicheski__deepseek-v4-pro.md)              | [report](experiments/stricter-prompt/reports/epizodicheski__kimi-k2-5.md)              |
| _от зари до зари_             | [report](experiments/stricter-prompt/reports/ot-zari-do-zari__claude-opus-4-6.md)            | [report](experiments/stricter-prompt/reports/ot-zari-do-zari__gpt-5-4.md)            | [report](experiments/stricter-prompt/reports/ot-zari-do-zari__gemini-pro-3-1.md)            | [report](experiments/stricter-prompt/reports/ot-zari-do-zari__deepseek-v4-pro.md)            | [report](experiments/stricter-prompt/reports/ot-zari-do-zari__kimi-k2-5.md)            |
| _ни стыда ни совести_         | [report](experiments/stricter-prompt/reports/ni-styda-ni-sovesti__claude-opus-4-6.md)        | [report](experiments/stricter-prompt/reports/ni-styda-ni-sovesti__gpt-5-4.md)        | [report](experiments/stricter-prompt/reports/ni-styda-ni-sovesti__gemini-pro-3-1.md)        | [report](experiments/stricter-prompt/reports/ni-styda-ni-sovesti__deepseek-v4-pro.md)        | [report](experiments/stricter-prompt/reports/ni-styda-ni-sovesti__kimi-k2-5.md)        |
| _битый час_                   | [report](experiments/stricter-prompt/reports/bityi-chas__claude-opus-4-6.md)                 | [report](experiments/stricter-prompt/reports/bityi-chas__gpt-5-4.md)                 | [report](experiments/stricter-prompt/reports/bityi-chas__gemini-pro-3-1.md)                 | [report](experiments/stricter-prompt/reports/bityi-chas__deepseek-v4-pro.md)                 | [report](experiments/stricter-prompt/reports/bityi-chas__kimi-k2-5.md)                 |
| _не брать в голову_           | [report](experiments/stricter-prompt/reports/ne-brat-v-golovu__claude-opus-4-6.md)           | [report](experiments/stricter-prompt/reports/ne-brat-v-golovu__gpt-5-4.md)           | [report](experiments/stricter-prompt/reports/ne-brat-v-golovu__gemini-pro-3-1.md)           | [report](experiments/stricter-prompt/reports/ne-brat-v-golovu__deepseek-v4-pro.md)           | [report](experiments/stricter-prompt/reports/ne-brat-v-golovu__kimi-k2-5.md)           |
| _не лезть в карман за словом_ | [report](experiments/stricter-prompt/reports/ne-lezt-v-karman-za-slovom__claude-opus-4-6.md) | [report](experiments/stricter-prompt/reports/ne-lezt-v-karman-za-slovom__gpt-5-4.md) | [report](experiments/stricter-prompt/reports/ne-lezt-v-karman-za-slovom__gemini-pro-3-1.md) | [report](experiments/stricter-prompt/reports/ne-lezt-v-karman-za-slovom__deepseek-v4-pro.md) | [report](experiments/stricter-prompt/reports/ne-lezt-v-karman-za-slovom__kimi-k2-5.md) |
| _через пень колоду_           | [report](experiments/stricter-prompt/reports/cherez-pen-kolodu__claude-opus-4-6.md)          | [report](experiments/stricter-prompt/reports/cherez-pen-kolodu__gpt-5-4.md)          | [report](experiments/stricter-prompt/reports/cherez-pen-kolodu__gemini-pro-3-1.md)          | [report](experiments/stricter-prompt/reports/cherez-pen-kolodu__deepseek-v4-pro.md)          | [report](experiments/stricter-prompt/reports/cherez-pen-kolodu__kimi-k2-5.md)          |
| _место под солнцем_           | [report](experiments/stricter-prompt/reports/mesto-pod-solntsem__claude-opus-4-6.md)         | [report](experiments/stricter-prompt/reports/mesto-pod-solntsem__gpt-5-4.md)         | [report](experiments/stricter-prompt/reports/mesto-pod-solntsem__gemini-pro-3-1.md)         | [report](experiments/stricter-prompt/reports/mesto-pod-solntsem__deepseek-v4-pro.md)         | [report](experiments/stricter-prompt/reports/mesto-pod-solntsem__kimi-k2-5.md)         |
