# How to set up `pyproject.toml` with Poetry

This guide is specifically for AstarVienna's packages, to ensure consistency.

## Basics

The following fields are mandatory:
```toml
[tool.poetry]
name = "mypackage"
version = "0.1.0"
description = "My awesome package."
authors = ["Kieran Leschinski <kieran.leschinski@unive.ac.at>"]
```

The following optional field can be defined directly below:
```toml
license = "GPL-3.0-or-later"
maintainers = [
    "Kieran Leschinski <kieran.leschinski@unive.ac.at>",
    "Hugo Buddelmeijer <hugo@buddelmeijer.nl>",
    "Fabian Haberhauer <fabian.haberhauer@univie.ac.at>",
]
readme = "README.md"
homepage = "https://mypackage.org/"
repository = "https://github.com/AstarVienna/mypackage_ipy"
documentation = "https://mypackage.readthedocs.io/en/latest/"
keywords = ["foo", "bar"]
```

Classifiers should follow [the standard](https://pypi.org/classifiers/), e.g.:
```toml
classifiers = [
    "Intended Audience :: Science/Research",
    "Operating System :: OS Independent",
    "Topic :: Scientific/Engineering :: Astronomy",
]
```
Note that things like license and python version are converted to classifiers internally in Poetry.



### Other info

Any further URLs not already listed above can be defined here, most notably a direct link to the issues page:
```toml
[tool.poetry.urls]
"Bug Tracker" = "https://github.com/AstarVienna/mypackage/issues"
```

TODO: add scripts, add plugins

### Build info

If done properly by use of `poetry new` or `poetry init`, this will be created automatically. No need to mess with it.

```toml
[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

## Dependencies

Package dependencies can be separated into "normal" dependencies (part of an invisible `main` group), group dependencies (optional and non-optional) and extra dependencies. This has different implications depending on how the package is installed.

### "Normal" dependencies and Python version

Any regular dependencies which are always needed by the package for basic operation become part of the implicit `main` group:

```toml
[tool.poetry.dependencies]
python = "^3.8"
numpy = "^1.24.4"
astropy = "^5.2.2"
```

Note that this is also where the required Python version is defined just like any other dependency. Poetry will take care of putting it correctly in the METADATA.

### Groups

Dependency groups are a way to organize dependencies internally for use with poetry. These are typically dependencies _not_ used in the package by the end user, but as part of the development / testing / building / deployment process. To define dependencies as part of a group we'll call `docs`, use:

```toml
[tool.poetry.group.docs.dependencies]
sphinx = "^5.3.0"
sphinx-rtd-theme = "^0.5.1"
jupyter-sphinx = "^0.2.3"
```

#### Important

Dependencies listed in groups can _only_ be installed using Poetry, not e.g. pip.

#### Optional groups

Groups can be optional or non-optional (the default). To make a group optional, add:

```toml
[tool.poetry.group.docs]
optional = true
```

This should by convention be placed _above_ the previous section that defines dependencies for the same group.

The difference between an optional and a non-optional group is simply that `poetry install` will install all non-optional dependencies (and those listed as "normal" dependencies), but not optional ones by default. This can be changed via e.g. `poetry install --with docs`, to install the `docs` group, or e.g. `poetry install --without test` to _not_ install the (non-optional) `test` group.

### Extras

#### Extra dependencies

Extras are dependencies that are not necessarily needed for basic operation of the package, but may be used for some advanced funcitonality. Extras are always opt-in, and never installed by default. To define a dependency as an extra, use the syntax from the following example:

```toml
synphot = { version = "^1.1.0", optional = true }
```

Importantly, this must be placed in the "normal" dependencies, not as part of a group!

#### Defining extras

To make extra dependencies installable (via Poetry, or any other tool like pip), they must be mentioned in an "extra":

```toml
[tool.poetry.extras]
syn = ["synphot"]
```

In this example, the extra `syn` is defined, which requires the synphot package, previously defined as an optional (extra) dependency. Note that this is a list, so multiple dependencies can be listed for an extra. Further note that any extra dependency can also be part of multiple extras.

#### Installing extras

Using Poetry, extras can be installed via `poetry install -E syn` (using the example from above), or via `poetry install --all-extras` to install all extras.

Using pip, the example from above can be installed via `pip install mypackage[syn]`.

### What to put where

As a rule-of-thumb, if it's something that's required for normal operation of the package's basic functionality, put it in the "normal" dependencies. If it's used only by some advanced users in a small number of use cases, it's more nuanced: If it's a simple, lightweight dependency, that isn't known for causing issues and doesn't in turn depend on a lot of (or heavyweight) other packages, it can be kept in the "normal" dependencies for simplicity. If on the other hand it is something that can be troublesome to install, or only works in certain environments, or in turn depends on a lot of other packages, it's probably better to put it in an extra.

Furthermore, if it's something that is only used for testing (e.g. pytest) or development (e.g. mypy) or documentation building (e.g. sphinx), but _not_ by the normal end user, it should be part of a dependency group. To furher differentiate those cases, if it's something that's used all the time in development (like e.g. pytest), it should be part of a non-optional group. However if it's only used in very special applications (like anything documentation-related), it should be in an optional group.

## Conventions

For AstarVienna's packages, the following conventions are used:

### Standard groups

#### `test`

Dependencies like pytest, pytest-cov, and anything else used for normal unit test, integration tests, etc. becomes part of the non-optional `test` group:
```toml
[tool.poetry.group.test.dependencies]
pytest = "^7.4.3"
pytest-cov = "^4.1.0"
```

#### `docs`

Everything related to documentation building becomes part of the optional `docs` group:
```toml
[tool.poetry.group.docs]
optional = true

[tool.poetry.group.docs.dependencies]
sphinx = "^5.3.0"
sphinx-rtd-theme = "^0.5.1"
jupyter-sphinx = "^0.2.3"
```
The name `docs` is fairly standardized for this and also what e.g. readthedocs [expects for use with Poetry](https://docs.readthedocs.io/en/stable/build-customization.html#install-dependencies-with-poetry) (although that can be modified).

#### `dev`

The `dev` group is reserved for any dev-only dependencies that are not part of the tests, but still might be used in the dvelopment process, e.g. mypy or pylint. It should be optional, unless it's very commonly used in development, and contains only trivial packages.

## Other tools

### Pytest

This section can be used for global configurations of Pytest, e.g.

```toml
[tool.pytest.ini_options]
addopts = "--strict-markers"
markers = [
    "webtest: marks tests as requiring network (deselect with '-m \"not webtest\"')",
]
filterwarnings = [
    "error",
    # Should probably be fixed:
    "ignore::ResourceWarning",
    "ignore:The fit may be poorly conditioned:astropy.utils.exceptions.AstropyUserWarning",
    # Raised when saving fits files, not so important to fix:
    "ignore:.*a HIERARCH card will be created.*:astropy.io.fits.verify.VerifyWarning",
]
```

This will:
- Create a pytest marker `webtest` that can be used as `@pytest.mark.webtest`.
- Treat all warnings as errors, _except_ the ones that match those listed explicity to be ignored.
(In this example, some common astropy warnings are ignored.)

### Coverage

To exclude the test from the coverage (which would always be 100% anyway), add:

```toml
[tool.coverage.report]
omit = ["mypackage/tests/*"]
```

If the `tests` folder is located somewhere else, the path has to be adjusted accordingly.