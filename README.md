# Puppet

*** Prerequisites

• A fresh Puppet Master with Ubuntu 20.04 installed.
• A fresh Puppet Agent/Client with Ubuntu 20.04 installed.
• A static IP address  is configured on Puppet Master.
• A static IP address  is configured on Puppet Agent.
• Minimum 4 GB Memory and Dual-Core CPU is required for Puppet Master.


*** Master Node
/// Stop the firewall if u r using virtual machines in your own small private network but it is not recommand in an organization
$ sudo systemctl stop firewalld

/// Update Package List
$ sudo apt-get update -y

/// Set Up Hostname Resolution
$ ifconfig   [copy ip address]
$ sudo nano /etc/hosts
[puppet master ip       Name  ]
   192.168.1.6      puppet-master
   10.0.2.15        puppet-agent       [copy agent hosts] 
Press Ctrl + X to close the file, and then type Y and press Enter to save the changes you made.
$ sudo nano /etc/hostname
puppet-master

/// Install Puppet Server on Master Node

// Download the latest Puppet version on the master node
$ wget https://apt.puppetlabs.com/puppet6-release-focal.deb

// Once the download is complete, install the package by using
$ sudo dpkg -i puppet6-release-focal.deb

// Update the package repository
$ sudo apt-get update -y

// Install the Puppet server with the following command
$ sudo apt-get install puppetserver -y

/// Open the puppetserver file by using 
$ sudo nano /etc/default/puppetserver
In the puppetserver file, modify the following line to change the memory size 1GB to 512m

JAVA_ARGS="-Xms512m -Xmx512m -Djruby.logger.class=com.puppetlabs.jruby_utils.jruby.Slf4jLogger

//Start the Puppet service and set it to launch on system boot by using:
$ sudo systemctl start puppetserver
$ sudo systemctl enable puppetserver

// Check if the Puppet service is running with
$ sudo systemctl status puppetserver


*** Agent/Client Node


// Set Up Hostname Resolution
$ ifconfig   [copy ip address]
$ sudo nano /etc/hosts
[puppet agent ip   puppe-agent]
   192.168.1.6     puppet-master  [add master hosts name] 
   10.0.2.15       puppet-agent
Press Ctrl + X to close the file, and then type Y and press Enter to save the changes you made.
$ sudo nano /etc/hostname
puppet-agent

/// Install Puppet Agent on Client Node

// Download the latest version of Puppet on a client node
$ wget https://apt.puppetlabs.com/puppet6-release-focal.deb

// Once the download is complete, install the package by using
$ sudo dpkg -i puppet6-release-focal.deb

// Update the package repository one more time
$ sudo apt-get update -y

// Install the Puppet agent by using
$ sudo apt-get install puppet-agent -y

// Open the Puppet configuration file
$ sudo nano /etc/puppetlabs/puppet/puppet.conf

// Add the following lines to the end of the Puppet configuration file to define the Puppet master information
[main]
certname = puppet-agent
server = puppet-master

// Start the Puppet service and set it to launch on system boot by using
$ sudo systemctl start puppet
$ sudo systemctl enable puppet

// Check if the Puppet service is running with
$ sudo systemctl status puppet


*** Master Node


/// Sign Puppet Agent Certificate

// Using the Puppet master node, list all the available certificates
$ sudo /opt/puppetlabs/bin/puppetserver ca list --all

// Sign the certificates with
$ sudo /opt/puppetlabs/bin/puppetserver ca sign --all

// Use the following command to test the communication between the master and client nodes
$ sudo /opt/puppetlabs/bin/puppet agent --test
