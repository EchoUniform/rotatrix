# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

- Work in progress for future releases

### Planned / Coming soon

- **Advanced parameterization**
  Allow rotation values (frequency, rotate, maxage, minsize, etc.) to be defined by variables and Jinja2 templates (no more fixed config files)

- **CI/CD & Molecule**
  Integrate Molecule and GitHub Actions for automated cross-platform testing

- **Multi-distro support**
  Test and extend support to RHEL 8, Rocky/AlmaLinux, CentOS Stream, and optionally Debian/Ubuntu

- **Custom log policies**
  Make it easy for users to add their own log files and rotation policies without code changes

- **Optional handlers**
  Add handlers to restart/reload services (like rsyslog) after config changes, controlled by variable

- **Verification tasks**
  Add post-deploy verification to ensure all configs and permissions are applied as expected

- **Docs & examples**
  Expand documentation with more examples, troubleshooting, and monitoring/alerting integration tips

## [0.1.1] - 2025-07-15

### Added

- English translations for README and meta/main.yml description to support internationalization and Galaxy publication.

- Updated documentation with bilingual comments (English and Spanish) for better accessibility.

## [0.1.0] - 2025-06-14

### Added

- Initial release of `rotatrix` role.

- Policies for safe, automated log rotation on RHEL 9 systems.

- Default logrotate.conf and policies for btmp, wtmp, rsyslog, and dnf.

- README and documentation in Spanish.

- Basic smoke tests and inventory for localhost.
