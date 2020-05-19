# Terrafrom OpenShift Container Platform 4.3 Installater for Cisco UPI

Terraform Installater for OpenShift COntainer Platform 4.3 on VMware deployed on Cisco User Provisioned Infrastructure 

# Pre-Requisites

* VMware Infrastructure deployed on Cisco Converged Infrastructiure
* terraform

# OCP 4.3 Cluster Installation 

1. Create an install-config.yaml.

```
apiVersion: v1
baseDomain: devcluster.openshift.com
metadata:
  name: mstaeble
networking:
  machineNetwork:
  - cidr: "139.178.89.192/26"
platform:
  vsphere:
    vCenter: vcsa.vmware.devcluster.openshift.com
    username: YOUR_VSPHERE_USER
    password: YOUR_VSPHERE_PASSWORD
    datacenter: dc1
    defaultDatastore: nvme-ds1
pullSecret: YOUR_PULL_SECRET
sshKey: YOUR_SSH_KEY
```

2. Run `openshift-install create ignition-configs`.

3. Fill out a terraform.tfvars file with the ignition configs generated.
There is an example terraform.tfvars file in this directory named terraform.tfvars.example. The example file is set up for use with the dev cluster running at vcsa.vmware.devcluster.openshift.com. At a minimum, you need to set values for the following variables.
* cluster_id
* cluster_domain
* vsphere_user
* vsphere_password
* ipam_token
* bootstrap_ignition_url
* control_plane_ignition
* compute_ignition
The bootstrap ignition config must be placed in a location that will be accessible by the bootstrap machine. For example, you could store the bootstrap ignition config in a gist.

4. Run `terraform init`.

5. Run `terraform apply -auto-approve`.
This will reserve IP addresses for the VMs.

6. Run `openshift-install wait-for bootstrap-complete`. Wait for the bootstrapping to complete.

7. Run `terraform apply -auto-approve -var 'bootstrap_complete=true'`.
This will destroy the bootstrap VM.

8. Run `openshift-install wait-for install-complete`. Wait for the cluster install to finish.

9. Enjoy your new OpenShift cluster.

10. Run `terraform destroy -auto-approve`.
