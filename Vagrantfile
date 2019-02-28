NETWORK_IP = '' # Leave blank for DHCP, or `192.168.x.x` for a static IP
NETWORK_TYPE = 'private' # Valid values: `public` or `private`
VM_MEMORY = 2048 # VM RAM usage.
VM_CPUS = 1 # VM CPU count.
VM_CPU_CAP = 50 # CPU execution cap percentage.

Vagrant.configure(2) do |config|

  # https://app.vagrantup.com/boxes/search
  config.vm.box = 'ubuntu/trusty64'
  config.vm.box_version = '20190226.0.0'

  # Defaults for forwarded port settings:
  forwarded_port_defaults = {
    auto_correct: true,
  }

  # HTTP:
  config.vm.network(
    'forwarded_port',
    forwarded_port_defaults.merge!({
      guest: 80,
      host: 80,
    })
  )
  # HTTPS:
  config.vm.network(
    'forwarded_port',
    forwarded_port_defaults.merge!({
      guest: 443,
      host: 443,
    })
  )

  # Network configuration:
  config.vm.network(
    (NETWORK_TYPE + '_network'),
    (
      if NETWORK_IP.to_s.empty?
        {
          type: 'dhcp',
        }
      else
        {
          ip: NETWORK_IP,
        }
      end
    )
  )

  config.vm.provider 'virtualbox' do |vb|

    # Boost memory usage:
    vb.customize [
      'modifyvm',
      :id,
      '--memory',
      VM_MEMORY,
    ]

    # Number of cpus:
    vb.customize [
      'modifyvm',
      :id,
      '--cpus',
      VM_CPUS,
    ]

    # Number of cpus:
    vb.customize [
      'modifyvm',
      :id,
      '--cpuexecutioncap',
      VM_CPU_CAP,
    ]

  end

  # SSH agent forwarding?
  config.ssh.forward_agent = true

  # Shared directory configuration defaults (disabled if Windows):
  synced_folder_defaults = {
    disabled: ((Vagrant::Util::Platform.windows?) ? true : false),
    type: 'virtualbox',
    create: true,
    owner: 'vagrant',
    group: 'vagrant',
    mount_options: [
      'dmode=775',
      'fmode=664',
    ],
  }

  # Adjust the default share:
  config.vm.synced_folder(
    './vagrant',
    '/vagrant',
    synced_folder_defaults.merge!({
      # Default overrides?
    })
  )

  # Command to obtain IP address of guest VM:
  show_network_ip = 'echo "NETWORK IP: $(hostname -I | cut -d \  -f2)"'

  config.vm.provision(
    'shell',
    {
      run: 'always',
      inline: show_network_ip,
    }
  )

  config.vm.provision(
    'shell',
    {
      privileged: false,
      path: 'vagrant/bootstrap/init.sh',
    }
  )

  config.vm.provision(
    'shell',
    {
      inline: show_network_ip,
    }
  )

end
