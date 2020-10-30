# vagrant-aws-instances
Create multiple EC2 instance in AWS cloud using vagrant

Prerequisites:

- vagrant
- vagrant-aws vagrant plugin: vagrant plugin install vagrant-aws
- vagrant-env vagrant plugin: vagrant plugin install vagrant-env
- dummy vagrant box: vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box

Usage:

1. Clone the repository
2. Change to your AWS credentials in .env file
3. customize inventory.yml for each instances you want to deploy
4. run vagrant up to bootstrap all the instances in AWS cloud

Note: the base iamge is based on Ubuntu server 18.04LTS, change ani settings in inventory.yml if you prefer different base images
