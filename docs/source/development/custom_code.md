# Adding code beyond starter files

After you [create a Kedro project](../get_started/new_project.md) and
[add a pipeline](../tutorial/create_a_pipeline.md), you notice that Kedro generates a
few boilerplate files: `nodes.py`, `pipeline.py`, `pipeline_registry.py`...

While those may be sufficient for a small project, they quickly become large, hard to
read and challenging to collaborate on as your codebase grows.
Those files also sometimes make new users think that Kedro framework requires code
to be located only in those starter files, which is not true.

This section elaborates what are the Kedro requirements in terms of organizing code
in files and modules.
It also provides examples of common scenarios such as sharing utilities between
pipelines and using Kedro in a monorepo setup.

## Common scenarios

### Sharing utilities between pipelines

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
    │   └── utils                       <-- Create a module to store your `utils`
    │       ├── __init__.py             <-- Required to import from it
    │       ├── pandas_utils.py         <-- Locate a file with utility functions here
    │       ├── dictionary_utils.py     <-- Or a few files
    │       ├── visualization_utils     <-- Or sub-modules to organize even more utilities
    └── tests
```

Example of importing a function `find_common_keys` from `dictionary_utils.py` would be:

```python
from my_project.utils.dictionary_utils import find_common_keys
```

```{note}
For imports like this to be displayed in IDE properly, it is required to perform an editable
installation of the Kedro project to your virtual environment.
This is done via `pip install -e <root-of-kedro-project>`, the easiest way to achieve
which is to `cd` to the root of Kedro project and do `pip install -e .`.
```

### Kedro project in a monorepo setup

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
    │   ├── kedro_project
    │   │   ├── conf
    │   │   ├── data
    │   │   ├── notebooks
    │   │   ├── ...
    │   ├── optimizer                   <-- Standalone package.
    │   └── dashboard                   <-- Standalone package, may import `optimizer`
    ├── requirements.txt                <-- Linters, code formatters... Not dependencies of packages.
    ├── pyproject.toml                  <-- Settings for those, like `[tool.isort]`.
    └── ...
```

### Importing another library of yours into a Kedro project

Examples:
* You made a package that traces data lineage in complex schemas, and want to use Kedro
  to prepare tens of schemas, run them through the package, and see the accuracy.
