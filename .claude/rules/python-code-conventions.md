---
paths:
  - "**/*.py"
  - "**/*.ipynb"
  - "scripts/**/*.py"
  - "notebooks/**/*.ipynb"
---

# Python Code Standards

**Standard:** Senior Principal Data Engineer + PhD researcher quality

---

## 1. Reproducibility

- `random.seed()` and/or `np.random.seed()` called ONCE at top of script
- All imports at top, grouped: stdlib → third-party → local
- All paths relative to repository root (use `pathlib.Path`)
- `Path(...).mkdir(parents=True, exist_ok=True)` for output directories
- `requirements.txt` or `pyproject.toml` for dependencies

## 2. Function Design

- `snake_case` naming, verb-noun pattern
- Type hints on all function signatures
- Docstrings (Google or NumPy style) for non-trivial functions
- Default parameters, no magic numbers
- Return typed objects (dataclasses, NamedTuples, or dicts with documented keys)

## 3. Domain Correctness

<!-- Customize for your field's known pitfalls -->
- Verify estimator implementations match slide formulas
- Check known package bugs (document below in Common Pitfalls)

## 4. Visual Identity

```python
# --- Project palette ---
PRIMARY_BLUE   = "#012169"
PRIMARY_GOLD   = "#f2a900"
ACCENT_GRAY    = "#525252"
POSITIVE_GREEN = "#15803d"
NEGATIVE_RED   = "#b91c1c"
```

### Custom Theme

```python
import matplotlib.pyplot as plt

def set_project_theme(font_size: int = 14) -> None:
    """Apply project visual identity to matplotlib."""
    plt.rcParams.update({
        "font.size": font_size,
        "axes.titleweight": "bold",
        "axes.titlecolor": PRIMARY_BLUE,
        "axes.labelcolor": ACCENT_GRAY,
        "legend.loc": "lower center",
        "figure.facecolor": "none",
        "axes.facecolor": "none",
        "savefig.facecolor": "none",
    })
```

### Figure Dimensions for Beamer

```python
fig.savefig(filepath, dpi=300, transparent=True, bbox_inches="tight")
# Default size: figsize=(12, 5) for full-width slides
```

## 5. Data Persistence

**Heavy computations saved as parquet/pickle; slide rendering loads pre-computed data.**

```python
import pandas as pd

df.to_parquet(output_dir / "descriptive_name.parquet")
# Or for non-DataFrame objects:
import pickle
with open(output_dir / "model_results.pkl", "wb") as f:
    pickle.dump(result, f)
```

## 6. Common Pitfalls

<!-- Add your field-specific pitfalls here -->
| Pitfall | Impact | Prevention |
|---------|--------|------------|
| Missing `transparent=True` | White boxes on slides | Always include in savefig() |
| Hardcoded paths | Breaks on other machines | Use `pathlib.Path` with relative paths |
| `import *` | Namespace pollution, unclear dependencies | Always use explicit imports |
| Missing `random.seed()` | Non-reproducible results | Set once at script top |

## 7. Line Length & Mathematical Exceptions

**Standard:** Keep lines <= 88 characters (Black default).

**Exception: Mathematical Formulas** -- lines may exceed 88 chars **if and only if:**

1. Breaking the line would harm readability of the math (influence functions, matrix ops, formula implementations matching paper equations)
2. An inline comment explains the mathematical operation:
   ```python
   # Sieve projection: inner product of residuals onto basis functions P_k
   alpha_k = np.sum(r_i * basis[:, k]) / np.sum(basis[:, k] ** 2)
   ```
3. The line is in a numerically intensive section (simulation loops, estimation routines)

**Quality Gate Impact:**
- Long lines in non-mathematical code: minor penalty (-1 to -2 per line)
- Long lines in documented mathematical sections: no penalty

## 8. Code Quality Checklist

```
[ ] Imports at top, grouped (stdlib → third-party → local)
[ ] Seeds set once at top (random, numpy, torch if applicable)
[ ] All paths relative (pathlib.Path)
[ ] Functions have type hints + docstrings
[ ] Figures: transparent bg, explicit dimensions, project theme
[ ] Data persistence: every computed result saved (parquet/pickle)
[ ] Comments explain WHY not WHAT
[ ] No wildcard imports
```
