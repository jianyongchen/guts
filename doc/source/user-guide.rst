..
    Copyright (c) 2015 Aptira Pty Ltd.
    All Rights Reserved.

       Licensed under the Apache License, Version 2.0 (the "License"); you may
       not use this file except in compliance with the License. You may obtain
       a copy of the License at

            http://www.apache.org/licenses/LICENSE-2.0

       Unless required by applicable law or agreed to in writing, software
       distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
       WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
       License for the specific language governing permissions and limitations
       under the License.

==========
User Guide
==========

GUTS is an open-source project that aims to make the move to an OpenStack cloud
easier. It addresses various difficulties that operators and administrators 
may face when migrating workloads from existing traditional virtualisation
platforms on to OpenStack cloud.

This guide shows GUTS users how to create and manage various migration entities
with the GUTS command line clients (CLI).

Manage Source Hypervisors
~~~~~~~~~~~~~~~~~~~~~~~~~
    
  * Create a new Source Hypervisor
    
    .. code-block:: console
    
       $ guts source-create vmware_source \
           --driver guts.migration.drivers.sources.vsphere.VSphereSourceDriver \
           --capabilities instance \
           --registered_host guts-environment \
           --credentials "{'host':'<HOST_IP>','username':'<USERNAME>','password':'<PASSWORD>','port':'<PORT>'}"
         +-----------------+--------------------------------------+
         | Property        | Value                                |
         +-----------------+--------------------------------------+
         | Binary          | guts-source                          |
         | Host            | guts-environment                     |
         | Hypervisor Name | vmware_source                        |
         | ID              | f57bb9c4-b319-4d71-a3c6-d3a815a87359 |
         +-----------------+--------------------------------------+

  * List all Source Hypervisors
    
    .. code-block:: console
    
       $ guts source-list
         +--------------------------------------+-----------------+------------------+--------+
         |                  ID                  | Hypervisor Name |       Host       | Status |
         +--------------------------------------+-----------------+------------------+--------+
         | f57bb9c4-b319-4d71-a3c6-d3a815a87359 |  vmware_source  | guts-environment |   Up   |
         +--------------------------------------+-----------------+------------------+--------+    

  * Show Source Hypervisor Details
    
    .. code-block:: console
    
       $ guts source-show <SOURCE_ID>
         +-----------------+--------------------------------------+
         | Property        | Value                                |
         +-----------------+--------------------------------------+
         | Binary          | guts-source                          |
         | Host            | guts-environment                     |
         | Hypervisor Name | vmware_source                        |
         | ID              | f57bb9c4-b319-4d71-a3c6-d3a815a87359 |
         +-----------------+--------------------------------------+    


Manage Destination Hypervisors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

  * Create a new Destination Hypervisor

    .. code-block:: console

       $ guts destination-create openstack_destination \
           --driver guts.migration.drivers.destinations.openstack.OpenStackDestinationDriver \
           --capabilities instance,network,volume \
           --registered_host guts-environment \
           --credentials "{'auth_url':'<KEYSTONE_AUTH_URL>','username':'<USER>','password':'<PASSWORD>','tenant_name':'<TENANT_NAME>}"
         +--------------------------------------+-----------------------+------------------+--------+
         |                  ID                  |    Hypervisor Name    |       Host       | Status |
         +--------------------------------------+-----------------------+------------------+--------+
         | a017eac1-dd81-4f66-936e-05d31951781e | openstack_destination | guts-environment |   Up   |
         +--------------------------------------+-----------------------+------------------+--------+

  * List all Destination Hypervisors

    .. code-block:: console

       $ guts destination-list
         +--------------------------------------+-----------------------+------------------+--------+
         |                  ID                  |    Hypervisor Name    |       Host       | Status |
         +--------------------------------------+-----------------------+------------------+--------+
         | a017eac1-dd81-4f66-936e-05d31951781e | openstack_destination | guts-environment |   Up   |
         +--------------------------------------+-----------------------+------------------+--------+

  * Show Destination Hypervisor Details

    .. code-block:: console

       $ guts source-show <DESTINATION_ID>
         +-----------------+--------------------------------------------------------------------------------------------------+
         | Property        | Value                                                                                            |
         +-----------------+--------------------------------------------------------------------------------------------------+
         | Binary          | guts-destination                                                                                 |
         | Host            | guts-environment                                                                                 |
         | Hypervisor Name | openstack_destination                                                                            |
         | ID              | a017eac1-dd81-4f66-936e-05d31951781e                                                             |
         | properties      | {'flavors': [u'm1.tiny', u'm1.small', u'm1.medium', u'm1.large',                                 
                              u'm1.nano', u'm1.xlarge', u'm1.micro', u'cirros256', u'ds512M', u'ds1G', u'ds2G', u'ds4G'],     
                              'keypairs': [], 'networks': [u'private'], 'secgroups': [u'default']}                            
         +-----------------+--------------------------------------------------------------------------------------------------+
             
