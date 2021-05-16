# ansible-vault-pass-client script


## About

A script for usage with Ansible Vault and pass (https://www.passwordstore.org)
or gopass (https://www.gopass.pw) to keep vault passwords in an encrypted 
passwordstore.


## Requirements

python3  
ansible  
pass or gopass


## Usage

This script MUST be saved with executable permissions.

To use gopass instead pass just change a line  
`pass_command = 'pass'`  
to  
`pass_command = 'gopass'`


You can set `ANSIBLE_VAULT_PASSWORD_FILE=/path/to/ansible-vault-pass-client`
environment variable or use ansible utils with --vault-password-file option.

Also you can add to 'vault_password_file' option to [defaults] section of the
Ansible config file (ansible.cfg):

``` ini
[defaults]
vault_password_file = /path/to/ansible-vault-pass-client
```


To configure default passwordstore for vault password add a new section to your
ansible configuration file:

``` ini
[vault]
passwordstore = ansible/prod
```

### Vault-id

Single script may return different vault passwords. To use this feature, script
file must have a name that ends with -client suffix.
Use a CLI option --vault-id to set required passwordstore name.

```
ansible-playbook \
        --vault-id ansible/dev@/path/to/ansible-vault-pass-client site.yml
```

If vault-id is not set by CLI option or vault-id=default, script will search
for a passwordstore name in the Ansible config file.

