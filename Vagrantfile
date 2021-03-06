# Reference

# https://github.com/patrickdlee/vagrant-examples/blob/master/example7/Vagrantfile
# https://github.com/calo81/vagrant-hadoop-cluster
# http://www.highlyscalablesystems.com/3597/hadoop-installation-tutorial-hadoop-2-x/
# http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15/bk_cluster-planning-guide/content/typical-hadoop-cluster-hardware.html
# https://github.com/Cascading/vagrant-cascading-hadoop-cluster/blob/2.7/Vagrantfile
# https://github.com/apache/bigtop/tree/master/bigtop-deploy/vm/vagrant-puppet-vm
# http://codiply.com/blog/hadoop-2-6-0-cluster-setup-on-ubuntu-14-04 - Good one for hadoop file config


# Warning

# VT-x/AMD-V is required to be enabled in your bios to boot a 64 bit guest machine
# To check if your CPU supports virtualization refer https://www.grc.com/securable.htm
# If it does, enable the same in BIOS


VAGRANTFILE_API_VERSION = "2"

# master runs HDFS NameNode, YARN ResourceManager, HBase Master (optional)
# slaves run  HDFS DataNode, YARN NodeManager, HBase RegionServers (optional)

# increase ram as needed, add additional slave nodes if needed.

nodes = [
  { :type     => 'master',
    :hostname => 'master.local',
    :ip       => '192.168.48.10',
    :cpus     => '1',
    :ram      => '4096' },

  { :type     => 'slave',
    :hostname => 'hadoop1.local',
    :ip       => '192.168.48.11',
    :cpus     => '1',
    :ram      => '2048' },

  { :type     => 'slave',
    :hostname => 'hadoop2.local',
    :ip       => '192.168.48.12',
    :cpus     => '1',
    :ram      => '2048' },

  { :type     => 'slave',
    :hostname => 'hadoop3.local',
    :ip       => '192.168.48.13',
    :cpus     => '1',
    :ram      => '2048' },

]

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  nodes.each do |node|
    config.vm.define node[:hostname] do |node_config|
      node_config.vm.box = "puppetlabs/ubuntu-14.04-64-puppet"
      node_config.vm.box_version = "1.0.3"  		# optional, can be removed to default to latest version
      node_config.vm.box_check_update = false		# disabling for now
      node_config.vm.host_name = node[:hostname]
      node_config.vm.network "private_network", ip: node[:ip]
      node_config.vm.provider :virtualbox do |vb|
        vb.customize ["modifyvm", :id, "--cpus", node[:cpus]]
        vb.customize ["modifyvm", :id, "--memory", node[:ram]]
        # vb.gui = true                             # uncomment this line to debug virtual machine startup issues
      end
      # Shell provisioning - bootstrap for puppet
      # install - git, ruby and librarian-puppet
      node_config.vm.provision :shell, :path => 'shell/main.sh'
      # Puppet provisioning
      node_config.vm.provision "puppet" do |puppet|
        #puppet.options = "--verbose --debug"      # uncomment to enable verbose mode
        puppet.environment_path = "puppet/environments"
        puppet.environment = "dev"
        puppet.manifests_path = "puppet/environments/dev/manifests"
        puppet.manifest_file = node[:type] + ".pp"
        puppet.module_path = "puppet/modules"
      end
    end
  end
end
