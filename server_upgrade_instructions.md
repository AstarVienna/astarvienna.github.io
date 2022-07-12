# July 2022: Server URL Upgrade instructions

Due to the downloadable content server being shut down in July 2022, the hard-coded URLs for the download functions in each of following packages no longer point to the correct place.

## Affected functions and classes

- scopesim

  - ``scopesim.download_packages()``

- scopesim_templates

  - ``scopesim_templates.galaxies.spiral_two_component()``

- spextra

  - ``spextra.Spextrum``
  - ``spextra.Database``
  - ``spextra.Config``

- pyckles

  - ``pyckles.utils.get_catalog_list()``
  - ``pyckles.utils.load_catalog()``

The easiest way to fix this is to upgrade to the latest version of the package via ``pip``.

    pip install --upgrade <pkg_name>

However if your system requires the use of an older version of any of the packages listed above, you can keep these running by updating the URL in the relevant package config file.

The installation directory for each package (``<pkg_name>``) can be found with the following code::

    >>> import <pkg_name>
    >>> print(<pkg_name>.__path__)

For example::

    >>> import scopesim
    >>> print(scopesim.__path__)
    ['F:\\Work\\ScopeSim\\scopesim']

The config files listed below can be found relative to the root directory of the package, as printed by the code above.
The offending entry in the config file should be replaced by the line of code given below by ``new entry``.

### ScopeSim

- affected versions: ``scopesim <= v0.5.0``
- config file: ``scopesim/defaults.yaml``
- new entry:

      file :
        server_base_url : "https://scopesim.univie.ac.at/InstPkgSvr/"

### ScopeSim_templates

- affected versions: ``scopesim_templates <= v0.4.1``
- config file: ``scopesim_templates/defaults.yaml``
- new entry:

      file :
        server_url : "https://scopesim.univie.ac.at/scopesim_templates/"

### SpeXtra

- affected versions: ``spextra <= v0.24``
- config file: ``spextra/data/config.yml``
- new entry:

      database_url: https://scopesim.univie.ac.at/spextra/database/

### Pyckles

- affected versions: ``pyckles <= v0.1``
- config file: ``pyckles/utils.py``
- new entry:

      SERVER_URL = "https://scopesim.univie.ac.at/pyckles/"

## Contact

If you have question regarding the changes, or continue to find bugs with the downloadable content server, please contact tha A* Team at [astar.astro@univie.ac.at](astar.astro@univie.ac.at)