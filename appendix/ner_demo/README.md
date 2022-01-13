<!-- SPACY PROJECT: AUTO-GENERATED DOCS START (do not remove) -->

# 🪐 spaCy Project: UD Japanese GSDを用いた固有表現認識

spaCy v3用の最小限の固有表現認識デモプロジェクト。

## 📋 project.yml

The [`project.yml`](project.yml) defines the data assets required by the
project, as well as the available commands and workflows. For details, see the
[spaCy projects documentation](https://spacy.io/usage/projects).

### ⏯ Commands

The following commands are defined by the project. They
can be executed using [`spacy project run [name]`](https://spacy.io/api/cli#project-run).
Commands are only re-run if their inputs have changed.

| Command | Description |
| --- | --- |
| `download` | spacyのモデルをダウンロード |
| `convert` | データセットをspaCyのバイナリ形式へ変換 |
| `create-config` | 固有表現認識のコンポーネント学習用の設定ファイルを作成 |
| `train` | モデルの学習 |
| `evaluate` | モデルを評価し、指標を書き出す |
| `visualize-model` | Streamlitを用いてモデルの出力をインタラクティブに可視化する |

### ⏭ Workflows

The following workflows are defined by the project. They
can be executed using [`spacy project run [name]`](https://spacy.io/api/cli#project-run)
and will run the specified commands in order. Commands are only re-run if their
inputs have changed.

| Workflow | Steps |
| --- | --- |
| `all` | `download` &rarr; `convert` &rarr; `create-config` &rarr; `train` &rarr; `evaluate` &rarr; `visualize-model` |

### 🗂 Assets

The following assets are defined by the project. They can
be fetched by running [`spacy project assets`](https://spacy.io/api/cli#project-assets)
in the project directory.

| File | Source | Description |
| --- | --- | --- |
| `assets/` | Git | The training/dev/test data. |

<!-- SPACY PROJECT: AUTO-GENERATED DOCS END (do not remove) -->
