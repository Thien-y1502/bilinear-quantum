# Changelog

## 4.2.0

- Added the public `bilinear_quantum.nn` namespace for reusable BQDL v5 Torch
  layers, blocks, networks, and neural task heads.
- Preserved the 34 stable top-level task constructors and the documented
  `import bilinear_quantum as blq` interface.
- Added PennyLane to the optional simulator dependency group for later parity
  validation.
- Hardware-QPU execution, application-dataset SOTA, and quantum advantage are
  not claims of this release.

## 4.1.0

- Routed all 34 public APIs through the shared BQDL v5 two-register PyTorch
  state-vector core.
- Kept the compact public facade while adding auditable v5 resource metadata.
- No QPU, application-dataset SOTA, or quantum-advantage claim is made.

## 4.0.2

- Wired all 34 task constructors into the public `bilinear_quantum` facade.
- Added stable generation (`sample`) and optimization (`optimize`) helpers.
- Added a clean-install smoke check covering all 34 public entry points.
- Kept provider and hardware execution optional; no QPU or quantum-advantage
  claim is made by the package release.

## 4.0.0

- Established the first public 34-task Bilinear Quantum API line.
- Added optional simulator and provider dependency groups.
