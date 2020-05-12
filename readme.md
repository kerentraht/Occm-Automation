# OCCM Ansible automation usage
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
## Step 3:Create Volume with Iscsi 
### Required variables:
+ occmIp: The IP-address of the Cloud Manager
+ refToken: The refresh token string (from step1)
+ weType: Working environment type - single node or HA [aws|awsha]
+ weName: String with the name of the working environment
+ volName: The name of the volume to create
+ providerType: The disk type for the volume [gp2|st1|sc1]
+ volSize: The size of the volume in GB 
+ os_name: [linux|windows|vmware]
+ iqn: The IQN of the initiator host
+ igName: The name of igroup that will be created
### Instuctions
Run the "createIscsiVolume.yml" playbook from the path in which the file is located with the extre-vars that are listed in the 'required variables' section (do not keep the square brackets in the final command).
### Usage
>NOTE: The followoing command will create a vol
```
ansible-playbook createIscsiVolume.yml --extra-vars "occmIp=[Cloud manager's IP] refToken=[refresh token string] weType=[aws|awsha] weName=[working-environment name] volName=[desired volume name] providerType=[gp2|st1|sc1] volSize=[desired size in GB] os_name=[linux|windows|vmware] iqn=[iqn of the initiator] igName=[desired igroup name]"
```
Wait for the playbook to complete all tasks successfully!

## Step 4 - Attach the iscsi device to the host

+ Guide reference for linux host can be found here:
https://www.synology.com/en-us/knowledgebase/DSM/tutorial/Virtualization/How_to_set_up_and_use_iSCSI_target_on_Linux

+ Guide reference for windows host can be found here:
https://www.virtualizationhowto.com/2017/07/add-iscsi-shared-storage-in-windows-server-2016/


![alt text](https://github.com/kerentraht/Occm-Automation/blob/master/png-transparent-copyright-symbol-copyright-law-of-the-united-states-computer-icons-copyright-text-trademark-words-phrases.png "copyrights") 
Keren Trajtenberg
