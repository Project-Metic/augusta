# CLAUDE.md — augusta

Augusta: Ada/SPARK verification tooling and test framework used by Project-Metic as a
formal verification component for Ada-based safety-critical software in the binary analysis
pipeline. Works alongside `spark2014` for SPARK 2014 / GNATprove-based verification.

## What This Repo Is

Ada/SPARK verification tooling — used by Project-Metic to analyze and formally verify
Ada programs before binary lifting. Augusta provides test infrastructure and verification
harnesses for the SPARK 2014 proof pipeline.

## Key Invariants

- **SPARK proof obligations must be fully discharged.** Any Ada code with SPARK annotations
  that passes through the Metic pipeline must have all GNATprove proof obligations discharged
  (zero unproved VCs). Unproved VCs must be marked UNVERIFIED, not silently dropped.
- **Flow analysis and proof are separate gates.** GNATprove flow analysis (information flow,
  initialization) must pass before proof obligations are dispatched. Do not skip flow analysis.
- **Ada source integrity.** Never modify Ada source in a way that silently removes SPARK
  annotations (`with SPARK_Mode => On`). Removing annotations bypasses verification.

## Dev Commands

See `spark2014` for the GNATprove build and test commands. Augusta-specific:

```bash
# Run Augusta test framework
gprbuild -P augusta_tests.gpr
./run_tests.sh

# Run with GNATprove (requires SPARK toolchain)
gnatprove -P project.gpr --mode=all
```

## Tech Stack

- Ada 2012, SPARK 2014, GNATprove, GPRbuild

## Integration with Project-Metic

- Ada source code lifted via McSema/remill after SPARK verification
- SPARK VCs feed into Infera as proof obligations (when Lean 4 backend is used)
- Augusta test results contribute to the binary analysis test vector corpus

## What NOT to Do

- Do not skip flow analysis before dispatching proof obligations
- Do not remove SPARK annotations to make code compile without verification
- Do not treat unproved GNATprove VCs as verified
