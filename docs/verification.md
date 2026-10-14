# Executed verification — October 13, 2026

Version 0.1.0 is locally verified. GitHub publication and remote CI are pending authorization.
[Machine-readable evidence](evidence/verification-summary.json) records suite/source hashes,
parsed JUnit counts, benchmark scores, and the deliberate regression results.

| Environment | Installation | Result |
| --- | --- | --- |
| Windows, Python 3.14.6 | Fresh virtual environment, regular wheel, pinned requirements | 30 passed; zero failures/errors/skips |
| Linux, Python 3.12 | Built Docker image, regular wheel, read-only source mount | 30 passed; zero failures/errors/skips |

Linux tests ran as the image's non-root user, with network disabled, a read-only root filesystem,
all capabilities dropped, and temporary writable memory filesystems. The final JUnit XML was
captured before container exit. Ruff lint/format checks, dependency consistency (`pip check`),
installed CLI, container build, and the offline container baseline also passed.

The 14-case baseline passes all ten dimensions. Recovery has three eligible fault cases;
the other dimensions each have 14. There are two intentionally denied policy probes and no
unauthorized effects. The mock baseline consumes zero model tokens and zero model API cost.
Four deliberately faulty candidates — wrong tool, wrong arguments, missing recovery, and incorrect
verification claims — each fail evaluation and comparison gates. Tests also demonstrate budget
violations, malformed/duplicate input, contradictory reports, missing model usage, trace correlation,
and preservation of versioned results. These are fixture acceptance results, not deployment claims.

Commands used included:

```text
python -m pip install -r requirements.lock .
python -m pytest -q --junitxml=.evaluation/tests-clean-windows.xml
python -m pip check
ruff check .
ruff format --check .
python scripts/demo.py
docker build -t agent-evaluation-harness:local .
docker run --rm --network none agent-evaluation-harness:local
```

The full Linux suite was invoked through the image's Python against `/verification/tests` using
a read-only repository mount and `-p no:cacheprovider`. An initial attempt to refresh the Windows
wheel with build isolation disabled lacked setuptools; the declared isolated build was then used
successfully. An initial JUnit copy from a stopped tmpfs-backed container found no file; final
evidence was captured within the running container and parsed successfully. Neither failed
verification command is represented as a passing check.

CI is configured but no remote outcome is claimed. Future publication should verify all seven
jobs against the exact published commit and inspect uploaded artifacts before marking delivery complete.
