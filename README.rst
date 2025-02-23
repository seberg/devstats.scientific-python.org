Scientific Python Development and Community Statistics
======================================================

.. warning::
   This project is not yet stable - the commit history on ``main`` will likely
   be altered --- bear this in mind when forking. This warning will be removed
   when the history modification is complete.

A static site for collating & publicizing development and community statistics
for Scientific Python projects.

Installation
------------

```python
python -m pip install -r requirements.txt
```

Building the site
-----------------

To get the data used in the analysis:

```bash
git submodule update --init
```

Then the site can be built with:

```bash
make html
```

Devstats data
-------------

The data used for the officially deployed site is available at
<https://github.com/scientific-python/devstats-data>.
The data repository is included as a submodule in this project, so updating the
submodule is all that is required to ensure you are using the latest data for
the published site.
