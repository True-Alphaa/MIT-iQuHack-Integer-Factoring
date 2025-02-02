# Shor's Algorithm on Quantum Rings Simulator

This repository demonstrates a **resource-optimized** implementation of **Shor’s Algorithm** for factoring **semiprime** integers on the **Quantum Rings Simulator**, from small (8-bit) to **64-bit** so far. The approach is purely quantum (no classical shortcuts) and features **approximate inverse QFT** plus **pruned modular exponentiation** to handle circuit depth and gate counts.

---

## Key Achievements

- **64-Bit Factorization**  
  - **Number**: 9223372036854775783  
  - **Factors**: 922337203 and 9999999997  
  - **Gate Operations**:  
    - `cx`: 10500  
    - `ccx`: 5200  
    - `h`: 128  
    - `u1`: 64  
    - `measure`: 64  
  - **Runtime**: ~40 minutes  
  - **Qubits Used**: 130  

- **Universality**  
  - Successful on multiple semiprime bit sizes (8-bit up to 64-bit).  
  - Same algorithmic framework can scale with more qubits.

---

## Approach Summary

1. **Superposition Creation**  
   - Control qubits are put in a uniform superposition for exponentiation.
2. **Optimized Modular Exponentiation**  
   - Power-of-two structure (\(a^{2^i} \mod N\)) and gate pruning reduce circuit depth.
3. **Approximate iQFT**  
   - Limits distant phase corrections, keeping the circuit shallower.
4. **Measurement and GCD**  
   - Classical GCD is used only after extracting the period \(r\) from the quantum measurement.

---

## Scalability Considerations

- **Qubit Budget**: Each extra bit requires more qubits and deeper circuits; approximate iQFT helps mitigate this.  
- **Iterative Phase Estimation** (Future): Could further cut qubit usage by reusing a single control qubit.  
- **Parallel/Threaded Runs**: Simplifies testing across different bit sizes.

---

## Next Steps

- **Refine Phase Estimation**: Implement fully iterative or semiclassical methods for bigger numbers.  
- **Adaptive Precision**: Dynamically tune iQFT accuracy based on partial measurement outcomes.  
- **Resource Upgrades**: Request additional qubits or compute time from Quantum Rings for 128-bit and beyond.

**Acknowledgments**: We thank the Quantum Rings team for the simulator resources and community support.
