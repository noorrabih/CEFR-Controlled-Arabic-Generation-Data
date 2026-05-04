# CEFR-Controlled Arabic Generation Data

This repository accompanies the paper **["Can LLMs Control Readability? A Multi-Dimensional Evaluation Framework for CEFR-Controlled Arabic Generation"](#citation)** (Rabih, Qwaider, and Briscoe). It releases the CEFR-aligned prompts, lexical constraint lists, and generated essays used in the study, so that other researchers can reproduce the analysis or build on it for adaptive Arabic learning systems.

## What's in this repo

The release has two parts: **prompt + vocabulary resources** (used to drive controlled generation) and **generated essays** (the outputs evaluated in the paper).

```
.
├── prompts_with_3cefr_levels_and_vocab.csv   # 161 ARWI prompts at 3 broad bands (A / B / C)
├── prompts_with_5cefr_levels_and_vocab.csv   # 272 ARWI prompts at 5 CEFR levels (A / B1 / B2 / C1 / C2)
├── zaebuc_prompts_vocab.csv                  # 9 ZAEBUC-derived prompts with vocab
├── generated_essays_6levels_p3.tsv           # 322 essays generated under prompt P3 (syntactic profile)
├── generated_essays_5levels_p5.tsv           # 272 essays generated under prompt P5 (vocab + syntactic)
└── README.md
```

## Prompt files

All three prompt CSVs share a core schema and differ in CEFR granularity. Each row is one essay topic paired with a CEFR-aligned Arabic vocabulary list that can be injected into the generation prompt as a lexical constraint.

| Column | Description |
|---|---|
| `Level` | Coarse band: `Beginner` / `Intermediate` / `Advanced` (from the ARWI dataset). |
| `Arabic Text` | The essay topic in Arabic. |
| `Translated Text` | English translation of the topic. |
| `Topic_ID` / `topic_id` | Topic identifier. |
| `prompt_id` | Unique prompt identifier (topic × CEFR level). |
| `CEFR_level` | Target CEFR level. Granularity varies by file (see below). |
| `level` | Numeric encoding of the CEFR level. |
| `words` | List of `(lemma, POS, gloss)` triples — the SAMER-validated, CEFR-aligned vocabulary suggested for this prompt. |

**File-specific notes:**

- `prompts_with_3cefr_levels_and_vocab.csv` — 161 prompts at three broad bands (`A`, `B`, `C`). Distribution: A=50, B=56, C=55. Useful when running the coarse-grained P1 baseline from the paper.
- `prompts_with_5cefr_levels_and_vocab.csv` — 272 prompts at five CEFR levels (`A`, `B1`, `B2`, `C1`, `C2`). Distribution: A=50, B1=56, B2=56, C1=55, C2=55. The five-level granularity matches the SAMER lexicon's bands, which is why A1 and A2 are merged into a single `A`.
- `zaebuc_prompts_vocab.csv` — 9 prompts derived from ZAEBUC topics, covering A2 / B1 / B2 / C1.

The vocabulary lists in `words` were built using the pipeline described in §4.4 of the paper: GPT-4o was prompted for candidate vocabulary, the candidates were filtered against the SAMER readability lexicon, and a Sentence-BERT relevance fallback was used when the filtered list was too short.

## Generated-essay files

Both essay TSVs share the same schema:

| Column | Description |
|---|---|
| `Document_ID` | Format `{topic_id}_{CEFR_level}_{n_levels}levels_p{prompt_condition}` — e.g. `1_A1_6levels_p7`. |
| `Grade` | Target CEFR level used at generation time. |
| `essay` | The generated Arabic essay (full text, no headings). |

**File-specific notes:**

- `generated_essays_6levels_p3.tsv` — 322 essays generated under prompt **P3** (CEFR level + syntactic-profile constraints, no vocabulary list). Six CEFR levels: A1=50, A2=50, B1=56, B2=56, C1=55, C2=55. P3 achieved the strongest overall alignment with reference profiles in the paper (cosine 0.91, BAREC agreement 0.99).
- `generated_essays_5levels_p5.tsv` — 272 essays generated under prompt **P5** (CEFR level + vocabulary list + syntactic-profile constraints). Five CEFR levels: A=50, B1=56, B2=56, C1=55, C2=55. The five-level partition reflects SAMER's vocabulary granularity.

The full prompt templates for P1–P5 are in **Appendix D** of the paper.

## How the files connect

```
ARWI / ZAEBUC topics
        │
        ▼
prompts_with_*cefr_levels_and_vocab.csv     ← (topic, target CEFR, SAMER-validated vocab)
        │
        │  fed into GPT-4o with prompt template P3 or P5
        ▼
generated_essays_*levels_p*.tsv             ← (essay text + grade)
        │
        ▼
evaluation: BAREC readability + linguistic-profile cosine similarity
```

You can match a generated essay to its source prompt by parsing `topic_id` and `CEFR_level` out of `Document_ID`.


```

## Use cases

This release is intended to support:

- Reproducing the CEFR-controlled generation analysis from the paper.
- Training or evaluating Arabic readability classifiers with proficiency-aligned synthetic data.
- Building adaptive Arabic learning content where the CEFR–vocabulary–topic alignment is needed off the shelf.
- Studying lexical and syntactic correlates of CEFR levels in Arabic.

## Citation

If you use this data, please cite:

```bibtex
@inproceedings{rabih2025cefr,
  title     = {Can {LLM}s Control Readability? A Multi-Dimensional Evaluation Framework for {CEFR}-Controlled {A}rabic Generation},
  author    = {Rabih, Nour and Qwaider, Chatrine and Briscoe, Ted},
  year      = {2025},
  organization = {Mohamed bin Zayed University of Artificial Intelligence (MBZUAI)}
}
```

## Related resources

- **BAREC** readability model: <https://huggingface.co/CAMeL-Lab/readability-arabertv2-d3tok-CE>
- **SAMER** readability lexicon: [Al Khalil et al., 2020.](https://sites.google.com/nyu.edu/samer)
- **ZAEBUC** corpus: <https://sites.google.com/view/zaebuc/home>
- **ARWI** dataset: <https://github.com/mbzuai-nlp/arabic-aes-bea25>
- **CamelParser 2.0**: <https://github.com/CAMeL-Lab/camel_parser>

## Contact

For questions, please reach out to the paper authors at MBZUAI.
