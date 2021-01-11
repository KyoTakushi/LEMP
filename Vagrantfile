# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "eighty8/centos6.8-min"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  #config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "./", "/vagrant", mount_options: ['dmode=777', 'fmode=777'], type:"virtualbox"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
   sudo yum install -y unzip
   sudo yum install -y vim
   # nginx をインストール
  ＃rpm -ivh http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
   sudo yum -y install nginx
   sudo cp -f /vagrant/vagrant_settings/nginx/nginx.conf /etc/nginx/nginx.conf
   sudo cp -f /vagrant/vagrant_settings/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf
   # Apache httpdをインストール
   # sudo yum -y install httpd
   # sudo cp -f /vagrant/vagrant_settings/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf
   # OS起動時にnginxを自動起動
   sudo service nginx start
   sudo chkconfig nginx on
   # wgetのインストール
   sudo yum -y install wget
   # MYSQL公式リポジトリの追加
   sudo yum -y localinstall http://dev.mysql.com/get/mysql57-community-release-el6-8.noarch.rpm
   # sudo rpm -Uvh http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
   # MYSQLのインストール
   sudo yum -y install mysql mysql-devel mysql-server mysql-utilities
   sudo yum -y install --enablerepo=mysql57-community mysql-community-server
   # OS起動時にmysqlを自動起動
   sudo chkconfig mysqld on
   sudo service mysqld start
   # epelリポジトリの追加
   sudo yum -y install epel-release.noarch
   # remiリポジトリの追加
   sudo yum -y install http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
   # php7.3と関連パッケージをインストール
   sudo yum -y install php php-devel php-mbstring php-mysql php-opcache php-pecl-apcu php-pecl-memcached php-cli php-common php-xml php-pear php-intl php-fpm
   # php-fpmの設定をコピー
   sudo cp -f /vagrant/vagrant_settings/php-fpm.d/www.conf /etc/php-fpm.d/www.conf
   # phpMyAdminと関連パッケージをインストール
   sudo yum -y install gd-last php-gd ImageMagick-last phpMyAdmin
   sudo cp -f /vagrant/vagrant_settings/httpd/conf.d/phpMyAdmin.conf /etc/httpd/conf.d/phpMyAdmin.conf
   sudo chown -R root.nginx /var/lib/php/session
   # MYSQL期設定
   sudo cp -f /vagrant/vagrant_settings/my.cnf /etc/my.cnf
   # /home/vagrant下にworkディレクトリを作成
   sudo mkdir /home/vagrant/work
   # workディレクトリに設定ファイルを移動
   sudo cp /vagrant/vagrant_settings/config.inc.php /home/vagrant/work
   sudo cp /vagrant/vagrant_settings/provisioner.sql /home/vagrant/work
   # ミドルウェアを再起動
   sudo service php-fpm restart
   sudo service nginx restart
   sudo service mysqld restart
   # Selinuxを無効化
   sudo cp -f /vagrant/vagrant_settings/selinux/config /etc/selinux/config
   # MYSQL初期パスワードログイン
   tmp_pwd=`cat /var/log/mysqld.log | grep "A temporary password" | tr ' ' '\n' | tail -n1`
   # MYSQLパスワード＝root
   mysql_pwd='root'
   # 上記パスワードでログイン可能にする
   sed -i -e "s/%pwd%/$mysql_pwd/g" /home/vagrant/work/provisioner.sql
   sed -i -e "s/%pwd%/$mysql_pwd/g" /home/vagrant/work/config.inc.php
   sudo cp -f /home/vagrant/work/config.inc.php /etc/phpMyAdmin/config.inc.php
   mysql -u root -p$tmp_pwd --connect-expired-password < /home/vagrant/work/provisioner.sql
   # サーバーを再起動
   sudo service php-fpm restart
   sudo service nginx restart
   sudo service httpd restart
   sudo service mysqld restart
  SHELL

  config.vm.provision :shell, run: "always", :inline => <<-EOT
  # ミドルウェアを再起動
  sudo service nginx restart
  sudo service php-fpm restart
  # sudo service httpd restart
  # sudo service mysqld restart
  EOT

  # 共有フォルダをアップデートしたくない（プラグインをインストールしている場合、2回目以降の起動で）
  config.vbguest.auto_update = false
  config.vbguest.no_remote = true

end
