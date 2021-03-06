<!-- SPACY PROJECT: AUTO-GENERATED DOCS START (do not remove) -->

# ðª spaCy Project: UD Japanese GSDãç¨ããåºæè¡¨ç¾èªè­

spaCy v3ç¨ã®æå°éã®åºæè¡¨ç¾èªè­ãã¢ãã­ã¸ã§ã¯ãã

## ð project.yml

The [`project.yml`](project.yml) defines the data assets required by the
project, as well as the available commands and workflows. For details, see the
[spaCy projects documentation](https://spacy.io/usage/projects).

### â¯ Commands

The following commands are defined by the project. They
can be executed using [`spacy project run [name]`](https://spacy.io/api/cli#project-run).
Commands are only re-run if their inputs have changed.

| Command | Description |
| --- | --- |
| `download` | spacyã®ã¢ãã«ããã¦ã³ã­ã¼ã |
| `convert` | ãã¼ã¿ã»ãããspaCyã®ãã¤ããªå½¢å¼ã¸å¤æ |
| `create-config` | åºæè¡¨ç¾èªè­ã®ã³ã³ãã¼ãã³ãå­¦ç¿ç¨ã®è¨­å®ãã¡ã¤ã«ãä½æ |
| `train` | ã¢ãã«ã®å­¦ç¿ |
| `evaluate` | ã¢ãã«ãè©ä¾¡ããææ¨ãæ¸ãåºã |
| `visualize-model` | Streamlitãç¨ãã¦ã¢ãã«ã®åºåãã¤ã³ã¿ã©ã¯ãã£ãã«å¯è¦åãã |

### â­ Workflows

The following workflows are defined by the project. They
can be executed using [`spacy project run [name]`](https://spacy.io/api/cli#project-run)
and will run the specified commands in order. Commands are only re-run if their
inputs have changed.

| Workflow | Steps |
| --- | --- |
| `all` | `download` &rarr; `convert` &rarr; `create-config` &rarr; `train` &rarr; `evaluate` &rarr; `visualize-model` |

### ð Assets

The following assets are defined by the project. They can
be fetched by running [`spacy project assets`](https://spacy.io/api/cli#project-assets)
in the project directory.

| File | Source | Description |
| --- | --- | --- |
| `assets/` | Git | The training/dev/test data. |

<!-- SPACY PROJECT: AUTO-GENERATED DOCS END (do not remove) -->
