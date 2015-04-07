# Vagrantfile
#   Defines the virtual machine and setup process for running Canvas
#
# This software is public domain.
#
# -*- mode: ruby -*-
# vi: set ft=ruby :
#


case ENV["CANVAS_SERVER_CONFIG"]
when "dual"
  canvas_config = {
    servers: {
      db: {
        private_ip: "192.168.100.100",
        memory: 2048,
        cpus: 2,
      },
      app: {
        private_ip: "192.168.100.101",
        memory: 2048,
        cpus: 2,
        forwarded_ports: [ { guest: 3000, host: 3000 } ],
      },
    },
    ansible: {
      groups: {
        "app" => %w( app ),
        "db" => %w( db ),
        "redis" => %w( db ),
        "job" => %w( app ),
      },
      extra_vars: {
        canvas_db_host: "192.168.100.100",
      },
    },
  }

when "multi"
  canvas_config = {
    servers: {
      db: {
        private_ip: "192.168.100.100",
        memory: 2048,
        cpus: 2,
      },
      app1: {
        private_ip: "192.168.100.101",
        memory: 2048,
        cpus: 2,
      },
      app2: {
        private_ip: "192.168.100.102",
        memory: 2048,
        cpus: 2,
      },
      redis: {
        private_ip: "192.168.100.103",
        memory: 2048,
        cpus: 2,
      },
      job: {
        private_ip: "192.168.100.104",
        memory: 2048,
        cpus: 2,
      },
      lb: {
        private_ip: "192.168.100.105",
        memory: 2048,
        cpus: 2,
        forwarded_ports: [ { guest: 80, host: 8000 } ],
      },
    },
    ansible: {
      groups: {
        "app" => %w( app1 app2 ),
        "db" => %w( db ),
        "redis" => %w( redis ),
        "job" => %w( job ),
        "lb" => %w( lb ),
      },
      extra_vars: {
        canvas_db_host: "192.168.100.100",
      },
    },
  }

else
  canvas_config = {
    servers: {
      canvas: {
        private_ip: "192.168.100.100",
        memory: 4096,
        cpus: 4,
        forwarded_ports: [ { guest: 3000, host: 3000 } ],
      }
    },
    ansible: {
      groups: {
        "app" => %w( canvas ),
        "db" => %w( canvas ),
        "redis" => %w( canvas ),
        "job" => %w( canvas ),
      },
      extra_vars: {
        canvas_db_host: "192.168.100.100",
      },
    },
  }
end

Vagrant.configure(2) do |config|
  config.vm.box = "trusty64"
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"

  config.vm.provision :ansible do |ansible|
    ansible.groups = canvas_config[:ansible][:groups]
    ansible.extra_vars = canvas_config[:ansible][:extra_vars]
    ansible.tags = ENV["ANSIBLE_TAGS"] if ENV["ANSIBLE_TAGS"]
    ansible.playbook = "playbooks/canvas.yml"
  end

  canvas_config[:servers].keys.each do |hostname|
    config.vm.define hostname do |m|
      m.vm.hostname = hostname
      if canvas_config[:servers][hostname][:forwarded_ports]
        canvas_config[:servers][hostname][:forwarded_ports].each do |forwarded_port|
          m.vm.network :forwarded_port, guest: forwarded_port[:guest], host: forwarded_port[:host]
        end
      end
      m.vm.network :private_network, ip: canvas_config[:servers][hostname][:private_ip]

      m.vm.provider "virtualbox" do |vb|
        vb.memory = canvas_config[:servers][hostname][:memory]
        vb.cpus = canvas_config[:servers][hostname][:cpus]

        # use nat dns to avoid bizarre dns slowdowns
        vb.customize ["modifyvm", :id, "--natdnsproxy1", "off"]
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "off"]
      end
    end
  end
end
