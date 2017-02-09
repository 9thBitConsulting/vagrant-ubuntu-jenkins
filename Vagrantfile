# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
JENKINS_USERNAME = "admin"
JENKINS_PASSWORD = "admin"

require 'yaml'
current_dir    = File.dirname(File.expand_path(__FILE__))
ext_conf       = YAML.load_file("#{current_dir}/options.yaml")
general_opts   = ext_conf.select {|hash| hash["option"] == "general"}
machines       = ext_conf.select {|hash| hash["option"] == "machine"}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Map NFS uid/gid to current user. This can be used to create a
  # user inside the VM with matching uid/gid which makes file access
  # a lot easier.
  #config.nfs.map_uid = Process.uid
  #config.nfs.map_gid = Process.gid

  # This section should help speed up provisioning by caching packages locally,
  # because Gerhard does not want to believe me that our link is dog slow.

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
    config.cache.synced_folder_opts = {
      type: :nfs,
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
    }
    # For more information please check http://docs.vagrantup.com/v2/synced-folders/basic_usage.html
  end

  # DNS for boxes
  if Vagrant.has_plugin?("vagrant-dns")
    config.dns.tld = general_opts.first["tld"]
    config.dns.patterns = []
    VagrantDNS::Config.logger = Logger.new("dns.log")
  end
  
  machines.each do |machines|

    config.vm.define machines["name"], primary: machines["primary"] do |srv|

      srv.vm.box = machines["box"]
      srv.ssh.insert_key = true
      srv.vm.synced_folder ".", "/vagrant", disabled: true

      srv.vm.provider :virtualbox do |v|
        v.name = "jenkins"
        v.memory = machines["memory"]
        v.cpus = 2
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["modifyvm", :id, "--ioapic", "on"]
      end

      srv.vm.hostname = "jenkins"
      srv.vm.network :private_network, ip: machines["priv_ip"]

      # Set the name of the VM. See: http://stackoverflow.com/a/17864388/100134
      #config.vm.define :jenkins do |jenkins|
      #end

    # Ansible provisioner.
      srv.vm.provision "ansible" do |ansible|
        ansible.playbook = "provisioning/ansible/playbook.yml" 
        #ansible.inventory_path = "provisioning/inventory"
        ansible.sudo = true
      end
      # Add FQDN
        if Vagrant.has_plugin?("vagrant-dns")
          fqdn = "/#{machines['hostname']}.#{general_opts.first['domain']}.#{general_opts.first['tld']}$/"
          config.dns.patterns.push fqdn
        end
    end
  end
end
