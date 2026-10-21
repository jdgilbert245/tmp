# PROJECT_NAME

TODO: one or two sentences on what this project is and what question it addresses.

## Contact

| | |
|---|---|
| Lead | TODO: name, email |
| PI | TODO: name, email |
| Group | TODO: |
| Started | TODO: YYYY-MM |
| Status | TODO: active / paused / complete / archived |

## Where the data lives

TODO: the path to the authoritative copy of the data, e.g. an RDSS project path
or a group storage location. Anyone picking this project up should be able to
find the data from this line alone.

Local copies under `data/` are working copies and are not tracked by git.

## How to run it

TODO: the sequence needed to reproduce the results from the raw data.

```
# example
Rscript code/01_clean.R
Rscript code/02_model.R
```

## Dependencies

TODO: what is needed to run the code, and how to restore it.

```
# example, if using renv
renv::restore()
```

The environment record is in `env/`.

## Layout

```
data/raw/        original data, read-only, never edited
data/processed/  generated from raw, safe to delete and regenerate
code/            scripts, numbered in run order
results/         figures and tables, generated, safe to delete and regenerate
docs/            protocols, notes, decisions
env/             record of the software environment
```

## Notes

TODO: anything a person taking this over would need to know and would not
work out from the code.
