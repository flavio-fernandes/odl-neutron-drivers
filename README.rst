odl-neutron-drivers
===================

This repository hosts the OpenDaylight project's OpenStack Neutron drivers.

'''NOTE: This repo is now deprecated.'''. Please refer to this link for more info:

   https://lists.opendaylight.org/pipermail/ovsdb-dev/2015-February/001132.html

Usage
-----

To use these drivers with Devstack....

1) Check out the repo and install the drivers::

    git clone https://github.com/dave-tucker/odl-neutron-drivers.git
    cd odl-neutron-drivers.git
    sudo python setup.py install

2) Edit your local.conf::

    Q_PLUGIN=ml2
    # Use "odl" instead of "opendaylight" to pick up the new driver
    Q_ML2_PLUGIN_MECHANISM_DRIVERS=odl,logger
    ODL_MGR_IP=192.168.50.1
    ENABLE_TENANT_TUNNELS=True
    Q_ML2_TENANT_NETWORK_TYPE=vxlan
    Q_ML2_L3_PLUGIN=odl-router
    Q_SERVICE_PLUGIN_CLASSES=firewall,lbaas

    [[post-config|/etc/neutron/plugins/ml2/ml2_conf.ini]]
    [agent]
    minimize_polling=True

    # Note this was formerly "ml2_odl"
    [odl_rest]
    url=http://192.168.50.1:8080/controller/nb/v2/neutron
    username=admin
    password=admin

    # For L3
    [[post-config]|/etc/neutron/l3_agent.ini]]
    [DEFAULT]
    interface_driver = neutron.agent.linux.interface.NullDriver

    # For FWaaS
    [[post-config]|/etc/neutron/fwaas_driver.ini]
    [fwaas]
    driver = odldrivers.fwaas.driver.OpenDaylightFwaasDriver
    enabled = True

    # For LBaaS
    [[post-config]|/etc/neutron/lbaas_agent.ini]
    [DEFAULT]
    device_driver = odldrivers.lbaas.driver.OpenDaylightLbaasDriver

3) Start devstack::

    cd devstack
    ./stack.sh

Testing
-------

A Vagrantfile is provided to easily create a DevStack environment to test with
First, run the ODL Controller on your local machine, then::

    vagrant up

If you would like more than one compute node, you can set the following environment variable::

    #Note: Only 3 or less nodes are supported today
    DEVSTACK_NUM_COMPUTE_NODES=3
