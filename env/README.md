# env

The record of what is needed to run the code.

| Language | File | How to write it | How to restore it |
|---|---|---|---|
| R | `renv.lock` | `renv::snapshot()` | `renv::restore()` |
| Python (pip) | `requirements.txt` | `pip freeze > env/requirements.txt` | `pip install -r env/requirements.txt` |
| Python (conda) | `environment.yml` | `conda env export > env/environment.yml` | `conda env create -f env/environment.yml` |

Write it when the analysis works, not when the paper is submitted.
