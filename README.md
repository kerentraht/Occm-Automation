# OCCM Ansible automation usage
This repository contains an automated Ansible pipeline for OCCM management, allowing a fully deployed environment with one command only.
Following is a step-by-step user guide to run your own deployement with Ansible.

Good luck (:
## Step 1: Get your refresh token
### Overview
A refresh token is required in order to run any ansible playbook on OCCM. 
This step can be taken once for all future uses of OCCM automation.
### Instructions
Go to https://services.cloud.netapp.com/refresh-token, click on "Generate refresh token for CloudManager" ( You need to have a Cloud Central Account ), and copy your token to the clipboard.
## Step 2: Get/Create Ansible account ID
### Instructions
1. Use the `account_subscriptions.yml` file to Get your account list. This file is located under "playbooks" folder.
> NOTE: In case an account is not yet defined, this strep will create one.
2. Run the following command from the path in which the file is located with extre-vars as explained below (do not keep the square brackets in the final command).
```
sudo ansible-playbook account_subscriptions.yml --extra-vars "ApiToken=[Cloud Central Key] AccountName=[desired new account name]"
```
+ ApiToken: the refresh Cloud Central API token that was obtained earlier in step 1.
+ AccountName: the desired account name in case you don't have an existing account

### Output example:
```
ok: [localhost] => {
        "msg": [
            {
                "accountName": "xoxoxoxo",
                "accountPublicId": "account-xxxxxxx",
                "userRole": "Role-1"
            },
            {
                "accountName": "yoyoyoyo",
                "accountPublicId": "account-yyyyyyy",
                "userRole": "Role-1"
            }
        ]
    }

TASK [Get Subscriptions for account account-xxxxxxx] **********************************************************************************************************
ok: [localhost] => (item={'cloudProvider': 'aws', 'subscriptionName': 'Prod Account Subscription', 'subscriptionId': 'aws-XXXXXXXXXXXXXXXX-FFFFFFFFFF'})
ok: [localhost] => (item={'cloudProvider': 'gcp', 'subscriptionName': 'GCP SUBSCRIPTION', 'subscriptionId': 'gcp-none-yet-123456789'})
```
## Step 3: Setting up your variables file
In the main directory you can find the `vars.yml` file, which contaoins all the variables that are required to run the automation, such as desired volume name, your connector's ID, etc...
This file should be modified by the user, according to the desired environment.
### Static variables (Never change):
+ auth0_domain: netapp-cloud-account.auth0.com
+ client_id: Mu0V1ywgYteI6w1MbD15fKfVIUrNXGWC
+ cloud_provider_account: InstanceProfile
+ api_backend: cloudmanager.cloud.netapp.com
### User input variables:
+ refToken: The refresh token string (from step1)
+ oconnector_id: Your Cloud Manager Connector ID
+ my_workspace: The workspace name in which you want to deploy the WE
+ envType: Working environment type - **aws** for single node or **awsha** for HA cluster
+ otc_name: The desired working environment's name
+ create_otc: (Boolean) set to 'true' to create a new WE, or 'false' to skip the WE creation
+ providerType: The cloud provider volume type. For AWS: ["gp2", "io1", "st1", "sc1"].
+ region: AWS region
+ vpc_id: AWS vpc Id
+ node1SubnetId: Id of the AWS subnet in which the first node wil be created
+ node2SubnetId: Id of the AWS subnet in which the second node wil be created
+ mediatorSubnetId: Id of the AWS subnet in which the mediator wil be created
+ routeTableIds: Id of VPC's route table
+ instance_type: AWS instance type for the CVO, for example: m4.4xlarge
+ key_pair: AWS key pair that will be used to connect to the CVO instane
+ svmPassword: Choose the login password for the CVO
+ clusterFloatingIP: Choose an IP address outside of your VPC for the cluster managment
+ dataFloatingIP1: Choose an IP address outside of your VPC for the first node
+ dataFloatingIP2: Choose an IP address outside of your VPC for the second node
+ svmFloatingIP: Choose an IP address outside of your VPC

#### Dictionary variables for loops:
The "aggrList" and "volList" variables are dictionary vars representing the aggregates and volumes to create, with their additional vars. 
Each row in the loop represents one volume or aggregate that will be created.
Add/Remove rows according to the desired number of aggrgateds and volume you wish to create.

##### vars for aggregates:
+ aggrName: The name for the new aggregate
+ diskSize: EBS disk size for the creation of the aggregate

##### Vars for iscsi volumes:
+ aggrName: The aggregate in which the volume will be created
+ volName: The name of the new volume
+ iqn: The initiator's host IQN
+ os_name: the initiator's host operating system [linux|windows|vmware]
+ volSize: The size of the volume in TB

## Step 4: Run the playbook
### Instructions
Run the "EnvDeployment.yml" playbook 

### Usage
>NOTE: The followoing command will create the full environment
```
ansible-playbook /playbooks/EnvDeployment.yml
```
Wait for the playbook to complete all tasks successfully!

## Step 5 - Attach the iscsi device to the host

+ Guide reference for linux host can be found here:
https://www.synology.com/en-us/knowledgebase/DSM/tutorial/Virtualization/How_to_set_up_and_use_iSCSI_target_on_Linux

+ Guide reference for windows host can be found here:
https://www.virtualizationhowto.com/2017/07/add-iscsi-shared-storage-in-windows-server-2016/

(c) Keren Trajtenberg (c) Esty Lipkin
