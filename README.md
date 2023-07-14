[![Molecule Test](https://github.com/bwInfoSec/ansible-role-apache-waf/actions/workflows/molecule-test.yml/badge.svg)](https://github.com/bwInfoSec/ansible-role-apache-waf/actions/workflows/molecule-test.yml)

ansible-role-apache-waf
=========

Install and configure apache2 with mod-security paranoia level 1. This should work for almost all use cases without 
adaption.

## Platforms

- RHEL 8
- RHEL 7
- Debian 10
- Debian 11
- Ubuntu 18.04
- Ubuntu 20.04
- Ubuntu 22.04

## Install

``` sh
ansible-galaxy install bwinfosec.apache_waf
```

## Example Playbook

```yml
- name: Setup webserver with WAF
  hosts: webserver
  become: true

  roles:
    - bwinfosec.apache_waf
```

## Local Development
This role includes a *Molecule* test to execute on RHEL/CentOS, Debian and Ubuntu.

To run all scenarios (i.e. test all platforms):

```bash
$ molecule test --all
```

To run a specific scenario by name:

```bash
$ molecule test --scenario-name ubuntu
$ molecule test --scenario-name debian
$ molecule test --scenario-name centos
```

## Customization
You can override the default behavior of this role by overriding the default values of these variables.

| Variable                      | Default                                  | Description                                                                                                                                                                  |
|-------------------------------|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `crs_dir`                     | "/opt/owasp_crs"                         | This is where the CRS rules from GitHub will be cloned. Making changes to these files will cause the GitHub task to check out the files again.                               |
| `crs_version`                 | "v3.3.4"                                 | Version of the Core Rule Set that should be used. For a list of usable releases, check the [OWASP CRS GitHub Releases](https://github.com/coreruleset/coreruleset/releases). |
| `crs_conf_dir`                | "/etc/crs"                               | Where Apache2 expects the `crs-setup.conf` to be.                                                                                                                            |
| `crs_conf_src_template`       | "{{ crs_dir }}/crs-setup.conf.example"   | What `crs-setup.conf` to use. If you change this path, make sure that the file exists in your `/templates` folder and is named differently.                                  |
| `modsec_tmp_dir`              | "/var/cache/modsecurity"                 | Where ModSecurity stores tmp files.                                                                                                                                          |
| `modsec_data_dir`             | "/var/cache/modsecurity"                 | Where ModSecurity stores data files.                                                                                                                                         |
| `modsec_log_dir`              | "/var/log/mod_security"                  | Where ModSecurity stores logs.                                                                                                                                               |
| `modsec_conf_dir`             | "/opt/mod_security"                      | Where Apache expects the ModSecurity configuration to be.                                                                                                                    |
| `modsec_conf_src_template`    | "modsecurity.conf.j2"                    | What `modsecurity.conf` to use. If you change this path, make sure that the file exists in your `/templates` folder and is named differently.                                |
| `modsec_conf`                 | "{{ modsec_conf_dir }}/modsecurity.conf" | Where the ModSecurity config will be created.                                                                                                                                |
| `modsec_unicode_mapping_file` | "/opt/mod_security/unicode.mapping"      | Unicode Mappings to include. No need to change this.                                                                                                                         |
| `test_waf`                    | true                                     | Whether the firewall should be tested after the role has been played.                                                                                                        |
| `test_waf_validate_certs`     | true                                     | Whether TLS certificates should be validated when testing the firewall.                                                                                                      |
| `test_waf_urls`               | [http://localhost/?e=/bin/bash]          | What a URLs to use for testing the firewall.                                                                                                                                 |

Variables and their defaults are defined in `ansible-role-apache-waf/defaults/main.yml`.

## Troubleshooting
You may be getting a warning about compatibility issues regarding [CVE-2022-39956](https://nvd.nist.gov/vuln/detail/CVE-2022-39956).

If you are using an up-to-date version of the CRS (>3.2.3, >3.3.4) with an incompatible older version of ModSecurity 
(<2.9.6), a certain rule must be deactivated in order to work. This is merely a workaround and should be avoided by 
running a compatible version of ModSecurity. Removing the rule will effectively revert the fix for CVE-2022-39956.

Some more recent OS images have compatible versions in their package repositories:
- Ubuntu 23.04 ("Lunar"): `libapache2-mod-security2` @ 2.9.7
- Debian 11 ("Bookworm"): `libapache2-mod-security2` @ 2.9.7
- Centos 8/9 Stream: `mod_security` @ 2.9.6
- RHEL 7/8/9: `mod_security` @ 2.9.6

See also the [release notes for CRS v3.3.4](https://github.com/coreruleset/coreruleset/releases/tag/v3.3.4) or [v2.3.
2](https://github.com/coreruleset/coreruleset/releases/tag/v3.2.3) and the [announcement about fixing these CVEs](https://coreruleset.org/20220919/crs-version-3-3-3-and-3-2-2-covering-several-cves/).

## Licensing
This work is licensed under the [LGPL](https://www.gnu.org/licenses/lgpl-3.0.html) AND the [EUPL 1.2](https://joinup.ec.europa.eu/collection/eupl/eupl-text-eupl-12).

If you redistribute this code please also include the AUTHORS file.

## Contribution
If you want to contribute feel free to do so by creating a pull request on [GitHub](https://github.
com/bwInfoSec/ansible-role-apache-waf).
