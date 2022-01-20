---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.13.6
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# NumPy Monthly Report

```{code-cell} ipython3
---
tags: [remove-cell]
---

import json
import functools
import datetime
import warnings

import numpy as np
import matplotlib.pyplot as plt
from myst_nb import glue

glue = functools.partial(glue, display=False)

warnings.filterwarnings(
    "ignore", category=DeprecationWarning, message="parsing timezone")
```

%TODO improve handling of datetimes (super annoying)

A snapshot of the development on the NumPy project in the past month

## Issues

%TODO: query_date should be synced up with the query that generates data, rather
%than specified manually

```{code-cell} ipython3
query_date = np.datetime64("2022-01-01 00:00:00")

# Load data
with open("../_data/issues.json", "r") as fh:
    issues = [item["node"] for item in json.loads(fh.read())]

glue("query_date", str(query_date))
```

### New issues

%TODO: should probably use pandas for this

```{code-cell} ipython3
---
tags: [hide-input]
---

newly_created = [
    iss for iss in issues if np.datetime64(iss["createdAt"]) > query_date
]
new_issues_closed = [iss for iss in newly_created if iss["state"] == "CLOSED"]

new_issue_lifetime = np.array(
    [
        np.datetime64(iss["closedAt"]) - np.datetime64(iss["createdAt"])
        for iss in new_issues_closed
    ],
).astype("m8[h]")  # in hours

glue("num_new_issues", len(newly_created))
glue(
    "num_new_issues_closed",
    f"{len(new_issues_closed)} ({100 * len(new_issues_closed) / len(newly_created)}%)"
)
glue("new_issue_avg_lifetime", f"{np.mean(new_issue_lifetime)}")
```

{glue:text}`num_new_issues` new issues have been opened since
{glue:text}`query_date`, of which {glue:text}`num_new_issues_closed` have been
closed.

The average lifetime of new issues that were created and closed in this period
is {glue:text}`new_issue_avg_lifetime`.

% TODO: replace with bokeh or some other live-plot
% TODO: for any remaining static/mpl plots, set default params for things
% like fontsize in a mplstyle file.

```{code-cell} ipython3
---
tags: [hide-input]
---
fig, ax = plt.subplots(figsize=(16, 12))
ax.hist(new_issue_lifetime.astype("m8[D]").astype(int), bins=np.arange(30))
ax.set_title(
    f"Lifetime of issues created and closed in the last "
    f"{(np.datetime64(datetime.datetime.now()) - query_date).astype('m8[D]')}",
    fontsize=24
)
ax.set_xlabel("Issue lifetime (days)", fontsize=20)
ax.set_ylabel(r"$\frac{issues}{day}$", fontsize=20);
```