# Bilinear Quantum

`bilinear-quantum` is a task-level public facade for Bilinear Quantum models.
Users import one package and call a task; provider details and research
implementation details stay behind the package boundary.

The 34-task facade is being released as `4.0.0.dev1`. It is a development
release: the public names are stable enough for notebook experiments, while
provider and hardware integrations remain optional and may evolve.

## Install

After the preview is published to PyPI:

```bash
python -m pip install --upgrade "bilinear-quantum==4.0.0.dev1"
```

To install a locally downloaded wheel before the PyPI release:

```bash
python -m pip install bilinear_quantum-4.0.0.dev1-py3-none-any.whl
```

## Five-minute usage

The public call pattern is deliberately small:

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

## The 34 public APIs

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

## Optional backend selection

The public runtime exposes a backend-selection hook without requiring provider
packages in the base install:

```python
with blq.runtime.use("aer", shots=128):
    model = blq.classify(x_train, y_train)
```

Optional dependency groups are provided for later integration:

```bash
python -m pip install "bilinear-quantum[sim]"  # Qiskit/Aer
python -m pip install "bilinear-quantum[ibm]"  # IBM Runtime
python -m pip install "bilinear-quantum[qi]"   # Quantum Inspire
```

The development wheel does not claim a hardware-QPU result. IBM Quantum and
Quantum Inspire execution will be qualified separately and will not change
the user-facing task calls.

## Colab smoke test

```python
!python -m pip install -U "bilinear-quantum==4.0.0.dev1"

import bilinear_quantum as blq
print(blq.__version__)
core = {"BLQModel", "BLQResult", "runtime"}
print("public task count:", len([n for n in blq.__all__ if n not in core]))
```

Use a fresh runtime when comparing versions. Keep train/test preprocessing and
baseline evaluation identical when reporting experimental results.

## Scope and privacy

The public repository contains usage documentation only. Private research
notes, datasets, checkpoints, evidence archives, and unpublished experiments
are kept in the private development repository and are not part of the public
package or this README.

## License

See [LICENSE](LICENSE) for the distribution terms. Third-party dependencies
retain their own licenses.
