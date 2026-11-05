# Changelog

All notable changes to this project are documented here. Releases are cut by
pushing a `vX.Y.Z` tag; the release workflow publishes the matching section.

## [Unreleased]

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
