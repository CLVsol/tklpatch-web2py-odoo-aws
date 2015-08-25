tklpatch-web2py-odoo-aws
========================

This project will help you install `Odoo 8.0 <https://www.odoo.com/>`_ over a `TurnKey web2py <http://www.turnkeylinux.org/web2py>`_ appliance, using the Amazon Web Services (AWS) EC2 infrastructure.

#. Create a new Key Pair:

	* Key pair name: **tkl-web2py-odoo-aws**
	* Private Key File: **tkl-web2py-odoo-aws.pem**

#. Launch a new Amazon EC2 instance:

	* Step 1: Choose an Amazon Machine Image (AMI) - Comnmunity AMIs: **turnkey-web2py-13.0-wheezy-amd64.ebs_3 - ami-09b70d14**
	* Step 2: Choose an Instance Type: **t1.micro**
	* Step 3: Configure Instance Details: (leave dafaults)
	* Step 4: Add Storage:
		* Volume Type: **Magnetic**
	* Step 5: Tag Instance: (leave dafaults)
	* Step 6: Configure Security Group: 
		* Security group name: **tkl-web2py-odoo-aws**
		* Description: **Security Group for tkl-web2py-odoo-aws**
	* Launch the instance:
		* Select a key pair: **tkl-web2py-odoo-aws**
	
	Related information:

		* Security Groups: **tkl-web2py-odoo-aws**
		* Private Key File: **tkl-web2py-odoo-aws.pem**

	Security Group: tkl-web2py-odoo-aws (Inbound)::

		Port (Service)    Source
		---------------------------------------
		22(SSH)           0.0.0.0/0
		80(HTTP)          0.0.0.0/0
		443(HTTPS)        0.0.0.0/0
        5432(PostgreSQL) <VPC Network> (172.31.0.0/16)
        12320(Web Shell)  0.0.0.0/0  (disable)
		12321(Webmin)     0.0.0.0/0  (disable)

#. Connect, via SSH, for the first time to the instance and set the passowrds:

	* root (Linux)
	* postgres (PostgreSQL)

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

		HOSTNAME=tkl-web2py-odoo-aws
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

	* openuser (PostgreSQL)
