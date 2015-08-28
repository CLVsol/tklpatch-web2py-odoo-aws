tklpatch-web2py-odoo-aws
========================

This project will help you install `Odoo 8.0 <https://www.odoo.com/>`_ over a `TurnKey web2py <http://www.turnkeylinux.org/web2py>`_ appliance, using the Amazon Web Services (AWS) EC2 infrastructure.

#. Create a new Key Pair:

	* Key pair name: **tk-aws-web2py-odoo**
	* Private Key File: **tk-aws-web2py-odoo.pem**

#. Launch a new Amazon EC2 instance:

	* Step 1: Choose an Amazon Machine Image (AMI) - Comnmunity AMIs: **turnkey-web2py-13.0-wheezy-amd64.ebs_4 - ami-fdb70de0**
	* Step 2: Choose an Instance Type: (select)
	* Step 3: Configure Instance Details: (leave dafaults)
	* Step 4: Add Storage:
		* Volume Type: **General Purpose (SSD)**
	* Step 5: Tag Instance: (leave dafaults)
	* Step 6: Configure Security Group: 
		* Security group name: **tk-aws-web2py-odoo**
		* Description: **Security Group for tk-aws-web2py-odoo**
        * Security Group Rules: (Inbound)::

			Port (Service)    Source
			---------------------------------------
			22(SSH)           0.0.0.0/0
			80(HTTP)          0.0.0.0/0
			443(HTTPS)        0.0.0.0/0
	        12320(Web Shell)  0.0.0.0/0  (disable)
			12321(Webmin)     0.0.0.0/0  (disable)

	* Launch the instance:
		* Select a key pair: **tk-aws-web2py-odoo**
	
#. Connect, via SSH, for the first time to the instance and set the passowrds:

	* root (Linux)
	* root (MySQL)
	* admin (web2py)

	Note: For the first login use the randon password created during the server launching. This randon password can be found in the System Log File.

#. `Disable Password-based Login <http://aws.amazon.com/articles/1233?_encoding=UTF8&jiveRedirect=1>`_:

	Log in to your instance as root and edit the ssh daemon configuration file "**/etc/ssh/sshd_config**"

	Find the line::

		PasswordAuthentication yes

	and change it to::

		PasswordAuthentication no

	Save the file and restart sshd::

		/etc/init.d/ssh restart

	**You will now only be able to log in with an ssh key.**

#. Update host name, executing the following commands:

	::

		HOSTNAME=tk-aws-web2py-odoo
		echo "$HOSTNAME" > /etc/hostname
		sed -i "s|127.0.1.1 \(.*\)|127.0.1.1 $HOSTNAME|" /etc/hosts
		/etc/init.d/hostname.sh start

#. Upgrade the software:

	::

		apt-get update
		apt-get -y upgrade

#. Install **TKLPatch** using the commands:

	::

		apt-get update
		apt-get -y install tklpatch

	The documentation is installed at "/usr/share/tklpatch/docs" and the exemples at "/usr/share/tklpatch/docs".

#. Get the TKLPatch script "**clvsol_tklpatch-odoo-aws**" using the commands:

	::

		cd /root
		git-clone https://github.com/CLVsol/tklpatch-web2py-odoo-aws.git clvsol_tklpatch-web2py-odoo-aws

#. Apply the patch "clvsol_tklpatch-web2py-odoo-aws":

	::

		cd /root
		tklpatch-apply / clvsol_tklpatch-web2py-odoo-aws

#. Change manually, using Webmin, the passwords for the accounts:

	* openerp (Linux)

#. Change manually, editing the Odoo configuration files (/opt/openerp/odoo/**openerp-server.conf**, /opt/openerp/odoo/**openerp-server_man.conf**), the passwords for the accounts:

	* admin (Odoo server - admin_passwd)
	* openuser (account on PostgreSQL - db_password)

#. Update the Security Group:

	Security Group: tkl-lamp-odoo-aws (Inbound)::

		Port (Service)    Source
		---------------------------------------
		22(SSH)           0.0.0.0/0
		80(HTTP)          0.0.0.0/0
		443(HTTPS)        0.0.0.0/0
		8069(Odoo)        0.0.0.0/0  (disable)
		12320(Web Shell)  0.0.0.0/0  (disable)
		12321(Webmin)     0.0.0.0/0  (disable)
		12325(Odoo)       0.0.0.0/0

#. Change manually, editing the Odoo configuration files (/opt/openerp/odoo/**openerp-server.conf**, /opt/openerp/odoo/**openerp-server_man.conf**), the db_host::

	# db_host = 127.0.0.1
	db_host = <PostgreSQL server>

#. To stop and start the Odoo server, use the following commands (as root):

	::

		/opt/openerp/openerp.init stop

		/opt/openerp/openerp.init start
