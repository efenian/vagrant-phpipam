# -*- mode: ruby -*-
# vi: set ft=ruby :

branch = "1.3"
password = "password"

$script = <<SCRIPT
apt-get update
DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" dist-upgrade
apt-get install -y apache2 libapache2-mod-php mariadb-server php-gmp php-pear php-mysql php-ldap apache2-utils php-mbstring php-gd php-mcrypt php-curl git php-snmp snmp-mibs-downloader
a2enmod rewrite
a2enmod ssl
a2ensite default-ssl
echo -e "\n\nvagrant\nvagrant\n\n\n\n\n\n" | mysql_secure_installation 2>/dev/null
mysql -u root --password=vagrant -e "CREATE USER 'phpipam'@'localhost' IDENTIFIED BY 'phpipamadmin'; GRANT ALL PRIVILEGES ON *.* TO 'phpipam'@'localhost' WITH GRANT OPTION; FLUSH PRIVILEGES;"
rm /var/www/html/index.html
git clone -b #{branch} https://github.com/phpipam/phpipam.git /var/www/html/.
cp -a /var/www/html/config.dist.php /var/www/html/config.php
sed -i '/<\\\/VirtualHost>/i\\\\t<Directory /var/www/html/>\\n\\t\\tOptions FollowSymLinks\\n\\t\\tAllowOverride all\\n\\t\\tOrder allow,deny\\n\\t\\tAllow from all\\n\\t<\\\/Directory>' /etc/apache2/sites-enabled/000-default.conf
sed -i '/<\\\/VirtualHost>/i\\\\t\\t<Directory /var/www/html/>\\n\\t\\t\\tOptions FollowSymLinks\\n\\t\\t\\tAllowOverride all\\n\\t\\t\\tOrder allow,deny\\n\\t\\t\\tAllow from all\\n\\t\\t<\\\/Directory>' /etc/apache2/sites-enabled/default-ssl.conf
systemctl restart apache2.service
systemctl enable apache2.service
update-rc.d mysql enable
echo "*/15 * * * * root /usr/bin/php /var/www/html/functions/scripts/pingCheck.php" >> /etc/crontab
echo "*/15 * * * * root /usr/bin/php /var/www/html/functions/scripts/discoveryCheck.php" >> /etc/crontab
curl -s -k -H 'X-Requested-With: XMLHttpRequest' --form mysqlrootuser=phpipam --form mysqlroorpass=phpipamadmin --form createdb=on --form creategrants=on https://127.0.0.1/app/install/install-execute.php
curl -s -k -H 'X-Requested-With: XMLHttpRequest' --form password1=#{password} --form password2=#{password} --form siteTitle=phpipam https://127.0.0.1/app/install/postinstall_submit.php
SCRIPT


# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.
  config.vm.hostname = "phpipam"

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "bento/ubuntu-16.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network :private_network, ip: "192.168.16.30"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.name =  "phpipam"
     vb.memory = "1024"
     vb.cpus = "1"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: $script
end
