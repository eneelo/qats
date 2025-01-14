## Changelog
All notable changes to the project will be documented in this file, with format based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

We apply the *"major.minor.micro"* versioning scheme defined in [PEP 440](https://www.python.org/dev/peps/pep-0440).


<!-- Notes:
- Header links are defined at the bottom of this markdown file.
- Versions are level 3 headings (###) (no difference between major/minor/micro versions)
- Change categories are level 4 headings (####).
- The following change categories should be used (and they should be grouped): 
  Added, Changed, Deprecated, Removed, Fixed.
  For details and guidance, see: https://keepachangelog.com/en/1.0.0/#how
-->


### [Unreleased]

Click link to see all [unreleased] changes to the master branch of the repository. 
For comparison to specific branches, use the [GitHub compare](https://github.com/dnvgl/qats/compare) page.

### [4.6.0] // 14.11.2019

#### Added
New reader for SINTEF Ocean data exchange format based on Matlab .mat files. It now supports all .mat file format versions <=7.3.

`TsDB` has a new attribute `uuid` with a universally unique identifier.

`TsDB` now supports length, membership and equality operations like:
 ```python
 db = TsDB.fromfile('data.csv')
 db_two = TsDB()
 len(db)
 14
 'ts_a_name' in db
 True
 db_two == db
 False
 ```

#### Changed

The file format previously referred to as Matlab files is now more precisely described as the data exchange format used by SINTEF Ocean. The submodule `qats.readers.matlab` has been renamed to `qats.readers.sintef_mat`. _Note: for backwards compatibilty, the submodule is still available as `qats.readers.matlab` due to aliased import at `qats.readers` level._

The `__repr__` and `__str__` methods on the `TsDB` class are improved to better serve their indented purposes, respectively: unambiguous and readable.

Previously iteration of a `TsDB` instance would return `None` for those time series which data was **not** loaded from file. Now iteration return `TimeSeries` instances for those time series which data has been loaded from file and skip the other.

Updated the package and environment requirements.

#### Fixed
Prevent error raised when the env. variable `APPDATA` does not exist.

### [4.5.0] // 05.11.2019

#### Added

File menu action that clears the logger widget. Hot key `Ctrl+Shift+Del`.

#### Fixed

A bug that caused the time series tooltip to only display the time series' relative path, not the absolute path.


### [4.4.0] // 04.11.2019

#### Added

`qats.fatigue.rainflow` module:

- Introduced a new optional parameter `endpoints` (bool) to functions `reversals()`, `cycles()` and `cycle_counting()`. If set to True, first and last points in series passed are included as cycle start/end points. This is convenient if the series passed is already an array of reversal points, but should in general not be used otherwise. Default is `endpoints=False`, which gives the same behavior as before.

`.plot_*()` methods of `TimeSeries` and `TsDB` classes (for background and more details, see [issue #55](https://github.com/dnvgl/qats/issues/55)):

- Introduced optional parameter `num`, to control `matplotlib.pyplot` figure number.
- Introduced optional parameter `show` (bool), to control whether `pyplot.show()` is invoked or not.
- `.plot_cycle_range()`: introduced optional parameter `bw`, to control bar width.

#### Fixed

- `TimeSeries.stats()`: `np.nan` is inserted for statistical values that cannot be calculated (e.g. Weibull parameters if fit fails).
- Desktop application (GUI), statistics tab: fixed issues related to clearing the table, updating when Weibull fit fails and mismatch between values and table heading. For details, see [issue #58](https://github.com/dnvgl/qats/issues/58).


### [4.3.0] // 30.10.2019

#### Added
The desktop application now:
- Has a separate tab with tabulated time series statistics. The statistics can be copied to clipboard.
- Enable user configuration of certain settings. The settings are saved between sessions.

#### Changed
The library now uses Scipy IIR filters instead of the self-made filters. Mainly to improve handling of edge effects and to simplify maintenance.

Restructured the functions for calculating power spectral density. *Note: The desktop application now apply a default segment length of 1000 points when estimating power spectral density using Welch's method. This can be adjusted in the application settings dialog. Previously the default segment length was 1/4 of the signal length.*

### [4.2.0] // 23.09.2019

#### Changed
Replaced hard dependency on `PyQt5` with `QtPy`, due to `PyQt5`'s strong copyleft GPL license. `QtPy` is a shim over various Qt bindings such as: `PyQt4`, `PyQt5`, `Pyside` and `Pyside2`. The user must now install the preferred binding himself/herself, in order to use the GUI.

Note: If several qt bindings are installed (e.g. `PyQt5` and `Pyside2`), the environmental variable `QT_API` may be used to control which binding is used. See https://pypi.org/project/QtPy/ for details.

### [4.1.1] // 17.09.2019

#### Fixed
- `qats.signal.psd()`: sampling frequency `fs` in the [welch function call](https://github.com/dnvgl/qats/blob/3378ea5972f1cd56a23a902397d195f03c0f8db2/qats/signal.py#L692) corrected to `fs=1./dt` (instead of `fs=dt` which was wrong). This error appeared between versions [4.0.1 and 4.1.0](https://github.com/dnvgl/qats/compare/4.0.1...4.1.0), and also affected `TimeSeries.psd()` and the "Power Spectrum" plot tab in the GUI. 
- `TimeSeries.plot*()` methods: plot label is now set to time series name, to avoid warning when legends are invoked.

#### Added
Four new tests on `qats.signal.psd()` that would have caught the bug described above.

### [4.1.0] // 28.08.2019

#### Added
- `qats.signal.psd()`: power spectral density calculation (now available as detached function, was previously available only through `TimeSeries.psd()`).
- `test_readers.py`: new test module for loading (reading) all supported file formats.
- `test_signal.py`: added more tests for `qats.signal` functions.


### [4.0.1] // 23.08.2019

#### Fixed
- SIMA .h5 reader bug that occured if numpy 1.16 is used.
- `qats.cli.launch_app()` did not connect `sys.excepthook` with custom error traceback dialogue.


### [4.0.0] // 22.08.2019

This release is not backwards compatible. Main updates are related to fatigue calculation capabilities.

#### Added
- New sub-package `qats.fatigue` for fatigue-related modules. 
  Modules in this sub-package: `corrections` (new), `rainflow` (moved), `sncalc` (new)
- New sub-package `qats.stats` for modules with statistical distributions. 
  Modules in this sub-package: `empirical` (moved + new name), `gumbel` (moved), `gumbelmin` (moved), `weibull` (moved)
- Rainflow counting algorithm now also extracts mean value of cycles (see `qats.fatigue.rainflow.cycles()` and `.count_cycles()`).
- Class method `TsDB.getl()` ("get list").
- New module `motions.py`, for transformations and operations related to motion time series.
- Changelog (this file)

#### Changed
- Sub-module `rainflow` moved into sub-package `qats.fatigue` => `qats.fatigue.rainflow`
- Sub-modules `gumbel`, `gumbelmin` and `weibull` moved into sub-package `qats.stats` => `qats.fatigue.`
- Sub-module `stats` renamed to `empirical` and moved into sub-package `qats.stats` => `qats.stats.empirical`
- Documentation updated with new API reference structure.
- Changed `TsDB.getd()` method ("get dict") to return dict of `TimeSeries` objects, which is identical to `TsDB.getm()`. 
  For previous functionality of `.getd()`, use `TsDB.getda()` ("get dict of arrays").

#### Fixed
- Wildcard prepending to names specified to `TsDB.list()` and `TsDB.get*()` methods. 
  See [this summary](https://github.com/dnvgl/qats/pull/28#issue-296526900) for details.


### [3.0.6] // 2019-06-27

#### Fixed
- Issues related to proper deployment to PyPI and Read the Docs.
- Bug when using band-stop filter in GUI.


### [3.0.5] // 2019-06-26
First proper release to [PyPI](https://pypi.org/project/qats/).



<!-- Links to be defined below here -->

[Unreleased]: https://github.com/dnvgl/qats/compare/4.6.0...HEAD
[4.6.0]: https://github.com/dnvgl/qats/compare/4.5.0...4.6.0
[4.5.0]: https://github.com/dnvgl/qats/compare/4.4.0...4.5.0
[4.4.0]: https://github.com/dnvgl/qats/compare/4.3.0...4.4.0
[4.3.0]: https://github.com/dnvgl/qats/compare/4.2.0...4.3.0
[4.2.0]: https://github.com/dnvgl/qats/compare/4.1.1...4.2.0
[4.1.1]: https://github.com/dnvgl/qats/compare/4.0.1...4.1.1
[4.1.0]: https://github.com/dnvgl/qats/compare/4.0.1...4.1.0
[4.0.1]: https://github.com/dnvgl/qats/compare/4.0.0...4.0.1
[4.0.0]: https://github.com/dnvgl/qats/compare/3.0.6...4.0.0
[3.0.6]: https://github.com/dnvgl/qats/compare/3.0.5...3.0.6
[3.0.5]: https://github.com/dnvgl/qats/compare/3.0.0...3.0.5
