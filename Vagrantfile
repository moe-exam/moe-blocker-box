# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "scotch/box"

    config.hostmanager.enabled = true
    config.hostmanager.manage_host = true
    config.hostmanager.manage_guest = true
    config.hostmanager.ignore_private_ip = false
    config.hostmanager.include_offline = true
    config.vm.define 'moodle.dev' do |node|
        node.vm.hostname = "moodle.dev"
        node.vm.network "private_network", ip: "192.168.33.11"
        node.hostmanager.aliases = %w(legacy.moodle.dev www.moodle.dev www.legacy.moodle.dev)
    end

    config.vm.synced_folder ".", "/var/www", :mount_options => ["dmode=777", "fmode=666"]

    config.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end
    
    # Optional NFS. Make sure to remove other synced_folder line too
    #config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }

    # provisioning script
    config.vm.provision "shell", inline: <<-SHELL
      ## for app.protege.dev
      echo "Creating vhost config for moodle.dev"
      sudo cp /etc/apache2/sites-available/scotchbox.local.conf /etc/apache2/sites-available/moodle.dev.conf

      echo "Updating vhost config for moodle.dev"
      sudo sed -i s,scotchbox.local,moodle.dev,g /etc/apache2/sites-available/moodle.dev.conf
      sudo sed -i s,/var/www/public,/var/www/moodle29,g /etc/apache2/sites-available/moodle.dev.conf

      echo "Enabling moodle.dev"
      sudo a2ensite moodle.dev

      echo "Creating vhost config for legacy.moodle.dev"
      sudo cp /etc/apache2/sites-available/scotchbox.local.conf /etc/apache2/sites-available/legacy.moodle.dev.conf

      echo "Updating vhost config for legacy.moodle.dev"
      sudo sed -i s,scotchbox.local,legacy.moodle.dev,g /etc/apache2/sites-available/legacy.moodle.dev.conf
      sudo sed -i s,/var/www/public,/var/www/moodle19,g /etc/apache2/sites-available/legacy.moodle.dev.conf

      echo "Enabling legacy.moodle.dev"
      sudo a2ensite legacy.moodle.dev

      # auto-update packages
      echo "Updating packages"
      sudo apt-get update
      sudo apt-get dist-upgrade -y

      # install php dependencies
      echo "Installing dependencies"
      sudo apt-get install php5-xdebug php5-xmlrpc -y
      sudo service apache2 restart
    SHELL
end
