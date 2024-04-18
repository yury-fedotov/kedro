# Using custom code in a Kedro project

## Context

...

## Common use cases

### Developing utilities to be shared between pipelines

Oftentimes you have utilities that have to be imported by multiple `pipelines`.
To keep them as part of a Kedro project, **create a module (e.g. `utils`) at the same
level as the `pipelines` folder**, and organize the functionalities there.

```text
├── conf
├── data
├── notebooks
└── src
    ├── my_project
    │   ├── __init__.py
    │   ├── __main__.py
    │   ├── pipeline_registry.py
    │   ├── settings.py
    │   ├── pipelines
    │   │   ├── __init__.py
    │   │   ├── first_pipeline
    │   │   ├── ...
    │   │   ├── nth_pipeline
    │   └── utils                       <-- Create a module to store your `utils`
    │       ├── __init__.py             <-- Required to import from it
    │       ├── pandas_utils.py         <-- Locate a file with utility functions here
    │       ├── dictionary_utils.py     <-- Or a few files
    │       ├── visualization_utils     <-- Or sub-modules to organize even more utilities
    └── tests
```

Example of importing a function `find_overlapping_keys` from `dictionary_utils.py` would be:

```python
from my_project.utils.dictionary_utils import find_overlapping_keys
```

NOTE: pip install!

### Using Kedro to produce an artifact used in other apps / packages

A common use case of Kedro is that a software product built by a team has components that
are logically independent of the Kedro project.

Let's use _a recommendation tool for equipment operators_ as an example. It would imply three parts:
* An ML model, or more precisely, a workflow to prepare the data, train an estimator, ship it to registry
* An optimizer that leverages the ML model and implements business logic to derive recommendations
* An application serving as a user interface: this can be e.g., a `plotly` or `streamlit` dashboard

A suggested solution in this case would be a **monorepo** design. Below is an example:

```text
└── repo_root
    ├── packages
    ├── requirements.txt               <-- Linters, code formatters... Not dependencies of packages.
    │   ├── __init__.py
    │   ├── __main__.py
    │   ├── pipeline_registry.py
    │   ├── settings.py
    │   ├── pipelines
    │   │   ├── __init__.py
    │   │   ├── first_pipeline
    │   │   ├── ...
    │   │   ├── nth_pipeline
    │   └── utils                       <-- Create a module to store your `utils`
    │       ├── __init__.py             <-- Required to import from it
    │       ├── pandas_utils.py         <-- Locate a file with utility functions here
    │       ├── dictionary_utils.py     <-- Or a few files
    │       ├── visualization_utils     <-- Or sub-modules to organize even more utilities
    └── tests
```

### Importing another library of yours into a Kedro project
