# Bilinear Quantum 4.1.0

The 4.1.0 wheel is the first public release whose 34 task constructors use
the BQDL v5 implementation directly. The shared core contains differentiable
angle state preparation, trainable cross-register CNOT/RZZ interaction, and
bounded signed Pauli-observable readout without a classical prediction head.

The package selects CPU or CUDA state-vector simulation automatically. QPU
execution and application-dataset superiority remain separate validation
gates and are not claimed by this release.
