# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/)
and this project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html),
except that the `patch` number is omitted.

<!--

These are the categories you should try to conform your
changelog entries to:

### Added
### Changed
### Deprecated
### Removed
### Fixed
### Security

-->

## [Unreleased]
### Added
### Changed
- The `private_key` schema no longer requires the `fingerprint` property, as the
	fingerprint requires an identity container to be created, and I didn't want
	to tie the two things together so strongly.
- The version number of the specs now includes only major and minor. Patches are
	not included in the version numbering.
- As part of Issue #51 the container specs were greatly reworked. Container
	extension data is now held in properties named the same as the value
	in the `sdmp.schemas` list.

### Deprecated
### Removed
### Fixed
- Fixed a handful of broken links.
- Some spelling and minor document fixes by @TehShrike
- Correction to 0.12 private key size (was 2056, should have been 2048)

### Security
