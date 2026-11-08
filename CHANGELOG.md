# Changelog

All notable changes to this project are documented here. Releases are cut by
pushing a `vX.Y.Z` tag; the release workflow publishes the matching section.

## [Unreleased]

- Added: The role includes `ans_features` (from ans-features, tag `features`) on Windows, to add and remove Windows
  features such as OpenSSH Server and Telnet Client, and `ans_packages` (from ans-packages, tag `packages`) to install
  and remove packages such as PowerShell 7. Toggles `defos_enable_features` and `defos_enable_packages`; both run before
  the firewall and local accounts, and change nothing until their per-feature and per-package variables are set.
- Changed: `ansible.windows` is at least 3.6.0, which `ans_features` needs for `win_capability`.
- Added: Linux support. `site.yml` has a play for the `linux` group (with `become`) and one for the `windows` group,
  replacing `hosts: all`, and Windows-only tasks are skipped on Linux.
- Added: `defos_enable_firewall` and `defos_enable_local_accounts` toggles.
- Fixed: The role included `ans_settings`, which does not exist, so every run failed at that task. The include and the
  event log tasks are removed, along with `defos_event_log_name` and `defos_event_source_name`.
- Fixed: `ans_firewall` was included twice, the second time with variable names the role does not read, so
  `defos_enable_rdp_mgmt` and `defos_enable_remote_admin_base` did nothing. Both are removed; set the
  `ans_firewall_*` variables directly.
- Fixed: Tags such as `rdp` now select the firewall role's tasks when run through this role.
- Changed: The license in the role metadata is MIT, matching the repository.
- Changed: `ansible.windows` is at least 3.1.0, which `ans_local_accounts` needs.
- Changed: Brought the repository in line with the shared `ans-template` standard: the `SAL9000-HomeLab/shared-actions`
  CI workflows (fixing CI, which pointed at the old `Bocephus/shared-github-actions`), shared lint configs, PR
  template, release workflow, MIT license, and this changelog at the repository root.
- Changed: The role includes `ans_firewall` (renamed from `firewall` in ans-firewall).
- Changed: Collections are listed once, in `requirements.yml`, as `ansible.windows` and `community.windows` 3.x.
  `chocolatey.chocolatey` was dropped because nothing uses it yet.
- Removed: The `ans-firewall` role entry from `requirements.yml`. It could never install, because ansible-galaxy
  needs the role at the root of the git repository (see README.md, "Known issues").
- Changed: `inventory/hosts.yml` uses placeholder host names.
- Removed: `ansible.cfg`, `collections/requirements.yml`, the galaxy `tests/` directory, empty `defaults/`,
  `vars/` and `handlers/`, and the placeholder Molecule docs (they live in `ans-template`).
- Added: Initial `defos` role, which applies the firewall role.
- Changed: Ansible CI runs on pull requests only, no longer on pushes to `main` (synced from ans-template).
- Added: The role includes `ans_local_accounts` (from ans-local-accounts, tag `local-accounts`) to manage local users
  and groups.
- Fixed: `requirements.yml` installs the included roles from git. Roles kept under `roles/<name>/` in their own
  repository install fine (ansible-galaxy finds them by `meta/main.yml`); the "Known issues" note is removed.
- Changed: `defos_local_admin_groups` defaults to `{}`; the lab domain and group are removed from the repository and
  belong on the AWX inventory or group.
- Changed: The included roles install over https from their (public) repositories, so CI and AWX need no SSH key or
  secret for them.
