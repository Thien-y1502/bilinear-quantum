# Bilinear Quantum 3.0.1

`bilinear-quantum` is a task-oriented TensorFlow/Keras library built around the
Bilinear Quantum Interaction Graph (BQIG). Supply data and a task; the library
handles data adaptation, recipe selection, optimization, validation, early
stopping, prediction, and evidence metadata through a compact public API.

Version `3.0.1` is a maintenance and compatibility release of the stable
3.x task-level API. It improves notebook-environment compatibility, keeps the
core install smaller, and moves Cirq and PennyLane to optional extras.

The project does **not** claim universal model superiority or computational
quantum advantage. Such claims require locked protocols and independently
verified experiments.

## Supported environment

- 64-bit CPython 3.12 or 3.13;
- Windows or Linux;
- CPU execution works by default;
- TensorFlow can use an available supported GPU when the TensorFlow runtime
  detects it;
- some preprocessing and scikit-learn components remain CPU-based, so GPU
  availability does not imply that every stage of a pipeline is GPU-accelerated;
- TensorFlow Quantum 0.7.6 belongs to a separate Python 3.12 reference
  environment and is not silently substituted on Python 3.13.

The final `3.0.1` release wheels were validated on Python 3.12 and 3.13. The
core classification path was also validated with the default installation,
without Cirq or PennyLane installed.

## Install

Install the stable release from PyPI:

```bash
python -m pip install --upgrade "bilinear-quantum==3.0.1"
```

Verify the installed version:

```python
import bilinear_quantum as bq

print(bq.__version__)
```

Expected output:

```text
3.0.1
```

## Google Colab / notebook quick start

Start from a fresh runtime when possible.

```python
!python -m pip install -U "bilinear-quantum==3.0.1"
```

Then verify the package:

```python
import bilinear_quantum as bq
print("Bilinear Quantum:", bq.__version__)
```

Optional TensorFlow device check:

```python
import tensorflow as tf

print("TensorFlow:", tf.__version__)
print("GPU devices:", tf.config.list_physical_devices("GPU"))
```

The same installation command can be used in other compatible notebook
environments, including Kaggle, when the runtime uses supported CPython 3.12 or
3.13 and package installation from PyPI is enabled.

## Five-minute classification example

The easiest input is a pandas `DataFrame`. Pass the target column name during
training, and omit that column when predicting new rows.

```python
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
import bilinear_quantum as bq

dataset = load_breast_cancer(as_frame=True)
frame = dataset.frame.rename(columns={"target": "label"})

train_df, test_df = train_test_split(
    frame,
    test_size=0.20,
    random_state=42,
    stratify=frame["label"],
)

model = bq.learn.classify(
    train_df,
    target="label",
    quality="fast",
)

X_test = test_df.drop(columns="label")
predictions = model.predict(X_test)
probabilities = model.predict_proba(X_test)

print(model.summary())
print(predictions[:10])
print(probabilities.shape)
```

Use `groups=` when samples from the same person, device, site, or trial must not
cross the train/validation boundary:

```python
model = bq.learn.classify(frame, target="label", groups=subject_ids)
```

The optional quality policy is semantic rather than architectural:

```python
fast_model = bq.learn.classify(frame, target="label", quality="fast")
best_model = bq.learn.classify(frame, target="label", quality="best")
```

Accepted values are `"auto"`, `"fast"`, `"balanced"`, and `"best"`.

## Optional quantum runtimes

The default install does **not** require Cirq or PennyLane.

Install Cirq support only when needed:

```bash
python -m pip install "bilinear-quantum[cirq]==3.0.1"
```

Install PennyLane support only when needed:

```bash
python -m pip install "bilinear-quantum[pennylane]==3.0.1"
```

Install all optional runtime and experiment dependencies:

```bash
python -m pip install "bilinear-quantum[full]==3.0.1"
```

Cirq provides circuit construction and simulation, PennyLane provides a
differentiable quantum execution lane, and TensorFlow/Keras remains the main
training runtime.

You can inspect available backends from the public API:

```python
import bilinear_quantum as bq

print(bq.quantum.available_backends())

resource_estimate = bq.quantum.resources(
    latent_dim=64,
    rank=16,
    heads=4,
)
print(resource_estimate)
```

## Common tasks

### Regression

```python
regressor = bq.learn.regress(training_frame, target="price")
prediction = regressor.predict(new_rows)
metrics = regressor.evaluate(test_frame, target="price")
```

### Embedding and clustering

```python
embedding = bq.learn.embed(features, dimensions=32)
embedded_new_rows = embedding.transform(new_rows)

clusters = bq.learn.cluster(features, clusters=4)
cluster_ids = clusters.predict(new_rows)
```

