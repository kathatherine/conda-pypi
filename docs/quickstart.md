# Quickstart

## Installation

`conda-pypi` is a `conda` plugin that is available in your `base`
environment in conda versions 26.5 and newer.

Update your conda installation to get `conda-pypi`:

```bash
conda update conda
```

You can also install the plugin directly into your `base` environment:

```bash
conda install --name base conda-pypi
```

Once installed, the `conda pypi` subcommand becomes available across all your
conda environments.

## Set up the community wheel channel

The conda-pypi community wheel channel is a public channel on anaconda.org
that makes pure Python packages from PyPI available through `conda install`.
When you add this channel, conda's solver can find and install these packages
alongside your regular conda packages in a single step.

To enable the conda-pypi channel, configure the Rattler solver, add the channel, and
reset channel priority to its default (flexible):

```bash
conda config --set solver rattler
conda config --append channels conda-pypi
conda config --set channel_priority flexible
```

With this configuration, `conda install` can resolve dependencies across both
regular conda packages and wheel packages in a single solve. When a wheel
package is selected, conda downloads the artifact directly from PyPI and
installs it into the environment while tracking it like any other conda
package.

:::{admonition} Beta
:class: warning
The community wheel channel is in public beta. It hosts pure Python wheels
only. Compiled wheels are not supported. The security posture is the same as
installing from public PyPI. For more details, see {ref}`community-wheel-channel`.
:::

## Basic usage

`conda-pypi` provides several {doc}`features`. The main functionality is
accessed through the `conda pypi` command:

### Installing PyPI packages

:::{note}
These instructions assume that you have done the following:

- Created and activated a conda environment
- Installed `python` and `pip` into that conda environment
- Added the `conda-pypi` channel to your `.condarc` file
- Configured your solver to be the rattler solver
:::

Use `conda install` to install a package (for example, `niquests`):

```bash
conda install niquests
```

This will download and unpack `niquests` from PyPI and
install it as a native wheel (`.whl`) file.
The dependencies of `niquests` will be installed from
the conda channel when available. For example, if `niquests` depends on
`urllib3` and `certifi`, and both are available on the conda channel, those
dependencies will be installed from conda rather than PyPI.

```bash
conda install build
```

This will download and unpack the `build` package from PyPI and
install it as a native wheel (`.whl`) file.
Even though `python-build` exists on conda, the explicitly requested
package always comes from PyPI to ensure you get exactly what you asked for.
However, its dependencies will preferentially come from conda channels when
available.

```bash
conda install some-package-with-many-deps
```

Here's where the hybrid approach really shines:
`some-package-with-many-deps` itself will be installed from PyPI, but
conda-pypi will analyze its dependency tree and:
- Install dependencies like `numpy`, `pandas`, etc. from the conda channel (if
  available)
- Install only the dependencies that aren't available on conda channels from
  PyPI

```bash
conda install --ignore-channels some-package
```

This command forces dependency resolution to use only PyPI, bypassing conda channel
checks for dependencies. The requested package is always installed from PyPI
regardless of this flag.

### Converting packages without installing

You can also convert PyPI packages to `.conda` format without installing
them:

```bash
# Convert to current directory
conda pypi convert niquests rope

# Convert to specific directory
conda pypi convert -d ./my_packages niquests rope
```

This is useful for creating conda packages from PyPI distributions or
preparing packages for offline installation.

### Development and editable installations

`conda-pypi` supports editable installations for development workflows:

```bash
# Install local project in editable mode
conda pypi install -e ./my-project/

# Preview what would be installed
conda pypi install --dry-run niquests pandas
```

### Environment protection

`conda-pypi` ships a special file called `EXTERNALLY-MANAGED` that helps
protect your conda environments from accidental pip usage that could break
their integrity. This file is automatically installed in the `base`
environment, all new environments, and existing environments that after running
a `conda pypi` command on them.

More details about this protection mechanism can be found at
{ref}`externally-managed`.
