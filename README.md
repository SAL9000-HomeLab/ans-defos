# ans-defos

Default OS configuration for Windows Server hosts. The `defos` role applies each part of the baseline by including
the role that owns it, each under its own tag. Run `site.yml` from AWX.

| Part | Role | Tag |
| --- | --- | --- |
| Windows Firewall | `ans_firewall` ([ans-firewall]) | `firewall` |
| Local users and groups | `ans_local_accounts` ([ans-local-accounts]) | `local-accounts` |

Base settings, event logs and remote administration are planned (commented out in
[roles/defos/tasks/main.yml](roles/defos/tasks/main.yml)).

The inventory in [inventory/hosts.yml](inventory/hosts.yml) is a placeholder. Put your real hosts in the AWX
inventory, or in a gitignored `local/` copy for workstation runs.

## Requirements

| Requirement | Version |
| --- | --- |
| ansible-core | >= 2.16 |
| Python | >= 3.10 |

- Target hosts: Windows Server 2019, 2022 or 2025, reached over PSRP (Kerberos, HTTPS). The controller needs the
  Python packages in [requirements.txt](requirements.txt).
- Collections: from [requirements.yml](requirements.yml).
- Roles: from [requirements.yml](requirements.yml), installed from git. `ansible-galaxy` finds each role by its
  `meta/main.yml`, so a source repository can keep its single role under `roles/<name>/`. The role repositories are
  public and installed over https, so AWX project syncs and CI need no credentials for them.

## Inventory

[site.yml](site.yml) runs on every host in the inventory. The example groups hosts under `windows` by version
(`windows_2019`, `windows_2022`, `windows_2025`) and by install type (`windows_core`, `windows_gui`), with the PSRP
connection settings on `windows`. Settings for an included role, such as `ans_firewall_mgmt_ips` or
`ans_local_accounts_groups`, go on the inventory or group as usual.

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

[ans-firewall]: https://github.com/SAL9000-HomeLab/ans-firewall
[ans-local-accounts]: https://github.com/SAL9000-HomeLab/ans-local-accounts
