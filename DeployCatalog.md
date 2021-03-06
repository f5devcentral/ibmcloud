# Deploy F5 BIG-IP Virtual Edition on IBM Public Cloud via IBM Catalog

<img src="/images/overview.png" width="400" height="300"/>

In this configuration, traffic is flowing through BIG-IP VE to application servers. The BIG-IP virtual server is listening for traffic destined for port 443. Port 8443 is for management traffic.

All access to the BIG-IP VE appliance is through the same IP address and virtual network interface (vNIC). This single NIC deployment has the following benefits:

- BIG-IP VE creates networking objects (a management VLAN, and an internal self IP address, security groups and floating IP).
- The BIG-IP VE sets the Configuration utility port to 8443 (instead of 443).

## Supported Environments:
- Single-NIC, standalone
- BYOL Licensing
- Throughput up to 1 Gbps

## Prerequisites:
- Must have access to a VPC in IBM VPC Gen 2.
- Must have one subnet configured to deploy the BIG-IP.  This subnet will be used for both management and data traffic.
- Ensure that you have the following permissions in IBM Cloud Identity and Access Management:
    * `Manager` service access role for IBM Cloud Schematics
    * `Operator` platform role for VPC Infrastructure
- An ssh key, to ssh into your instance after deployment.

## Notes
- The resource group used by the tile when creating resources will use the same one attributed to the original subnet.  For example, if the subnet you specify to deploy on belongs in the "default" resource group, the BIG-IP, security groups and floating IP will belong to the "default" as well.

## Step Summary

1. Login to your IBM Cloud Account and Navigate to the IBM Catalog.
2. Search for "F5" in the search bar.  

<img src="/images/f5_tile.png" width="200" height="200"/>

3. Click on the "F5 BIG-IP Virtual Edition for VPC Gen 2 - 1-NIC" tile.
4. Set the deployment values (note that there are two sections of variables, one for those with default values and one for those without)

_Deployment variables without defaults_
![](/images/no_default.png)

_Deployment variables with defaults_
![](/images/default.PNG)

| Key | Default | Definition |
| --- | ------- | ---------- |
| `region` | null | The VPC Zone that you want your VPC virtual servers to be provisioned. To list available zones, run `ibmcloud is regions`.  Currently BIG-IP is supported in the us-east, us-south, eu-gb and eu-de zones. |
| `ssh_key_name` | null | The name of your public SSH key to be used to login to the BIG-IP instance. Follow [Public SSH Key Doc](https://cloud.ibm.com/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-ssh-keys) for creating and managing ssh keys. |
| `subnet_id` | null | The ID of the subnet where the BIG-IP interface will be deployed. Click on the subnet details in the VPC Subnet Listing to determine this value. | 
| `instance_name` | null | The hostname of the BIG-IP instance to be provisioned.  Note that the system will add ".local" to this name. Please note that this name should contain only lowercase alphanumbeic characters and dashes and should begin with a lowercase character. 
| `tmos_admin_password` | null | The password to be used for the admin account on the BIG-IP GUI.  If left blank, this will generate a random, 32 byte password. |
| `vnf_vpc_image_name` | bigip-14-1-2 | The name of the BIG-IP custom image that will temporarily be created in your account.|
| `instance_profile` | cx2-2x4 | The profile of compute CPU and memory resources to be used when provisioning the BIG-IP instance. To list available profiles, run `ibmcloud is instance-profiles`.  See below for recommended profiles. |
| `tmos_license_basekey` | null | The registration key to be used to license the BIG-IP.  Leave blank if you wish to license the BIG-IP later. |

_Instance Profiles_
| Profile Name | Throughput Max (per IBM) | Recommendations |
| ------------ | ------------------------ | --------------- |
| `cx2-2x4` | 4 Gbps | Ideal for Good or LTM license up to 1 Gbps (note that startup times may be extended due to memory utilization) |
| `cx2-4x8` | 8 Gbps | Ideal for Better, WAF and AWAF license up to 1 Gbps |
| `cx2-8x16` | 16 Gbps | Ideal for Best license up to 10 Gbps or Good/Better/WAF/AWAF throughputs above 1 Gbps |
| `cx2-16x32` | 64 GBps | Ideal for performance requiring 8 cores or more |

5. Once these values are set, click the checkbox for accepting the license agreement, and then choose "Install".  You will then be taken to the Schematics / Workspace page where you can "View log" to see how the deployment is progressing.

6. This process will take about 10 minutes to complete.  Note, however, that the instance will be shown as available on the IBM Cloud before the process ends.  This is because F5 TMOS is still booting itself.  Within a few minutes you will be able to access the GUI or ssh into the instance.
