# QUAD: ProVerif Symbolic Security Verification and Reproducibility Package

**Manuscript:** *A Lightweight Hybrid Post-Quantum Authentication Protocol for Resource-Constrained UAV–GCS Communications*  
**Verifier:** ProVerif 2.05 (Windows)  
**Scope:** Six formal symbolic models with 26 manuscript-numbered queries; three independent event-reachability diagnostic models with 19 reconstructed event witnesses.  
**Source integrity:** Every `.pv` file has been copied byte-for-byte from the reviewed source version. Only archive paths and filenames were changed. See `SHA256SUMS.txt`.

## 1. Model-to-query mapping

| Verification objective | Formal model | Queries | Observed result |
|---|---|---|---|
| Initial authenticated handshake (M1–M6) | `QUAD_Authenticated_Handshake_Model_Q01-Q06.pv` | Q1–Q6 | 6 True |
| Authenticated rekeying (R1–R4) | `QUAD_Authenticated_Rekeying_Model_Q07-Q11.pv` | Q7–Q11 | 5 True |
| Bounded rekeying retransmissions | `QUAD_Bounded_Rekey_Retransmission_Model_Q12-Q16.pv` | Q12–Q16 | 5 True |
| Single long-term-secret disclosure after M6 | `QUAD_Post_M6_Single_Secret_Disclosure_Model_Q17-Q22.pv` | Q17–Q22 | 6 True |
| Historical telemetry compromise and future impersonation | `QUAD_Historical_Telemetry_and_Impersonation_Attack_Model_Q23-Q24.pv` | Q23–Q24 | 2 False (expected counterexamples) |
| Historical command compromise and future impersonation | `QUAD_Historical_Command_and_Impersonation_Attack_Model_Q25-Q26.pv` | Q25–Q26 | 2 False (expected counterexamples) |

The manuscript groups the verification objectives into **five categories implemented in six separate models**. The predefined compromise/impersonation category is split into Q23–Q24 and Q25–Q26. The four False results are **intentional attack counterexamples**, not security successes. The formal total is **22 True and four expected False**.

## 2. Independent event-reachability diagnostics (outside the 26 formal queries)

| Diagnostic model | Retained formal queries | Additional witness queries | Observed result |
|---|---:|---:|---|
| `QUAD_Handshake_Event_Reachability_Diagnostic_Q01-Q06.pv` | 6 True | 10 | 10 False, each with reconstructed execution trace |
| `QUAD_Rekey_Retransmission_Event_Reachability_Diagnostic_Q12-Q16.pv` | 5 True | 5 | 5 False, each with reconstructed execution trace |
| `QUAD_Post_M6_Disclosure_Event_Reachability_Diagnostic_Q17-Q22.pv` | 6 True | 4 | 4 False, each with reconstructed execution trace |

For these deliberately exposed witnesses, `not attacker(witness) is false` is evidence that the corresponding process point is reached; it is **not a formal confidentiality failure**. The retransmission diagnostic adds ProVerif `[precise]` annotations to six public-channel receives to assist trace reconstruction; it is separate from the unmodified formal retransmission model.

## 3. Contents and reproduction

- `01_Formal_Models/`: six official formal model sources.
- `02_Reachability_Diagnostics/`: three independent event-reachability models.
- `03_Formal_Verification_Logs/`: complete original execution outputs for all six formal models.
- `04_Event_Reachability_Logs/`: complete outputs with reconstructed witness traces for three diagnostics.
- `SHA256SUMS.txt`: SHA-256 fingerprints for integrity checking.

Extract the archive, open Windows PowerShell in its root directory, and run each `.pv` independently with ProVerif 2.05. Example:

`& 'D:\proverif\proverif2.05\proverif.exe' '.\01_Formal_Models\QUAD_Authenticated_Handshake_Model_Q01-Q06.pv'`

Compare each `RESULT` line with the corresponding `.log`. Q1–Q26 are manuscript-assigned identifiers based on the ordering of the six model files; they are not built-in ProVerif query IDs. The 26 original formal results were recorded in six separate runs, not in a single composed process run.

## 4. Scope and limitations

These are Dolev–Yao symbolic models with idealized primitives, not computational proofs of the underlying algorithms. The models are scenario-specific rather than one continuous end-to-end system: rekeying assumes a securely initialized previous epoch. Bounded retransmissions cover prescribed additional R1/R3 paths only. Post-M6 single-secret disclosure uses specified honest historical sessions and disclosure timing and **does not prove full forward secrecy**. Arbitrary retransmissions, real-time expiration, concurrent cache access, full sliding replay windows, old-key erasure, and implementation-level security are not all proven by these models.

ProVerif binaries, experimental C sources, manuscript drafts, and obsolete development variants are deliberately excluded.
