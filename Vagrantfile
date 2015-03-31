# Vagrantfile
#   Defines the virtual machine and setup process for running Canvas
#
# Created 2015-03-26 daveadams@gmail.com
#
# https://github.com/daveadams/canvas-vagrant
#
# This software is public domain.
#
# -*- mode: ruby -*-
# vi: set ft=ruby :

SingleServerConfig = {
  app_hosts: %w( canvas ),
  db_hosts: %w( canvas ),
  redis_hosts: %w( canvas ),
  job_host: "canvas"
}

MultiServerConfig = {
  app_hosts: %w( app1 app2 ),
  db_hosts: %w( db ),
  redis_hosts: %w( redis ),
  job_host: "app1"
}

SimpleMultiServerConfig = {
  app_hosts: %w( app1 ),
  db_hosts: %w( db ),
  redis_hosts: %w( db ),
  job_host: "app1"
}

canvas_config = if ENV["CANVAS_SERVER_CONFIG"] == "simplemulti"
                  SimpleMultiServerConfig
                elsif ENV["CANVAS_SERVER_CONFIG"] == "multi"
                  MultiServerConfig
                else
                  SingleServerConfig
                end


# As of 2015-03-31, Vagrant uses Ruby 2.0, which does not
# include Array#to_h. However, the alternative syntax for
# converting an array of two-member arrays into a hash is
# confusing:
#
#    Hash[ array_expression ]
#
# It is more clear to use to_h to create a has, so if #to_h
# is not defined as an Array instance method:
unless Array.instance_methods.include? :to_h
  # HACK: monkeypatch Array to add #to_h
  class Array
    def to_h
      Hash[self]
    end
  end
end

# assign each VM a unique private IP
last_octet = 100
vm_settings = canvas_config.values.flatten.uniq.collect do |hostname|
  [ hostname, { "private_ip" => "192.168.100.#{last_octet += 1}" } ]
end.to_h

Vagrant.configure(2) do |config|
  config.vm.box = "trusty64"
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"

  config.vm.provision :ansible do |ansible|
    ansible.groups = {
      "app" => canvas_config[:app_hosts],
      "db" => canvas_config[:db_hosts],
      "redis" => canvas_config[:redis_hosts],
      "job" => canvas_config[:job_host]
    }
    ansible.extra_vars = {
      host_settings: vm_settings
    }
    ansible.playbook = "playbooks/canvas.yml"
  end

  vm_settings.keys.each do |hostname|
    config.vm.define hostname do |m|
      m.vm.hostname = hostname
      #m.vm.network :forwarded_port, guest: 80, host: 8080
      m.vm.network :private_network, ip: vm_settings[hostname]["private_ip"]

      m.vm.provider "virtualbox" do |vb|
        vb.memory = "4096"
        vb.cpus = 2
      end
    end
  end
end
