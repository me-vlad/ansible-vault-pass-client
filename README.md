# ansible-vault-pass-client script


## About

A script that allows to keep Ansible Vault passwords in a gpg encrypted files
managed by pass (https://www.passwordstore.org) or compatible password managers
like gopass (https://www.gopass.pw).


## Requirements

- `python3`
- ansible
- `pass`, `gopass` or similar tool/wrapper


## Usage

This script **MUST** be saved with executable permissions.

By default the script uses `pass`. To use gopass or any compatible command
set the `ANSIBLE_VAULT_PASS_COMMAND` environment variable:

```shell
export ANSIBLE_VAULT_PASS_COMMAND=gopass
```

Commands with arguments are supported as well:

```shell
export ANSIBLE_VAULT_PASS_COMMAND='gopass show -o'
```


You can set `ANSIBLE_VAULT_PASSWORD_FILE=/path/to/ansible-vault-pass-client`
environment variable or use ansible utils with --vault-password-file option.

Also you can add to 'vault_password_file' option to [defaults] section of the
Ansible config file (ansible.cfg):

```ini
[defaults]
vault_password_file = /path/to/ansible-vault-pass-client
```


To configure default passwordstore for vault password add a new section to your
ansible configuration file:

```ini
[vault]
passwordstore = ansible/prod
```

or set the `ANSIBLE_VAULT_PASSWORDSTORE` environment variable:

```shell
export ANSIBLE_VAULT_PASSWORDSTORE=ansible/prod
```

### Vault-id

Single script may return different vault passwords. To use this feature, script
file must have a name that ends with -client suffix.
Use a CLI option --vault-id to set required passwordstore name.

```shell
ansible-playbook \
        --vault-id ansible/dev@/path/to/ansible-vault-pass-client site.yml
```

If vault-id is not set by CLI option or vault-id=default, script will search
for a passwordstore name in the `ANSIBLE_VAULT_PASSWORDSTORE` environment
variable, then in the Ansible config file.

Password store lookup priority:

1. `--vault-id` CLI option
2. `ANSIBLE_VAULT_PASSWORDSTORE` environment variable
3. `[vault] passwordstore` in ansible.cfg

### Errors

If the password manager command fails (missing entry, locked gpg key, etc.),
the script prints its stderr and exits with the same non-zero code, so Ansible
reports the underlying error instead of silently receiving an empty password.


## License

MIT


## Author Information

Vlad V. Teteria
