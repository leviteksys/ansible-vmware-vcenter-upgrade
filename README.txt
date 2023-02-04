Minor upgrades with Ansible:

The following two playbooks use the custom ansible module ansible-vmware-vcenter-upgrade
found at: https://github.com/scottd018/ansible-vmware-vcenter-upgrade

The playbook vcenter_query.yml uses the above module to detect whether
there is a minor upgrade available.

The playbook vcenter_upgrade.yml will do minor upgrades if available.  It will not do major upgrades.  (The module doesn't support it.)


Upgrading vcenter V6 to V7 on the command line

Note:  vcsa-deploy requres libnsl.  Without libnsl you will get authentication
errors.  Install with yum install libnsl or apt-get install libnsl.

Overview:  The process creates a new vcenter on top of esxi

Find the appropriate ISO and mount it locally on the Ansible server.  There is no
need to mount the ISO on the client machine.
ISOs can be found here:
https://customerconnect.vmware.com/downloads/#all_products

For 6.7 to 7.0 upgrade we used VMware-VCSA-all-7.0.3-20990077.iso

The vcs-deploy executiable is located on the iso in the
/[iso mnt]/vcsa-cli-installer/lin64 directory.  The script execs
vcsa-deploy.bin which it expects to be in the local directory.  We
executed vcsa-deploy directly from within the iso.

If you have self-signed certs or no certs, use the option
--no-ssl-certificate-verification.  Also use --accept-eula to avoid
getting prompted to accept the eula.

You must pass as an argument the absolute path to a json file that contains
the configuration for your upgrade.  (this is covered later)  The full
command we used:

(From directory /[iso mount]/vcsa-cli-installer/lin64)
./vcsa-deploy upgrade /path/to/my.json --accept-eula --no-ssl-certificate-verification

The json configuration file:

Your configuration file contains the parameters required for the upgrade.
These include, hostname, ip address, both Administrator and root credentials
for the current vcenter, hostname, ip, and root credentials for the esxi server,
information about the network, and temporary network settings and login credentials
for the new vcenter instance.

Best results will be found by taking an existing template, found in
[iso mount point]/vcsa-cli-installer/templates and fill in your values.

Pick the template that best matches your installation.  Since we did not
have an appliance, and had vcenter installed on top of esxi, the
template vCSA-on-ESXi.json was most appropriate.  A filled out version
of that template is included.

Note, we created a json file by hand using the reference manual, which is the
file json.template, but we couldn't get it to work.  The error seemed to
indicate that the "name" field in the "appliance" stanza is both the
source and destination vcenter, which of course doesn't work.

vCSA-on-ESXi.json does work.
