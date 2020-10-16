# -*- mode: ruby -*-
# vi: set ft=ruby :

# Require the AWS provider plugin and YAML module
require 'vagrant-aws'
require 'yaml'

# Workaround https://github.com/mitchellh/vagrant-aws/issues/566
class Hash
  def slice(*keep_keys)
    h = {}
    keep_keys.each { |key| h[key] = fetch(key) if has_key?(key) }
    h
  end unless Hash.method_defined?(:slice)
  def except(*less_keys)
    slice(*keys - less_keys)
  end unless Hash.method_defined?(:except)
end

# Read YAML file with instance information
inventory = YAML.load_file(File.join(File.dirname(__FILE__), 'inventory.yml'))
instances = inventory['instances']

# Specify Vagrant version verified
Vagrant.require_version '>= 2.2.0'

# Create and configure the AWS instance(s)
Vagrant.configure("2") do |config|

  # Use dummy AWS box
  config.vm.box = "dummy"

  # Enable vagrant-env(.env)
  config.env.enable

  # Specify AWS provider configuration
  config.vm.provider 'aws' do |aws|

    # Set AWS authentication information from environment variables
    aws.access_key_id = ENV['AWS_ACCESS_KEY_ID']
    aws.secret_access_key = ENV['AWS_SECRET_ACCESS_KEY']

    # Specify region, AMI ID, and security group(s)
    aws.security_groups = inventory['security_groups']
    aws.region = inventory['region']

    # use the region_config method to specify region-specific overrides
    aws.region_config "us-west-1" do |region|
      region.ami = inventory['ami-us-west-1']
    end
    aws.region_config "us-east-2" do |region|
      region.ami = inventory['ami-us-east-2']
    end
  end # config.vm.provider 'aws'

  # Loop through instances and set per-instance information
  instances.each do |instance|
    config.vm.define instance['name'] do |srv|

      # Disable default shared folder
      # srv.vm.synced_folder '.', '/vagrant', disabled: true

      # Set per-instance provider configuration/overrides
      srv.vm.provider 'aws' do |aws, override|
        # Tag the EC2 instance, at least give it a name
        aws.tags = {
          'Name' => instance['name']
        }

        # Set the instance falvor
        aws.instance_type = instance['type']

        # Specify SSH keypair on EC2 to use
        aws.keypair_name = ENV['AWS_SSH_KEYPAIR_NAME']

        # Specify VM SSH access username and local private key path for the keypair
        override.ssh.username = inventory['user']
        override.ssh.private_key_path = ENV['AWS_SSH_KEYPAIR_PEM']
      end # srv.vm.provider 'aws'
    end # config.vm.define
  end # instances.each
end # Vagrant.configure