### Sequence classification

Sequence arrays may have shape `(samples, time, features)`.

```python
sequence_model = bq.sequence.classify(windows, labels, groups=subject_ids)
sequence_labels = sequence_model.predict(unseen_windows)
```

### Forecasting

```python
forecast = bq.sequence.forecast(
    time_series_frame,
    target="power",
    horizon=24,
)

print(forecast.values)
print(forecast.summary())
```

### Anomaly detection

```python
anomalies = bq.sequence.detect_anomalies(features)
print(anomalies.labels)   # 1 = anomaly, 0 = normal
print(anomalies.scores)
```

### Paired or multimodal data

```python
fusion_model = bq.fusion.learn(sensor_features, context_features, labels)
fusion_predictions = fusion_model.predict(new_sensor, new_context)
```

### Graph learning

```python
node_model = bq.graph.node_classify(node_features, node_labels)
edge_model = bq.graph.edge_classify(node_features, edge_index, edge_labels)
link_model = bq.graph.link_predict(node_features, positive_edges)
```

### Scientific surrogate learning

```python
surrogate = bq.science.surrogate(parameters, observations)
estimated_observations = surrogate.predict(new_parameters)
```

## Save and restore a trained result

```python
saved_path = model.save("saved_bq_model")
restored = bq.AutoResult.load(saved_path)
predictions = restored.predict(new_rows)
```

Only load model files from a trusted source.

## Evidence and fair baseline comparison

```python
certificate = bq.audit.certify(model)
bq.audit.export_evidence(model, "evidence.json")

rows = bq.audit.compare(
    y_test,
    {
        "bilinear_quantum": bq_predictions,
        "baseline": baseline_predictions,
    },
    reference="baseline",
    problem="classification",
)

for row in rows:
    print(row.to_dict())
```

Every compared model must use the same held-out examples. A positive empirical
result is evidence only for the tested data, split, metric, and protocol.

## Public task namespaces

- `bq.learn`: classification, regression, embeddings, and clustering;
- `bq.sequence`: classification, forecasting, events, segmentation, anomalies;
- `bq.fusion`: paired-source learning, alignment, and matching;
- `bq.relation`: matching, ranking, retrieval, and recommendation;
- `bq.graph`: node, edge, and link prediction;
- `bq.science`: surrogates, dynamics, operator discovery, and data-driven solves;
- `bq.quantum`: backend inspection, execution, sampling, noise, and resources;
- `bq.audit`: certification, paired comparison, and evidence export;
- `bq.research`: explicit, recorded research protocols.

## Troubleshooting

### `No matching distribution found`

The public `3.0.1` release provides CPython-specific wheels for Python 3.12 and
3.13. Check the runtime first:

```bash
python --version
python -c "import platform; print(platform.architecture())"
```

A Python 3.11 or 3.14 environment will not match the published `3.0.1` wheels.

### Check dependency consistency

```bash
python -m pip check
```

A healthy environment should report:

```text
No broken requirements found.
```

### TensorFlow Quantum on Python 3.13

TensorFlow Quantum 0.7.6 has no compatible CPython 3.13 wheel. Use the main
TensorFlow, PennyLane, and Cirq lanes on Python 3.13, or create a separate
Python 3.12 TFQ reference environment.

### Data leakage prevention

For subject-, patient-, device-, or site-dependent data, always pass the
corresponding identifiers through `groups=`. For forecasting, keep time order
and never shuffle future rows into training.

## Support

For installation problems or a reproducible usage question, open a GitHub
issue and include:

- `bilinear-quantum` version;
- Python version;
- operating system or notebook environment;
- CPU/GPU runtime information when relevant;
- a minimal example that uses only the documented public API.

Do not attach private or sensitive data.

## Distribution and license

This repository intentionally contains documentation only. It does not contain
implementation source, private recipes, experiment registries, datasets,
checkpoints, build pipelines, tests, or unpublished evidence.

The public package is distributed as CPython-specific sourceless wheels. It
does not contain implementation `.py` files, and no source distribution is
published.

The compiled runtime is governed by the accompanying **Bilinear Quantum
Proprietary License Agreement (BQPLA) v1.0**. It permits installation and use
through the documented public Python API, but does not permit redistribution,
resale, sublicensing, modification, derivative works, decompilation, reverse
engineering, or extraction of implementation details without prior written
permission. Third-party dependencies retain their own licenses.

Project documentation: https://github.com/Thien-y1502/bilinear-quantum

PyPI package: https://pypi.org/project/bilinear-quantum/
