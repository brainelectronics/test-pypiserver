# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!--
## [x.y.z] - yyyy-mm-dd
### Added
### Changed
### Removed
### Fixed
-->
<!--
RegEx for release version from file
r"^\#\# \[\d{1,}[.]\d{1,}[.]\d{1,}\] \- \d{4}\-\d{2}-\d{2}$"
-->

## Released
## [0.2.0] - 2026-04-14
### Added
- [`.yamllint`](.yamllint) and [`.pre-commit-config.yaml`](.pre-commit-config.yaml) files

### Changed
- Update `pypiserver` to `v2.4`
- Fix yamllint suggestions
- Update root [`README`](README.md) with helpful PyPI server endpoint links

## [0.1.0] - 2022-10-22
### Added
- This changelog file
- [`.gitignore`](.gitignore) file respecting custom content in `data` folder
- [`docker-compose.yml`](docker-compose.yml) file for the PyPi server
- [`requirements.txt`](requirements.txt) file
- Initial root [`README`](README.md)

<!-- Links -->
[Unreleased]: https://github.com/brainelectronics/test-pypiserver/compare/0.2.0...main

[0.2.0]: https://github.com/brainelectronics/test-pypiserver/tree/0.2.0
[0.1.0]: https://github.com/brainelectronics/test-pypiserver/tree/0.1.0

<!-- [ref-issue-1]: https://github.com/brainelectronics/test-pypiserver/issues/1 -->
