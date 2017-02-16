# ansible-argus
Ansible role for a site ARGUS

Requirments
-----------

CC7 or compatable OS (with ssh access via public key encryption).

Role
-----------

This role will install and configure a CC7 machine to act as a site ARGUS server for Grid purposes.  All configuration is done through ansible using user supplied variables.

* Install host cert and key.(install_host_certificates.yml)
* Install EGI IGTF trust anchors.(install_EGI_trustanchors.yml)
  * http://argus-documentation.readthedocs.io/en/latest/misc/argus_deployment.html#argus-deployment
* Install EPEL and UMD4 repos. (install_argus_repos.yml)
  * http://argus-documentation.readthedocs.io/en/latest/misc/argus_deployment.html#configuration
* Install, run and configure  fetch-url (install_fetch_crl.yml)
* Install and configure argus (install_argus.yml)

Variables
-----------

* install host certs:
host_cert: host_files/<host_name>/etc/grid-security/hostcert.pem
host_key: host_files/<host_name>/etc/grid-security/hostkey.pem



Quick Setup
-----------

Optional Variables
-------------



Example
------------



Author
------------
Robin Long