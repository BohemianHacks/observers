# 🤗🔭 Observers

<div align="center">

A lightweight library for (generative) AI observability.

</div>

## Usage

We differentiate between observers and stores. Observers wrap generative AI APIs (like OpenAI or llama-index) and track their interactions. Stores are classes that sync these observations to different storage backends (like duckdb or Hugging Face datasets).

```python
import os

from observers.observers.models.openai import wrap_openai
from observers.stores.duckdb import DuckDBStore
from openai import OpenAI

store = DuckDBStore().connect()

api_key = os.environ["HF_TOKEN"]
openai_client = OpenAI(
    base_url="https://api-inference.huggingface.co/v1/", api_key=api_key
)

client = wrap_openai(openai_client, store=store)

response = client.chat.completions.create(
    model="Qwen/Qwen2.5-Coder-32B-Instruct",
    messages=[{"role": "user", "content": "Tell me a joke."}],
)
```

### Observers

#### Supported Observers

- [OpenAI](https://openai.com/) and every other LLM provider that implements the [OpenAI API message formate](https://platform.openai.com/docs/api-reference)

### Stores

#### Supported Stores

- [Hugging Face Datasets](https://huggingface.co/docs/huggingface_hub/en/package_reference/io-management#datasets)
- [DuckDB](https://duckdb.org/)

#### Viewing / Querying

##### Hugging Face Datasets

To view and query Hugging Face Datasets, you can use the [Hugging Face Datasets Viewer](https://huggingface.co/docs/hub/en/datasets-viewer). From within here, you can query the dataset using SQL or using your own UI.

![Hugging Face Datasets Viewer](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/hub/dataset-viewer.png)

##### DuckDB Store

The default store is [DuckDB](https://duckdb.org/) and can be viewed and queried using the [DuckDB CLI](https://duckdb.org/#quickinstall).

```bash
> duckdb store.db
> from openai_records limit 10;
┌──────────────────────┬──────────────────────┬──────────────────────┬──────────────────────┬───┬─────────┬──────────────────────┬───────────┐
│          id          │        model         │      timestamp       │       messages       │ … │  error  │     raw_response     │ synced_at │
│       varchar        │       varchar        │      timestamp       │ struct("role" varc…  │   │ varchar │         json         │ timestamp │
├──────────────────────┼──────────────────────┼──────────────────────┼──────────────────────┼───┼─────────┼──────────────────────┼───────────┤
│ 89cb15f1-d902-4586…  │ Qwen/Qwen2.5-Coder…  │ 2024-11-19 17:12:3…  │ [{'role': user, 'c…  │ … │         │ {"id": "", "choice…  │           │
│ 415dd081-5000-4d1a…  │ Qwen/Qwen2.5-Coder…  │ 2024-11-19 17:28:5…  │ [{'role': user, 'c…  │ … │         │ {"id": "", "choice…  │           │
│ chatcmpl-926         │ llama3.1             │ 2024-11-19 17:31:5…  │ [{'role': user, 'c…  │ … │         │ {"id": "chatcmpl-9…  │           │
├──────────────────────┴──────────────────────┴──────────────────────┴──────────────────────┴───┴─────────┴──────────────────────┴───────────┤
│ 3 rows                                                                                                                16 columns (7 shown) │
└────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md)
