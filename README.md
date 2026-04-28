# MemeIntent

**Benchmark dataset for intent description generation on internet memes.**

This repository hosts the official dataset accompanying the SIGdial 2024 paper:

> **MemeIntent: Benchmarking Intent Description Generation for Memes**
> Jeongsik Park, Khoi P. N. Nguyen, Terrence Li, Suyesh Shrestha, Megan Kim Vu, Jerry Yining Wang, Vincent Ng.
> _Proceedings of the 25th Annual Meeting of the Special Interest Group on Discourse and Dialogue (SIGDIAL 2024)_, Kyoto, Japan, pages 631–643.
> [[ACL Anthology]](https://aclanthology.org/2024.sigdial-1.54/) · [[PDF]](https://aclanthology.org/2024.sigdial-1.54.pdf) · [DOI: 10.18653/v1/2024.sigdial-1.54](https://doi.org/10.18653/v1/2024.sigdial-1.54)

---

## Overview

While most existing meme research focuses on detecting whether a meme contains malicious or harmful content, **MemeIntent** introduces a new task: **intent description generation** — generating a natural-language description of _what the author intended to convey_ when creating a meme.

To support this task, we release a corpus of **950 memes** annotated with:

1. **Reader-perceived intents** — what the meme is trying to communicate (e.g., mock, praise, criticize, persuade).
2. **Background knowledge (BKs)** — the world / cultural knowledge a reader needs to correctly infer the intent.
3. **Image captions** — short descriptions of the visual content.
4. **OCR text** — the text rendered on the meme image.

The paper additionally establishes baseline performance on this task using state-of-the-art large language models, and shows that **retrieving the right background knowledge is critical** for generating faithful intent descriptions.

## Why this dataset?

- **Beyond hate-speech / harmful-meme detection.** Most public meme benchmarks (Hateful Memes, MAMI, SemEval-2021 Task 6, etc.) are framed as classification tasks. MemeIntent targets _generation_, requiring multimodal grounding **and** commonsense / world knowledge.
- **Explicit background-knowledge supervision.** Each example contains the reasoning chain (`bks`) needed to bridge the gap between surface content (image + text) and author intent — useful for studying retrieval-augmented generation, chain-of-thought reasoning, and knowledge grounding in vision-language models (VLMs).
- **Compact and high-quality.** 950 carefully annotated memes covering politics, COVID-19, anti-vaccine discourse, and other socially-relevant topics commonly found in real-world meme data.

## Repository contents

```
MemeIntent/
├── README.md
└── meme_intent.json   # 950 annotated memes (JSON object keyed by example id)
```

## Data format

`meme_intent.json` is a JSON object mapping a string id (`"1"`, `"2"`, …, `"950"`) to an annotation dictionary:

```json
{
  "69": {
    "img": "169_image.png",
    "text": "Our elders were called to war to save lives. We are being called to sit on the couch to save theirs. We can do this",
    "image_caption": "depicting a couch",
    "bks": "* previous generations went to war to fight for their country\n* current COVID restrictions include staying at home and not going out\n* going to war is a dangerous and difficult activity\n* that sitting on the couch is very easy\n* activities with less hardship are generally more appealing to people",
    "intents": ["the meme encourages following COVID-19 restrictions"]
  }
}
```

| Field           | Description                                                                             |
| --------------- | --------------------------------------------------------------------------------------- |
| `img`           | Filename of the meme image in the SemEval-2021 Task 6 corpus.                           |
| `text`          | OCR / transcribed text on the meme.                                                     |
| `image_caption` | Human-written caption describing the visual content of the meme.                        |
| `bks`           | Background knowledge needed to infer the intent (one item per line, prefixed with `*`). |
| `intents`       | List of reader-perceived author intent descriptions (gold references).                  |

## Getting the meme images

The meme **images themselves are not redistributed here** due to licensing. Download them from the SemEval-2021 Task 6 corpus and align by filename (`img`):

- SemEval-2021 Task 6 corpus: <https://github.com/di-dimitrov/SEMEVAL-2021-task6-corpus>

After downloading, point your data loader at the image directory and use the `img` field in `meme_intent.json` to locate each image.

## Quick start

```python
import json

with open("meme_intent.json", "r", encoding="utf-8") as f:
    data = json.load(f)

print(f"# examples: {len(data)}")
example = data["69"]
print("Image:", example["img"])
print("OCR text:", example["text"])
print("Caption:", example["image_caption"])
print("Background knowledge:\n", example["bks"])
print("Gold intents:", example["intents"])
```

## Suggested tasks & evaluation

The dataset can be used to study, among others:

- **Intent description generation** from `(image, text)` → `intents` (the headline task in the paper).
- **Knowledge-grounded generation** from `(image, text, bks)` → `intents`.
- **Background-knowledge retrieval / generation**: predicting `bks` from `(image, text)`.
- **Multimodal commonsense reasoning** for memes and other image–text artifacts.

For evaluation, the paper reports standard generation metrics (e.g., BLEU, ROUGE, BERTScore) along with analyses of the impact of background knowledge. Please refer to the paper for the full experimental protocol.

## Citation

If you use this dataset, please cite:

```bibtex
@inproceedings{park-etal-2024-memeintent,
    title     = "{M}eme{I}ntent: Benchmarking Intent Description Generation for Memes",
    author    = "Park, Jeongsik  and
                 Nguyen, Khoi P. N.  and
                 Li, Terrence  and
                 Shrestha, Suyesh  and
                 Vu, Megan Kim  and
                 Wang, Jerry Yining  and
                 Ng, Vincent",
    booktitle = "Proceedings of the 25th Annual Meeting of the Special Interest Group on Discourse and Dialogue",
    month     = sep,
    year      = "2024",
    address   = "Kyoto, Japan",
    publisher = "Association for Computational Linguistics",
    url       = "https://aclanthology.org/2024.sigdial-1.54/",
    doi       = "10.18653/v1/2024.sigdial-1.54",
    pages     = "631--643"
}
```

## License

- **Annotations** (`meme_intent.json`) are released under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/), consistent with ACL Anthology materials.
- **Meme images** are _not_ included in this repository; they are distributed by the [SemEval-2021 Task 6 organizers](https://github.com/di-dimitrov/SEMEVAL-2021-task6-corpus) under their respective terms.

## Keywords

memes · intent description · multimodal NLP · vision-language models · benchmark dataset · commonsense reasoning · background knowledge · meme understanding · meme intent detection · hateful memes · harmful memes · SIGDIAL 2024
