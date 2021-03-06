# openshift-cic

# CNS Inventory file Creator (CIC)

Script which the user runs and then answers a list of questions to create set of inventory_file options to be incorporated into their larger inventory_file for running openshift-ansible playbooks. The goal is to reduce the complexity and error prone nature of needing to know the correct CNS/CRS inventory_file options for a particular OCP/CNS version. The first prototype will be created for OCP 3.9 with the goal of using with OCP 3.7 as well. 

Embedded in the answers will be some calculations or ‘pre-flight checks’. An example is the minimum size for the block_host_vol_size (GB). The minimum size is equal to all of the blockvolume PVs added up for metrics, logging and registry + 30% overhead.

$ ./cic.py 
------------------------------------------------------------
   CNS - Inventory File Creator
------------------------------------------------------------
'' 
This tool is very highly version dependent. It is currently,
configured to work with OpenShift 3.9 and CNS 3.9.
The output is NOT A COMPLETE Inventory File.
Created Inventory file options should be copied and pasted into
the larger openshift-ansible inventory file for your deployment.
 ''
1. Storage for Applications + Registry 
2. Storage for Applications + Logging
3. Storage for Applications + Metrics 
4. Storage for Applications + Registry + Logging + Metrics
5. Storage for Applications Only
------------------------------------------------------------

Enter your choice [1-5] : 4
------------------------------------------------------------
For this configuration 7 nodes are recommended
With a minimum of 3 required 
------------------------------------------------------------
How many nodes are available ?:  3

What hosts will be used for application storage (IP/FQDN) ?: ip-172-16-30-124.us-west-2.compute.internal ip-172-16-34-239.us-west-2.compute.internal ip-172-16-59-115.us-west-2.compute.internal

What are the raw storage devices for these hosts(/dev/<device>) ?: /dev/xvdf /dev/xvdg

What is the size of each raw storage device (GB) ?: 100

What is the size for the registry persistent volume (GB)?: 10

How many replicas for logging ?: 3

What is the size for each logging persistent volume (GB) ?: 25

What is the size for each metrics persistent volume (GB) ?: 20

```
OSEv3:children]
glusterfs
   
# registry
openshift_hosted_registry_storage_kind=glusterfs
openshift_hosted_registry_storage_volume_size=10
openshift_hosted_registry_selector={"region":"infra"}
   
# logging
openshift_logging_install_logging=true
openshift_logging_es_pvc_dynamic=true 
openshift_logging_es_pvc_size= 25
openshift_logging_es_cluster_size=3
openshift_logging_es_pvc_storage_class_name='glusterfs-storage-block'
openshift_logging_kibana_nodeselector={"region":"infra"}
openshift_logging_curator_nodeselector={"region":"infra"}
openshift_logging_es_nodeselector={"region":"infra"}
  
# metrics
openshift_metrics_install_metrics=true 
openshift_metrics_storage_kind=dynamic
openshift_metrics_storage_volume_size=20
openshift_metrics_cassandra_pvc_storage_class_name='glusterfs-storage-block'
openshift_metrics_hawkular_nodeselector:{"region":"infra"}
openshift_metrics_cassandra_nodeselector:{"region":"infra"}
openshift_metrics_heapster_nodeselector:{"region":"infra"}
   
# CNS storage cluster
openshift_storage_glusterfs_namespace=app-storage
openshift_storage_glusterfs_storageclass=true
openshift_storage_glusterfs_storageclass_default=false
openshift_storage_glusterfs_block_deploy=true
openshift_storage_glusterfs_block_host_vol_create=true
openshift_storage_glusterfs_block_host_vol_size=123
openshift_storage_glusterfs_block_storageclass=true
openshift_storage_glusterfs_block_storageclass_default=false
   
[glusterfs]
ip-172-16-30-124.us-west-2.compute.internal glusterfs_devices='["/dev/xvdf", "/dev/xvdg"]'
ip-172-16-34-239.us-west-2.compute.internal glusterfs_devices='["/dev/xvdf", "/dev/xvdg"]'
ip-172-16-59-115.us-west-2.compute.internal glusterfs_devices='["/dev/xvdf", "/dev/xvdg"]'
```