Manage Source Resources
~~~~~~~~~~~~~~~~~~~~~~~
    
  * List all avalilable Resources
    
    .. code-block:: console
    
       $ guts resource-list
         +--------------------------------------+---------------------------+----------+-----------------+----------+
         |                  ID                  |            Name           |   Type   | Hypervisor Name | Migrated |
         +--------------------------------------+---------------------------+----------+-----------------+----------+
         | 29042f03-a330-4b38-9c63-87ea99ed3c86 |        XXXXXXXXXXXX       | instance |  vmware_source  |  False   |
         | 29ca34a9-4713-4db1-9ab9-ffc71f201fe1 |        XXXXXXXXXXXX       | instance |  vmware_source  |  False   |
         | 3ef3e64b-84c6-478d-a484-6a17c99c4a3b |        XXXXXXXXXXXX       | instance |  vmware_source  |  False   |
         | 5c78d0ec-ebf0-49f9-a962-e611804d60ed |        XXXXXXXXXXXX       | instance |  vmware_source  |   True   |
         +--------------------------------------+---------------------------+----------+-----------------+----------+
    
  * List all Instances
    
    .. code-block:: console
    
       $ guts instance-list
    
  * List all Resources

    .. code-block:: console

       $ guts resource-list

  * List all Networks

    .. code-block:: console

       $ guts network-list

    
Manage Migrations
~~~~~~~~~~~~~~~~~
    
  * Create a new Migration process
    
    .. code-block:: console
    
       $ guts create --name <MIGRATION_NAME> \
           --destination <DESTINATION_NAME> \
           --extra_param "{'flavor':<FLAVOR_ID>,'secgroup':'<SECURITYGROUP_NAME>','network':'<NETWORK_NAME>','keypair':'<KEYPAIR_NAME>'}" \
           <RESOURCE_ID>
         +--------------------------------------+---------------+--------+-------+----------------------+--------------------------------------+
         |                  ID                  |      Name     | Status | Event |     Description      |          Source Instance ID          |
         +--------------------------------------+---------------+--------+-------+----------------------+--------------------------------------+
         | efbb708d-b9c3-4f8d-85c7-d814994ccff4 | XXXXXXXXXXXXX |   -    |   -   | Sample VM1 Migration | 12821516-7ff0-4a76-9b7b-bb56df54b300 |
         +--------------------------------------+---------------+--------+-------+----------------------+--------------------------------------+

      .. note::

         Guts supports the following migration types.
         +---------------------+-----------------------------+------------------------+
         | Source_Hypervisor   | Resources                   | Destination_hypervisor |
         +=====================+=============================+========================+
         | OpenStack           | Instances,Networks,Volumes  | OpenStack              |
         +---------------------+-----------------------------+------------------------+
         | VMware              | Instances                   | Openstack              |
         +---------------------+-----------------------------+------------------------+
      ..

    
  * List all Migrations
    
    .. code-block:: console
    
       $ guts list
         +--------------------------------------+-----------+----------+-------+-------------+---------------+------------------------+
         |                  ID                  |    Name   |  Status  | Event | Resource ID | Resource Type | Destination Hypervisor |
         +--------------------------------------+-----------+----------+-------+-------------+---------------+------------------------+
         | d16aa1a0-f6dc-4588-b615-008455d27ed2 | XXXXXXXXX | COMPLETE |   -   |   XXXXXXX   |    instance   | openstack_destination  |
         +--------------------------------------+-----------+----------+-------+-------------+---------------+------------------------+ 
