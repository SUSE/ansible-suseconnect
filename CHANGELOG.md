Changelog
=========

[1.0.3-suse] - 2026-11-08
--------------------

### New Features

- feat: add meta/argument_specs.yml (#11)

### Bug Fixes

- fix: Pick transactional-update register by the system, not its name (#12)
- fix: Support SLES 16.1 Immutable and run without injected fact vars (#13)
- fix: mask the registration code in the registration failure message (#14)

### Other Changes

- docs: Add an installation example to the README (#15)

[1.0.2-suse] - 2026-07-15
--------------------

### Bug Fixes

- fix: assert required package present, run status query in check mode
- fix: honor system proxy config
- fix: detect azure via chassis asset tag (#4)

### Other Changes

- Add meta file
- docs: add RMT reference and fix typo
- ci: lint on push/PR, fix meta schema/role-name (#5)
- docs: drop unused var and correct readme (#6)

[1.0.1-suse] - 2025-06-24
--------------------

### Bug Fixes

- Simplified sanitization logic to work without jmespath

[1.0.0-suse] - 2024-12-13
--------------------

### New Features

- Initial SUSE release: registration, modules and extensions, deregistration, public cloud detection
