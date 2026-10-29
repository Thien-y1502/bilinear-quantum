# Bilinear Quantum

`bilinear-quantum` provides a task-oriented API for Bilinear Quantum models.
The interface abstracts backend selection and numerical execution behind
consistent task constructors.

The package defines a 34-task interface. Provider and hardware integrations are
optional components and may evolve independently.

Current stable release: **4.0.2**. The public package exposes the same compact
task-level call pattern for all 34 APIs; implementation and provider details
remain behind the public facade.

## Install

Install from PyPI:

```bash
python -m pip install --upgrade bilinear-quantum
```

To install a locally downloaded wheel:

```bash
python -m pip install path/to/bilinear_quantum-*.whl
```

## Five-minute usage

Basic usage:

```python
import bilinear_quantum as blq

model = blq.classify(x_train, y_train)
prediction = model.predict(x_test)
report = model.evaluate(x_test, y_test)

print(prediction)
print(report.summary())
```

All task constructors accept `data`, an optional `target`, and optional
keyword configuration. A fitted model provides `predict`, `evaluate`,
`summary`, `resources`, and `save`. The package uses NumPy by default, so the
same call works in a local Python process or a Colab notebook.

## The 34 APIs

| API | Typical use |
|---|---|
| `classify` | Single-label classification |
| `regress` | Continuous-value regression |
| `multilabel` | Multiple labels per sample |
| `cluster` | Unsupervised clustering |
| `embed` | Low-dimensional representation learning |
| `rank` | Ordered scoring or ranking |
| `retrieve` | Similarity-based retrieval |
| `recommend` | Recommendation scoring |
| `detect_objects` | Object detection outputs |
| `segment_semantic` | Semantic segmentation |
| `segment_instances` | Instance segmentation |
| `estimate_pose` | Keypoint or pose estimation |
| `track_objects` | Temporal object tracking |
| `denoise` | Noise reduction |
| `super_resolve` | Resolution enhancement |
| `inpaint` | Missing-region reconstruction |
| `forecast` | Future-value forecasting |
| `impute` | Missing-value imputation |
| `detect_anomalies` | Outlier or anomaly detection |
| `detect_events` | Event detection in sequences |
| `sequence_label` | Per-step sequence labelling |
| `align_sequences` | Sequence alignment |
| `node_classify` | Graph node classification |
| `link_predict` | Graph link prediction |
| `graph_predict` | General graph prediction |
| `graph_match` | Graph matching |
| `fuse_modalities` | Multimodal feature fusion |
| `generate` | Sample or representation generation |
| `density_estimate` | Probability-density estimation |
| `learn_dynamics` | Dynamical-system learning |
| `discover_operator` | Operator discovery |
| `solve_pde` | PDE surrogate/solution tasks |
| `optimize` | Optimization tasks |
| `learn_quantum_process` | Quantum-process learning |

Each API follows the same basic shape:

```python
import bilinear_quantum as blq

task_model = blq.regress(x_train, y_train)
y_hat = task_model.predict(x_test)
metrics = task_model.evaluate(x_test, y_test)
```

For unsupervised tasks, omit the target:

```python
clusters = blq.cluster(x_train)
cluster_ids = clusters.predict(x_test)
```

For generation and optimization contracts:

```python
generator = blq.generate(x_train)
samples = generator.sample(16)

optimizer = blq.optimize(candidates, objective_values)
best = optimizer.optimize()
```

## Backend selection

The runtime accepts a backend and shot configuration without requiring provider
packages in the base install:

```python
with blq.runtime.use("aer", shots=128):
    model = blq.classify(x_train, y_train)
```

Optional provider dependency groups are available:

```bash
python -m pip install "bilinear-quantum[sim]"  # Qiskit/Aer
python -m pip install "bilinear-quantum[ibm]"  # IBM Runtime
python -m pip install "bilinear-quantum[qi]"   # Quantum Inspire
```

Hardware execution is outside the scope of this release. IBM Quantum and
Quantum Inspire support can be added without changing the task-level calls.

The wheel smoke gate confirms import/call coverage for all 34 constructors; it
is not a claim of application-dataset SOTA, QPU performance, or quantum
advantage.

## Colab smoke test

```python
!python -m pip install -U bilinear-quantum

import bilinear_quantum as blq
print(blq.__version__)
core = {"BLQModel", "BLQResult", "runtime"}
print("task count:", len([n for n in blq.__all__ if n not in core]))
```

Use a fresh runtime when comparing versions. Keep train/test preprocessing and
baseline evaluation identical when reporting experimental results.

## Scope and reproducibility

This repository documents the public API and installation contract. Algorithmic
details, benchmark protocols, and validation artifacts should be identified by
the exact package version and corresponding release record when results are
reported.

## License

See [LICENSE](LICENSE) for the distribution terms. Third-party dependencies
retain their own licenses.
