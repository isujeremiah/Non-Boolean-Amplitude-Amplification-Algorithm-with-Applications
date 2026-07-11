# Non-Boolean-Amplitude-Amplification-Algorithm-with-Applications
This notebook presents an enhanced version of the generalized Grover’s algorithm, extended to handle the non‑Boolean setting.

## Overview

This notebook implements the **Non-Boolean Quantum Amplitude Amplification Algorithm** from [Prasanth Shyamsundar (2021)](https://arxiv.org/abs/2102.04975), with an application to **Quantum Value at Risk (QVaR)** for portfolio risk assessment. The Non-Boolean Quantum Amplitude Amplification is an advanced extension of Grover's algorithm that generalizes quantum search to support:
- **Multiple marked states** (generalized Grover)
- **Non-boolean objective functions** $\varphi:$ `{0,1, \dots, N-1\}` $\to [0,1]$, where $N = 2^n$ for a given non-negative integer $n$. In this notebook $\varphi(x) = \pi f(x)$, where $f(x) = \sin^2(x)$.
- **Alternating odd/even iterations** that yield optimal amplification behavior.

The **Quantum Value at Risk** estimates portfolio loss probability across $2^n$ market scenarios using quantum amplitude amplification:
- Encodes scenario probabilities in quantum state $|\psi_0\rangle = A|0^n\rangle$
- Marks high-risk scenarios via phase oracle based on loss function $f(x)$
- Amplifies high-risk amplitudes using diffuser operator
- Measures ancilla to extract $\Pr(\text{loss} > \tau)$

## Quantum Advantage
- **Classical:** $O(2^n)$ samples needed
- **Quantum:** $O(\sqrt{2^n})$ oracle calls via amplitude estimation
- **Example:** For $n=10$ (1024 scenarios), quantum uses ~32 evaluations vs. 1024 classical samples

## Notebook Structure

| Section | Description |
|---------|-------------|
| **Steps 1-3** | Build unitary operator $A_0$, construct diffuser $S_{\Psi_0}$ |
| **Steps 4-6** | Define phase oracle $U_\varphi$, construct circuit |
| **Step 7** | Implement amplitude amplification algorithm with iterations |
| **Application** | QVaR implementation for portfolio risk decision-making |

## Algorithm Components

### 1. Unitary Preparation
- Generate random unitary $A$ of size $2^n \times 2^n$
- Create superposition $|\psi_0\rangle = A|0^n\rangle$

### 2. Phase Oracle
$$U_\varphi = \text{diag}(e^{i\pi g(0)}, e^{i\pi g(1)}, \ldots, e^{i\pi g(N-1)})$$
Encodes non-boolean function values as phases.

### 3. Diffuser Operator
$$S_{\Psi_0} = 2|\psi_0\rangle\langle\psi_0| - I$$
Performs selective phase-flip (reflection about $|\psi_0\rangle$).

### 4. Iterations
- **Odd iterations:** Apply oracle + diffuser
- **Even iterations:** Apply oracle adjoint + diffuser
- **Number of iterations:** $K = \lfloor \frac{\pi}{2\theta} \rfloor$ where $\theta = \arccos(\mu)$

## QVaR Decision Rule

**Hypothesis Testing:**
- $H_0: \mu \geq \tau$ → **Acceptable** risk (no intervention)
- $H_1: \mu < \tau$ → **Unacceptable** risk (rebalance portfolio)

Where $\mu = \sum_x |\langle x|\psi_0\rangle|^2 \cos(\pi f(x))$ is the weighted-mean metric.

<!-- **Monotonicity:** Ancilla probability $P(\text{ancilla}=1) = \sin^2((2K+1)\theta)$ distinguishes the two hypotheses without measuring all $2^n$ states. -->


## Implementation Details

- **Framework:** Qiskit (IBM Quantum)
- **State vectors:** Dense matrix simulation
- **Parameters:** $n=3$ (main), $n=2$ (QVaR application)
- **Registers:** Data qubits + 1 ancilla qubit

## Dependencies

```python
from qiskit import QuantumCircuit, QuantumRegister, AncillaRegister
from qiskit.quantum_info import Operator, Statevector
import numpy as np
import sympy as sp
```

## References

1. [Prasanth Shyamsundar (2021)](https://arxiv.org/abs/2102.04975) - Non-Boolean Quantum Amplitude Amplification and
Quantum Mean Estimation
2. [Grover's Algorithm](https://en.wikipedia.org/wiki/Grover%27s_algorithm)

## Notes

- Ancilla qubit ordering reversed relative to paper (data qubits first)
- Algorithm reduces to generalized Grover under specific conditions
- QVaR applicable to any risk scoring function $f(x) \in [0,1]$

---

**Author:** Jeremiah Isu | **Affiliation:** Erdős Institute Quantum Bootcamp & The University of Oklahoma
