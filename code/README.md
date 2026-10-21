# code

Scripts, numbered in the order they run:

```
01_clean.R
02_model.R
03_figures.R
```

Zero-pad the numbers if the project will have more than nine steps.

Use paths relative to the root of the project, not absolute paths. In R,
`here::here("data", "raw", "file.csv")`; in Python, paths built from the project
root rather than starting `C:/Users/` or `/home/`.
