# Political trends analysis

Analysis of political trends: collects comments from social networks (Facebook, YouTube) and processes them automatically through filtering, sentiment analysis, and classification to gauge public opinion around French political parties.

## Architecture

The project is split into several modules:

| Module | Role |
|--------|------|
| `web_scraping/` | Collects Facebook and YouTube comments |
| `filter/` | Filters comments by relevant political party (RN, LREM) |
| `sentiment_analysis/` | Sentiment analysis (positive / negative / neutral) with a fine-tuned model (Barthez) |
| `classification/` | Classifies comments by topic / party theme |
| `main.py` | Main pipeline: reads the CSV, filters, analyzes, classifies, and inserts into a MySQL database |

## Pipeline

1. **Collection** — `web_scraping/` fetches comments from Facebook and YouTube and writes them to a CSV file (`DATA_FILE`).
2. **Filtering** — `filter/inference.py` determines whether a comment concerns one of the tracked parties (RN, LREM). If not (`id_groupe == 0`), the comment is ignored.
3. **Sentiment analysis** — `sentiment_analysis/inference.py` returns a score and a sentiment.
4. **Classification** — `classification/inference.py` returns the comment's class identifier.
5. **Storage** — the result is inserted into the `data` table of the MySQL database (`trend`).

## Installation

```bash
pip install -r requirements.txt
```

## Configuration

Fill in the constants in the different `config.py` files:

- `config.py` — MySQL connection (`HOST`, `USER`, `PASSWORD`, `DATABASE`) and data file path (`DATA_FILE`).
- `web_scraping/config.py` — YouTube API key and paths to the scraping scripts.
- `sentiment_analysis/config.py` — pretrained model, training data, and model paths.
- `classification/config.py` — training file and save path.
- `filter/config.py` — dictionary of parties and their keywords.

## Usage

Run the main pipeline:

```bash
python main.py
```

### Modules

Train the models / run the inferences:

```bash
# Sentiment analysis
python sentiment_analysis/train.py
python sentiment_analysis/inference.py

# Classification
python classification/train.py
python classification/inference.py

# Web scraping
python web_scraping/facebook.py
python web_scraping/youtube.py
```

## Prerequisites

- Python (see `requirements.txt`)
- MySQL with a `trend` database and a `data` table
  (`date`, `comment`, `id_source`, `score`, `sentiment`, `id_class`, `id_groupe`)
