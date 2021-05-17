#!/usr/bin/env python3
# -*- coding: utf8 -*-

"""
A script that allows to keep Ansible Vault passwords in a gpg encrypted files
managed by pass (https://www.passwordstore.org) or compatible password managers
like gopass (https://www.gopass.pw).
"""

from argparse import ArgumentParser as AP
from configparser import ConfigParser, NoOptionError, NoSectionError
from os import environ, getcwd, path
from subprocess import PIPE, Popen
from sys import exit, stderr, stdout

# Password manager to use (pass or gopass)
pass_command = 'pass'

# Get Ansible config file
try:
    import ansible.constants as C
    ansible_config_file = C.CONFIG_FILE
except ImportError:
    try:
        ansible_config_file = environ['ANSIBLE_CONFIG']
    except KeyError:
        cfg = getcwd() + '/' + 'ansible.cfg'
        if path.isfile(cfg):
            ansible_config_file = cfg
        else:
            ansible_config_file = path.expanduser('~/.ansible.cfg')


def get_vault_id():
    # Get passwordstore name from '--vault-id' CLI option
    parser = AP(description='Get a vault password from passwordstore',
                epilog='Please read the README.md file for more info.',
                allow_abbrev=False)

    parser.add_argument('--vault-id', action='store', default='default',
                        dest='vault_id',
                        help='passwordstore containing the vault password')

    vault_id = parser.parse_args().vault_id.strip()

    return vault_id


def get_config_passwordstore():
    # Get passwordstore name from Ansible config file
    if ansible_config_file:
        try:
            # Raad Ansible config
            config = ConfigParser()
            config.read(ansible_config_file)
            # Get passwordstore name from Ansible config
            passwordstore = config.get('vault', 'passwordstore',
                                       fallback='').strip()
        except NoOptionError:
            pass
        except NoSectionError:
            pass
    else:
        pass
    return passwordstore


def main():
    vault_id = get_vault_id()
    if (vault_id != 'default' and vault_id != ""):
        passwordstore = vault_id
    else:
        passwordstore = get_config_passwordstore()

    if passwordstore:
        # Get vault password from passwordstore
        proc = Popen([pass_command, passwordstore], stdout=PIPE, stderr=PIPE)
        output = proc.communicate()[0].decode('utf-8').strip().split("\n")[0]
        stdout.write(output)
        exit(0)
    else:
        stderr.write("Couldn't get passwordstore settings from Ansible config "
                     "file or --vault-id option!\nPlease read the README.md "
                     "file for more info about script settings.\n")
        exit(1)


if __name__ == '__main__':
    main()
