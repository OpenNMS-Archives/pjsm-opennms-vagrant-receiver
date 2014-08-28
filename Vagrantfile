VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Use a small CentOS 6.5 and build and install OpenNMS in Vagrant
  # config.vm.box = "opscode_centos-6.5"
  # config.vm.box_url = "http://mirror.opennms.eu/pub/vagrant/stable/opscode_centos-6.5_chef-provisionerless.box"

  # Use an image where OpenNMS is completely pre-installed and just change configuration
  config.vm.box = "opennms-pjsm-centos-6.5"
  config.vm.box_url = "http://mirror.opennms.eu/pub/vagrant/opennms-pjsm/pjsm-opennms-centos-6.5-development.box"

  config.vm.network "forwarded_port", guest: 8980, host: 1234
  config.vm.network :public_network

  config.vm.provider :virtualbox do |vb|
    # Use VBoxManage to customize the VM. For example to change memory:
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    # vb.gui = true
  end

  # This installs chef into the created box
  config.vm.provision :shell do |shell|
    shell.inline = "which chef-client || wget -qO- https://www.opscode.com/chef/install.sh | bash"
  end

  config.vm.provision :chef_solo do |chef|
    chef.json = {
      :opennms => {
        :release => "branches/pjsm-2.0",
        :home => "/opt/opennms",
        :discovery => {
          :foreignsource => "UK-Store-1",
          :range => {
            :start => "10.23.42.1",
            :end => "10.23.42.254"
          },
          :threads => "5"
        },
        :reqPush => {
          :baseUrl => "http://172.20.1.115:8980",
	      :restUser => "admin",
	      :restPasswd => "admin"
	    },
        :activemq => {
          :eventforwarder => {
            :location => "Birmingham"
          }
        }
      },
      :activemq => {
        :server => {
          :dispatcher => {
            :broker_name => "my-uk-store-dispatcher-01",
            :brokerUri => "tcp://127.0.0.1:61616"
          }
        }
      },
      :java => {
        :install_flavor => "oracle",
        :jdk_version => "7",
        :oracle_rpm => {
          :type => "jdk"
        },
        :oracle => {
          :accept_oracle_download_terms => "true"
        }
      }
    }
    chef.cookbooks_path = "cookbooks"
    chef.add_recipe "opennms-light::event-receiver"
  end
end
