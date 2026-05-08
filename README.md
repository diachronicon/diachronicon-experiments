# Diachronicon Experiments

This repository collects all experiments we run in the Diachronicon
project — our effort to automate diachronic markup of Russian
idiomatic constructions with the help of large language models. We
use it as a transparent navigator through everything we do: prompts,
model-generated reports, and the manually annotated reference data. The
repository is read-only by design, no analytics live here. We publish
the analysis in a series of papers based on these materials.

## The data: Diachronicon

[Diachronicon](https://diachronicon.ru) is a database of historical
Russian idiomatic constructions. It records each construction across
the 18th–21st centuries and breaks its history into stages — discrete
states of the construction, each backed by examples from the
Russian National Corpus. Diachronicon currently holds about 170
constructions, some of which are still in draft.

[The Diachronicon dataset](dataset/)

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

### Series 1: initial run with the MCP server

The first series of experiments tested the basic setup: the MCP server connected to the
official claude.ai web chat, a single prompt, and three Claude
configurations: Claude Sonnet 4.5, Claude Sonnet 4.5 with extended thinking, and Claude Opus
4.5 with extended thinking. We picked 15 constructions from
Diachronicon to see how the models handle autonomous corpus navigation
across a range of difficulty.

The prompt sets up the model as a diachronic linguist. It asks for
several searches across the corpus, a careful look through the result
pages, an analysis that covers both syntactic and semantic changes, and a
write-up of every stage: formula, level, type, subtype, and the first
and last RNC attestations. Strict constraints forbid making up examples
or dates and require the model to admit uncertainty.

[Prompt](experiments/series1/prompt.md)

| Construction | Sonnet 4.5 | Sonnet 4.5 ET | Opus 4.5 ET |
| --- | --- | --- | --- |
| *пруд пруди* | [report](experiments/series1/reports/prud-prudi__claude-sonnet-4-5.md) | [report](experiments/series1/reports/prud-prudi__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/prud-prudi__claude-opus-4-5-et.md) |
| *рука не поднимается* | [report](experiments/series1/reports/ruka-ne-podnimaetsya__claude-sonnet-4-5.md) | [report](experiments/series1/reports/ruka-ne-podnimaetsya__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/ruka-ne-podnimaetsya__claude-opus-4-5-et.md) |
| *конь не валялся* | [report](experiments/series1/reports/kon-ne-valyalsya__claude-sonnet-4-5.md) | [report](experiments/series1/reports/kon-ne-valyalsya__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/kon-ne-valyalsya__claude-opus-4-5-et.md) |
| *в ус не дуть* | [report](experiments/series1/reports/v-us-ne-dut__claude-sonnet-4-5.md) | [report](experiments/series1/reports/v-us-ne-dut__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/v-us-ne-dut__claude-opus-4-5-et.md) |
| *чуть свет* | [report](experiments/series1/reports/chut-svet__claude-sonnet-4-5.md) | [report](experiments/series1/reports/chut-svet__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/chut-svet__claude-opus-4-5-et.md) |
| *суть да дело* | [report](experiments/series1/reports/sut-da-delo__claude-sonnet-4-5.md) | [report](experiments/series1/reports/sut-da-delo__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/sut-da-delo__claude-opus-4-5-et.md) |
| *большая половина* | [report](experiments/series1/reports/bolshaya-polovina__claude-sonnet-4-5.md) | [report](experiments/series1/reports/bolshaya-polovina__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/bolshaya-polovina__claude-opus-4-5-et.md) |
| *в точку* | [report](experiments/series1/reports/v-tochku__claude-sonnet-4-5.md) | [report](experiments/series1/reports/v-tochku__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/v-tochku__claude-opus-4-5-et.md) |
| *не дай бог* | [report](experiments/series1/reports/ne-day-bog__claude-sonnet-4-5.md) | [report](experiments/series1/reports/ne-day-bog__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/ne-day-bog__claude-opus-4-5-et.md) |
| *до лампочки* | [report](experiments/series1/reports/do-lampochki__claude-sonnet-4-5.md) | [report](experiments/series1/reports/do-lampochki__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/do-lampochki__claude-opus-4-5-et.md) |
| *тютелька в тютельку* | [report](experiments/series1/reports/tyutelka-v-tyutelku__claude-sonnet-4-5.md) | [report](experiments/series1/reports/tyutelka-v-tyutelku__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/tyutelka-v-tyutelku__claude-opus-4-5-et.md) |
| *ни к селу ни к городу* | [report](experiments/series1/reports/ni-k-selu-ni-k-gorodu__claude-sonnet-4-5.md) | [report](experiments/series1/reports/ni-k-selu-ni-k-gorodu__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/ni-k-selu-ni-k-gorodu__claude-opus-4-5-et.md) |
| *от корки до корки* | [report](experiments/series1/reports/ot-korki-do-korki__claude-sonnet-4-5.md) | [report](experiments/series1/reports/ot-korki-do-korki__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/ot-korki-do-korki__claude-opus-4-5-et.md) |
| *в долгий ящик* | [report](experiments/series1/reports/v-dolgiy-yaschik__claude-sonnet-4-5.md) | [report](experiments/series1/reports/v-dolgiy-yaschik__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/v-dolgiy-yaschik__claude-opus-4-5-et.md) |
| *на ночь глядя* | [report](experiments/series1/reports/na-noch-glyadya__claude-sonnet-4-5.md) | [report](experiments/series1/reports/na-noch-glyadya__claude-sonnet-4-5-et.md) | [report](experiments/series1/reports/na-noch-glyadya__claude-opus-4-5-et.md) |

### Series 2: feeding the model the full concordance

Reading the series 1 reports, we noticed that the models, even with RNC
access, only sample the first few pages of the corpus output and at best
peek at the last one. They draw conclusions too fast and miss stages
because they never see the whole picture. We stepped back to try handing
the model the entire concordance up front.

In series 2 we ran the search on the RNC ourselves, downloaded the full
output, kept only the columns we cared about (title, full context, year),
and pasted it into the chat together with the prompt. The MCP server was
not used here. We tested two constructions — *в точку* and *пруд пруди* —
across more models: Claude Opus 4.6 with extended thinking, Claude
Sonnet 4.6 with extended thinking, Gemini 3 Flash, Gemini 3.1 Pro, GLM-5,
Kimi-K2.5, and GPT-5.4. We ran the Claude models in the official chat
and the rest in our self-hosted LibreChat instance.

The prompt is almost identical to series 1. We excluded the page-navigation
instructions (the model now receives the whole concordance in one go) and
added a small note that lets the model return several ordering variants
when uncertain instead of asking for more data.

[Prompt](experiments/series2/prompt.md)

| Construction | Claude Opus 4.6 ET | Claude Sonnet 4.6 ET | Gemini Flash 3 | Gemini Pro 3.1 | GLM 5 | Kimi K2.5 | GPT 5.4 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| *в точку* | [report](experiments/series2/reports/v-tochku__claude-opus-4-6-et.md) | [report](experiments/series2/reports/v-tochku__claude-sonnet-4-6-et.md) | [report](experiments/series2/reports/v-tochku__gemini-flash-3.md) | — | [report](experiments/series2/reports/v-tochku__glm-5.md) | [report](experiments/series2/reports/v-tochku__kimi-k2-5.md) | [report](experiments/series2/reports/v-tochku__gpt-5-4.md) |
| *пруд пруди* | [report](experiments/series2/reports/prud-prudi__claude-opus-4-6-et.md) | [report](experiments/series2/reports/prud-prudi__claude-sonnet-4-6-et.md) | — | [report](experiments/series2/reports/prud-prudi__gemini-pro-3-1.md) | [report](experiments/series2/reports/prud-prudi__glm-5.md) | [report](experiments/series2/reports/prud-prudi__kimi-k2-5.md) | [report](experiments/series2/reports/prud-prudi__gpt-5-4.md) |

### Series 3: back to the MCP, with batched corpus output

Series 2 experiments confirmed that the bottleneck was the model's inability to walk
through many pages of the RNC by itself. Rather than keep handing it the
data manually, we extended the MCP server: we added a `max_examples`
parameter that lets a single tool call return many examples at once and
hides pagination from the model. With that fix in place we returned to the
agent-driven setup and ran 10 constructions through Claude Opus 4.6 on
the improved server.

The prompt for this round is essentially the series 2 prompt with the
page-navigation guidance updated for the new `max_examples` workflow.

[Prompt](#)

| Construction | Claude Opus 4.6 |
| --- | --- |
| *в точку* | [report](experiments/series3/reports/v-tochku__claude-opus-4-6.md) |
| *в ус не дуть* | [report](experiments/series3/reports/v-us-ne-dut__claude-opus-4-6.md) |
| *до лампочки* | [report](experiments/series3/reports/do-lampochki__claude-opus-4-6.md) |
| *конь не валялся* | [report](experiments/series3/reports/kon-ne-valyalsya__claude-opus-4-6.md) |
| *на ночь глядя* | [report](experiments/series3/reports/na-noch-glyadya__claude-opus-4-6.md) |
| *не дай бог* | [report](experiments/series3/reports/ne-day-bog__claude-opus-4-6.md) |
| *пруд пруди* | [report](experiments/series3/reports/prud-prudi__claude-opus-4-6.md) |
| *рука не поднимается* | [report](experiments/series3/reports/ruka-ne-podnimaetsya__claude-opus-4-6.md) |
| *суд да дело* | [report](experiments/series3/reports/sut-da-delo__claude-opus-4-6.md) |
| *чуть свет* | [report](experiments/series3/reports/chut-svet__claude-opus-4-6.md) |

### Series 4: a stricter prompt

When we analyzed the series 3 reports, we found a recurring set of linguistic
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

We ran this stricter prompt on five constructions with Claude Opus 4.6.

[Prompt](experiments/series4/prompt.md)

| Construction | Claude Opus 4.6 |
| --- | --- |
| *до лампочки* | [report](experiments/series4/reports/do-lampochki__claude-opus-4-6.md) |
| *конь не валялся* | [report](experiments/series4/reports/kon-ne-valyalsya__claude-opus-4-6.md) |
| *не дай бог* | [report](experiments/series4/reports/ne-day-bog__claude-opus-4-6.md) |
| *суд да дело* | [report](experiments/series4/reports/sut-da-delo__claude-opus-4-6.md) |
| *чуть свет* | [report](experiments/series4/reports/chut-svet__claude-opus-4-6.md) |
