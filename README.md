# ans-defos

Default OS configuration for Linux and Windows hosts. The `defos` role applies each part of the baseline by including
the role that owns it, each with its own toggle and tag. Run `site.yml` from Ascender.

| Part | Role | Toggle | Tag | Linux | Windows |
| --- | --- | --- | --- | --- | --- |
| Windows features | `ans_features` ([ans-features]) | `defos_enable_features` | `features` | No | Yes |
| Packages | `ans_packages` ([ans-packages]) | `defos_enable_packages` | `packages` | Yes | Yes |
| Firewall | `ans_firewall` ([ans-firewall]) | `defos_enable_firewall` | `firewall` | firewalld on Rocky, opt-in | Yes |
| Local users and groups | `ans_local_accounts` ([ans-local-accounts]) | `defos_enable_local_accounts` | `local-accounts` | Yes | Yes, except creating users |

The roles run in that order. Features come first because adding OpenSSH Server opens a firewall rule that
`ans_firewall` replaces, and starts `sshd`, which creates the `sshd_config` that `ans_local_accounts` edits.

The tags of the included roles also work from here, such as `rdp`, `firewalld`, `openssh` or `powershell`.

The inventory in [inventory/hosts.yml](inventory/hosts.yml) is a placeholder. Put your real hosts in the Ascender
inventory, or in a gitignored `local/` copy for workstation runs.

## Requirements

| Requirement | Version |
| --- | --- |
| ansible-core | >= 2.16 |
| Python | >= 3.10 |

- Linux targets: RHEL/Rocky 9 and 10 and Ubuntu 24.04, reached over SSH with `become`. Ubuntu gets packages and
  local accounts only.
- Windows targets: Windows Server 2019, 2022 or 2025, reached over PSRP (Kerberos, HTTPS). `ans_features` and
  `ans_packages` support Server 2025 and Windows 11. The controller needs the
  Python packages in [requirements.txt](requirements.txt).
- Collections: from [requirements.yml](requirements.yml).
- Roles: from [requirements.yml](requirements.yml), installed from git. `ansible-galaxy` finds each role by its
  `meta/main.yml`, so a source repository can keep its single role under `roles/<name>/`. The role repositories are
  public and installed over https, so Ascender project syncs and CI need no credentials for them.

## Inventory

[site.yml](site.yml) runs on the `linux` group with `become` and on the `windows` group, with the connection settings
on each group. Hosts in neither group are left alone.

Set each role's variables directly on the inventory, group or host, such as `ans_firewall_mgmt_ips` or
`ans_local_accounts_groups`. Use these rather than job template extra vars, which override every level for every host
in the job.

Features and packages each have their own variable, set to `present`, `absent`, or left empty to leave them alone,
so a host can override one without repeating the rest:

```yaml
# windows group
ans_features_openssh_server: present
ans_features_telnet_client: present
ans_packages_powershell: present

# linux group
ans_packages_powershell: present
```

A list set at two levels is replaced, not merged: a host's `ans_local_accounts_users` replaces its group's.

Windows Firewall rules are on by default and need `ans_firewall_mgmt_ips`. To use firewalld, set
`ans_firewall_firewalld_enabled: true` on the group; leave it off for hosts that run with firewalld off, such as RKE2
nodes.

**Lockout risk:** `ans_firewall_mgmt_ips` must include the address Ascender jobs connect from. Try a change on one host
with a job limit first.

## Development and CI

The workflows and lint configs are the shared ones from `ans-template`:

- **Ansible CI** (`.github/workflows/ansible-ci.yml`), on every pull request: `yamllint`,
  `ansible-playbook --syntax-check site.yml`, `ansible-lint`.
- **Linting Validation** (`.github/workflows/ci.yml`), on pull requests: markdownlint, linkspector, yamllint.

Run the same checks locally:

```sh
python3 -m venv .venv && . .venv/bin/activate
pip install "ansible-core>=2.16" "ansible-lint>=6" "yamllint>=1.30" -r requirements.txt
ansible-galaxy collection install -r requirements.yml
yamllint -f parsable .
ansible-playbook -i inventory/hosts.yml --syntax-check site.yml
ansible-lint .
npx markdownlint-cli2 "**/*.md" "#.venv"
```

Add a line under `## [Unreleased]` in [CHANGELOG.md](CHANGELOG.md) with each change. Pushing a `vX.Y.Z` tag
publishes that version's section as a GitHub release.

## License

MIT, see [LICENSE](LICENSE).

[ans-features]: https://github.com/SAL9000-HomeLab/ans-features
[ans-packages]: https://github.com/SAL9000-HomeLab/ans-packages
[ans-firewall]: https://github.com/SAL9000-HomeLab/ans-firewall
[ans-local-accounts]: https://github.com/SAL9000-HomeLab/ans-local-accounts
