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

  config.vm.provider "libvirt" do |prv|
      prv.cpus = 4
  end

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "generic/ubuntu1804"

  config.vm.synced_folder ".", "/vagrant", type: "rsync",
      rsync__exclude: ".git/"

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
  
      set -eu -o pipefail
      IFS=$'\n\t'
          
      sed --in-place 's/addresses.*/addresses: \[1.1.1.1\]/g' /etc/netplan/01-netcfg.yaml
      sed --in-place 's/DNSSEC=yes/DNSSEC=no/g' /etc/systemd/resolved.conf

      netplan apply
      systemctl restart systemd-networkd
      systemctl restart systemd-resolved

      cd /vagrant

      dpkg --add-architecture arm64
      cat ./bionic-sources.txt | tee /etc/apt/sources.list
      apt update

      apt install --yes binutils-aarch64-linux-gnu build-essential \
            crossbuild-essential-arm64 devscripts debhelper equivs
      
      sudo -u vagrant wget --no-verbose 'https://github.com/libvips/libvips/archive/refs/tags/v8.10.5.tar.gz' --output-document=vips_8.10.5.orig.tar.gz
      sudo -u vagrant tar --auto-compress --create --file vips_8.10.5-3.debian.tar.xz ./debian

      sha512sum --check ./sha512sums.txt

      sudo -u vagrant dpkg-source --no-check --extract vips_8.10.5-3.dsc build_amd64
      sudo -u vagrant dpkg-source --no-check --extract vips_8.10.5-3.dsc build_arm64
  #   sudo mk-build-deps --tool "apt-get --yes --no-install-recommends" --install ./build_amd64/debian/control
  #   cd ./build_amd64
  #   debuild --unsigned-source --unsigned-buildinfo --unsigned-changes
  #   sudo apt purge vips-build-deps
  #   sudo apt autoremove
  #   sudo mk-build-deps --host-arch arm64 --tool "apt-get --yes --no-install-recommends" --install ./build_amd64/debian/control
  #   cd ./build_arm64
  #   debuild --host-arch arm64 --unsigned-source --unsigned-buildinfo --unsigned-changes
  SHELL
  
end
