# A Scalable Implementation of Shor's Algorithm on the Quantum Rings Simulator

## Introduction
Shor’s algorithm is widely recognized for its potential to factor large integers efficiently by leveraging quantum computation. In this project, we use Shor’s algorithm to factor semiprime numbers of varying bit sizes—starting from small (8-bit) integers and potentially scaling up to 256-bit or more. All our simulations run on the **Quantum Rings Simulator**, ensuring an authentic quantum approach without classical shortcuts.

### Key Requirements
1. **Factor Large Semiprime Integers**  
   We work through a prepared list of semiprimes, demonstrating our method’s universality.
2. **Leverage the Quantum Rings Simulator**  
   All simulations execute on the Quantum Rings platform, reflecting realistic quantum-resource constraints.
3. **Focus on Real Quantum Solutions**  
   We avoid purely classical factorization tricks—every step aligns with Shor’s quantum procedure.
4. **Demonstrate Universality**  
   Our solution must be able to handle multiple numbers of each given bit size.
5. **Document the Approach**  
   We clearly outline our methods, scalability considerations, and lessons learned.

---

## Overview of the Algorithm

### 1. Shor’s Algorithm Basics
- **Choose a Base \(a\)**: Pick an integer \(a\) such that \(1 < a < N\) and \(\gcd(a, N) = 1\).  
- **Find the Period \(r\)**: On quantum hardware, perform order finding to solve \(a^r \equiv 1 \pmod{N}\).  
- **Extract Factors**: If \(r\) is even, compute \(\gcd(a^{r/2} \pm 1, N)\) to get non-trivial factors of \(N\).

### 2. Key Steps in the Implementation
1. **Superposition Creation**: We initialize “control” qubits in a uniform superposition to represent different exponents \(x\).  
2. **Optimized Modular Exponentiation**: 
   - Use exponent-by-squaring for \(a^{2^k} \mod N\).  
   - Prune unnecessary gates if \(a^{2^k} \equiv 1 \pmod{N}\).  
3. **Approximate iQFT**: We apply an approximate inverse Quantum Fourier Transform, limiting distant controlled-phase gates to keep circuit depth manageable.  
4. **Measurement**: Measure control qubits to infer the period \(r\).  
5. **Classical Post-Processing**: Compute GCDs to reveal factors from the measured period.

---

## Implementation Choices and Rationale

1. **Register Allocation**  
   - We dynamically allocate qubits based on the bit size of the target semiprime \(N\).  
   - For example, if \(N\) is \(k\) bits, we use about \(k + 2\) control qubits for the exponent range and an additional \(\sim k\) qubits for storing values \(\le N\).

2. **Optimized Modular Exponentiation**  
   - We apply the modular exponentiation in a power-of-two fashion (\(a^{2^i} \mod N\)), each controlled by one control qubit.  
   - This approach efficiently handles large exponents without inflating the circuit depth unnecessarily.

3. **Approximate iQFT**  
   - Instead of applying the full iQFT (which can be costly for large registers), we introduce a “precision cutoff.”  
   - We only apply controlled-phase gates up to a certain distance in qubit indices, reducing the number of gates exponentially with the cutoff.

4. **Threaded Execution**  
   - To handle multiple bit sizes quickly, we run separate factorization tasks in parallel (thread pool) with a per-task timeout.  
   - This strategy doesn’t affect the quantum algorithm itself but allows for faster experimentation across different semiprime sizes.

5. **Classical Factor Extraction**  
   - After reading measurement outcomes, we interpret the binary string as an approximation of \(s/r\).  
   - We verify \(r\) (checking whether \(a^r \equiv 1 \pmod{N}\) and \(r\) is even) before computing \(\gcd(a^{r/2} \pm 1, N)\) to obtain potential factors.

---

## Scalability Considerations

1. **Qubit Budget and Circuit Depth**  
   - Each additional bit in \(N\) significantly increases circuit complexity.  
   - Our use of approximate iQFT and pruning in modular exponentiation helps keep the circuit length under control.

2. **Iterative Phase Estimation (Future Direction)**  
   - Iterative or semiclassical phase estimation can drastically reduce the number of qubits, trading off for repeated circuit executions.

3. **Probability of Successful Factorization**  
   - If a chosen \(a\) yields an odd period or trivial factors, we must pick a different \(a\).  
   - The random choice of \(a\) typically works with reasonable probability (about 50% success rate for valid period extraction).

4. **Requesting More Resources**  
   - If we demonstrate success on smaller instances but need additional qubits or compute time for larger (e.g., 256-bit) semiprimes, we can coordinate with Quantum Rings to expand our resource limits.

---

## Observations and Learnings

1. **Gate Pruning is Essential**  
   - Each unneeded controlled operation contributes overhead in simulation. By checking whether \(a^{2^i} \equiv 1 \pmod{N}\), we skip pointless gates.

2. **Approximate iQFT is a Viable Shortcut**  
   - While it slightly reduces accuracy, it often still produces a valid period. This trade-off is crucial for handling larger bit sizes.

3. **Parallelization Simplifies Testing**  
   - Handling multiple bit sizes concurrently provides broader coverage and quick feedback on which approaches scale better.

4. **Avoiding Classical Factorization**  
   - We ensure the entire pipeline remains authentically “quantum,” only using classical post-processing to compute GCDs and interpret measured data.

---

## Conclusion and Future Work

In this work, we have:
- **Implemented** a resource-optimized version of Shor’s algorithm on the Quantum Rings Simulator.
- **Validated** it on multiple semiprime bit sizes, using the same universal framework (not a custom-coded factorization trick).
- **Learned** that approximate iQFT and modular exponentiation pruning are key to enabling the algorithm to scale.

### Next Steps
- **Iterative Phase Estimation**: This could further reduce qubit usage and allow factoring of larger \(N\) with fewer hardware resources.  
- **Adaptive Precision**: Dynamically adjust the cutoff in approximate iQFT based on measured outcomes or partial success metrics.  
- **Expanded Resource Access**: Once we show consistent factorization of moderately large semiprimes (say, 64–128 bits), we may request enhanced simulation resources from Quantum Rings for 256-bit attempts.

**Acknowledgments**: We extend our thanks to the Quantum Rings community for providing the tools and support necessary to bring this project to fruition.
