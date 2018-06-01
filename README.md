# ansible-argus
Ansible role for a site ARGUS

Requirments
===========

CC7 or compatable OS (with ssh access via public key encryption).

Role
===========

This role will install and configure a CC7 machine to act as a site ARGUS server for Grid purposes.  All configuration is done through ansible using user supplied variables.

* Install host cert and key.(install_host_certificates.yml)
* Install EGI IGTF trust anchors.(install_EGI_trustanchors.yml)
  * http://argus-documentation.readthedocs.io/en/latest/misc/argus_deployment.html#argus-deployment
* Install EPEL and UMD4 repos. (install_argus_repos.yml)
  * http://argus-documentation.readthedocs.io/en/latest/misc/argus_deployment.html#configuration
* Install, run and configure  fetch-url (install_fetch_crl.yml)
* Install and configure argus (install_argus.yml)
http://argus-documentation.readthedocs.io/en/latest/misc/manual_configuration.html#argus-manual-configuration



Configuration
=============

Configuring pap-admin
----------------------

This is configured through variables, [Client configuration documentation is available here](http://argus-documentation.readthedocs.io/en/latest/pap/cli.html#client-configuration-since-version-1-3-0). There are only two variables for this:

|Ansible variable | variable | default |
|---------------- | -------- | -------- |
|n/a                 | host     | non configurable, ansible will set to host fqdn |
|pap_port         | port     | 8150 |


Configuring the PAP
-------------------

Configuration of Argus Policy Administraion Point, `pap_configuration.ini` is done through ansible, the [Argus documentation is here](http://argus-documentation.readthedocs.io/en/latest/pap/configuration.html#service-configuration-file)

PAPs are optional, and have many additional options. These are done through a `dict`, this means there is no error checking on variable names at the minute.   This is used in two places in the templare, to populate that paps, and to automatically obtain the `[paps:properties], ordering`.


`[paps:properties]`

|Ansible variable  | variable      | default |
|----------------- | ------------- | --- |
|pap_poll_interval | poll_interval | 14400 (4 hours) |
|pap_ordering      | ordering      | Obtained from paps dictionary list |


`[repository]`
|Ansible variable             | variable                 | default |
|-----------------            | -------------            | --- |
|pap_location                 | location                 | `$PAP_HOME/repository` |
|pap_consistency_check        | consistency_check        | false |
|pap_consistency_check_repair | consistency_check.repair | false |


`[standalone-service]`
|Ansible variable      | variable         | default |
|-----------------     | -------------    | --- |
|                      | hostname         | non configurable, ansible will set to host fqdn |
|pap_port              | port             | 8150 |
|pap_shutdown_port     | shutdown_port    | 8151 |
|pap_shutdown_command  | shutdown_command | shutdown |
|pap_entity_id         | entity_id        | http://<fqdn_hostname>:<port>/pap |


`[security]`
|Ansible variable        | variable            | default |
|-----------------       | -------------       | --- |
|pap_certificate         | certificate         | /etc/grid-security/hostcert.pem |
|pap_private_key         | private_key         | /etc/grid-security/hostkey.pem |
|pap_trust_store_dir     | trsut_store_dir     | /etc/grid-security/certificates |
|pap_crl_update_interval | crl_update_interval | 30m |

Configuration of Argus Policy Administraion Point, `pap_authorization.ini` is done through ansible, the [Argus documentation is here](http://argus-documentation.readthedocs.io/en/latest/pap/configuration.html#service-access-control). This controls the ACEs.  It is done through a `dict` instead of variables.  This is done through two `dict`: `pap_dn` for DNs and `pap_fqan` for VOMS FQAN.  Each takes key:value pairs of the form `principle`:`permission`. An example is:

```python
pap_dn:
   "/C=IT/O=INFN/OU=Personal Certificate/L=CNAF/CN=Andrea Ceccanti": POLICY_READ_LOCAL|POLICY_READ_REMOTE
pap_fqan:
   /atlas/Role=PAP-Admin: ALL
```

Configuring PDP
----------------

Configuration of Argus PDP, `pdp.ini` is done through ansible, the [Argus documentation is here](http://argus-documentation.readthedocs.io/en/latest/pap/configuration.html#service-access-control). 

etc/argus/pdp/pdp.ini

[SERVICE]
|Ansible variable                    | variable                        | default |
|-----------------                   | -------------                   | --- |
|pdp_entityID       | entityID      | http://<fqdn_hostname>:<port>/pdp |
|                   | hostname 	   | non configurable, ansible will set to host fqdn |
|pdp_port           | port	   | 8152 |
|pdp_admin_host     | adminHost	   | fqdn_hostname |
|pdp_admin_port     | adminPort	   | 8153 |
|pdp_admin_password | adminPassword | None |

`[POLICY]`
|Ansible variable      | variable               | default |
|-----------------     | -------------          | --- |
|pdp_paps              | paps                   | None |
|pdp_retentionInterval | pdp_retentionInterval  | 240 (4 hours) |

`[SECURITY]`
|Ansible variable                    | variable                        | default |
|-----------------                   | -------------                   | --- |
|pdp_servicePrivateKey 	            | servicePrivateKey               | /etc/grid-security/hostcert.pem |
|pdp_serviceCertificate 	    	    | serviceCertificate              | /etc/grid-security/hostkey.pem |
|pdp_trustInfoDir		    | trustInfoDir                    | /etc/grid-security/certificates |
|pdp_enableSSL                       | enableSSL                       | false |
|pdp_requireClientCertAuthentication | requireClientCertAuthentication | false |





Configuring PEP
----------------

Configuration of Argus PEP, `pepd.ini` is done through ansible, the [Argus documentation is here](http://argus-documentation.readthedocs.io/en/latest/pep/pepd_configuration.html#argus-pep-server-con\figuration).

`[SECURITY]`
Ansible variable                    | variable                        | default
-----------------                   | -------------                   | ---
pepd_servicePrivateKey 	            | servicePrivateKey               | /etc/grid-security/hostcert.pem 
pepd_serviceCertificate 	    | serviceCertificate              | /etc/grid-security/hostkey.pem
pepd_trustInfoDir		    | trustInfoDir                    | /etc/grid-security/certificates
pepd_enableSSL                      | enableSSL                       | false
pepd_requireClientCertAuthentication | requireClientCertAuthentication | true


`[PDP]`
Ansible variable            | variable                        | default
----------------            | -------------                   | ---
pepd_pdps		    | pdps			      | https://<fqdn_hostname>:<pepd_port>/authz
pepd_maximumCachedResponses | maximumCachedResponses	      |


`[SERVICE]`
pdp_entityID       | entityID      | http://<fqdn_hostname>:<port>/pepd
                   | hostname 	   | non configurable, ansible will set to host fqdn
pdp_port           | port	   | 8154
pdp_admin_host     | adminHost	   | fqdn_hostname
pdp_admin_port     | adminPort	   | 8155
pdp_admin_password | adminPassword | None


The final configuration options are for the pips.  This is done a space separtated list of section names of PIPs and Obligation Handlers. Ansible handles this by have a list of dictionaries.  Each dict is named after a section heading, and contains a series fo key value pairs where each key is a property, and the value is the value.   Ansible will take care of putting the section headings and the full properties into the template file.  The order used will be the order in the list.


Quick Setup
===========


Example
============



Author
============
Robin Long