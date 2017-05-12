# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.network "private_network", ip: "192.168.33.99"
  config.vm.network "forwarded_port", guest: 80, host: 8000
  config.vm.hostname = "civicbot"

  if Vagrant::Util::Platform.windows? then
    # This isn't tested - if you run this on windows and it doesn't work, make sure to push up the fix.
    config.vm.synced_folder ".", "/var/www"
  else
    config.vm.synced_folder ".", "/var/www", :nfs => { :mount_options => ["dmode=777","fmode=666"] }
  end


  # This is more a personal thing, so it's in the gitignore. Do your own if you like.
  aliasesPath = "aliases"
  if File.exists? aliasesPath then
    config.vm.provision "file", source: aliasesPath, destination: "~/.bash_aliases"
  end


  # Provision the server
  config.vm.provision "shell", inline: <<-SHELL

    # Setup Postgres
    sudo add-apt-repository "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main"
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    sudo apt-get -y update
    sudo apt-get -y install postgresql-9.6
    sudo apt-get -y install postgis
    sudo apt-get -y install gdal-bin
    sudo -u postgres createuser -s ubuntu
    createdb civicbot
    export PGDATABASE=civicbot
    psql -c "CREATE EXTENSION postgis;"

    # Setup Node
    sudo add-apt-repository "deb https://deb.nodesource.com/node_6.x xenial main"
    wget --quiet -O - https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo apt-key add -
    sudo apt-get -y update
    sudo apt-get -y install nodejs
    sudo npm install -g foreman
    sudo npm install -g knex
    
  SHELL
end
