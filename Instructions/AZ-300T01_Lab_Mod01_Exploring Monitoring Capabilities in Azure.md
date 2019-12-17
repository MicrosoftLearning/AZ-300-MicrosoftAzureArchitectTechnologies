# Managing Azure Subscriptions and Resources

# Lab: Exploring Monitoring Capabilities in Azure

### Scenario

Adatum Corporation wants to explore monitoring capabilities in Azure

### Objectives

After completing this lab, you will be able to:

- Deploy Azure VM scale sets

- Implement monitoring and alerting by using Azure Monitor

### Lab Setup

Estimated Time: 45 minutes

User Name: **Student**

Password: **Pa55w.rd**

# Exercise 1: Deploy Azure VM scale sets

The main tasks for this exercise are as follows:

1. Deploy an Azure VM scale set by using an Azure QuickStart template

1. Review autoscaling settings of the Azure VM scale set

#### Task 1: Deploy an Azure VM scale set by using an Azure QuickStart template

1. From the lab virtual machine, start Microsoft Edge and browse to the Azure portal at http://portal.azure.com and sign in by using the Microsoft account that has the Owner role in the target Azure subscription.

1. In the Azure portal, in the Microsoft Edge window, start a **PowerShell** session within the Cloud Shell.

1. If you are presented with the **You have no storage mounted** message, click **Show Advanced Settings** and then configure storage using the following settings:

   - Subsciption: the name of the target Azure subscription

   - Cloud Shell region: the name of the Azure region that is available in your subscription and which is closest to the lab location

   - Resource group: the name of a new resource group **az3000100-LabRG**

   - Storage account: a name of a new storage account (between 3 and 24 characters consisting of lower case letters and digits)

   - File share: a name of a new file share: **cloudshell**

1. From the Cloud Shell pane, run the following command to identify a unique DNS domain name (substitute the placeholder `<custom-label>` with any alphanumeric string starting with a letter and no longer than 9 characters, which is likely to be unique and the placeholder `<location>` with the name of the Azure region into which you intend to deploy resources in this lab):

   ```pwsh
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
   ```

1. Verify that the command returned **True**. If not, rerun the same command with a different value of the `<custom-label>` until the command returns **True**.

1. Note the value of the `<custom-label>` that resulted in the successful outcome. You will need it in the next task.

1. From the lab virtual machine, start Microsoft Edge and browse to the Azure QuickStart template that deploys autoscale demo app on Ubuntu 16.04 at [**https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale**](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale).

1. Click **Deploy to Azure** and, when prompted, sign in by using the Microsoft account that has the Owner role in the target Azure subscription.

1. In the Azure Portal, on the **Deploy VM Scale Set with Python Bottle server & AutoScale** blade, specify the following settings and initiate the deployment:

   - Subscription: the name of the target Azure subscription

   - Resource group: the name of a new resource group **az3000101-LabRG**

   - Location: the name of the Azure region that you referenced when running `Test-AzDnsAvailability` earlier in this task

   - Vm Sku: **Standard_D2_v2**

   - Vmss Name: the custom label you identified when running `Test-AzDnsAvailability` earlier in this task

   - Instance count: **1**

   - Admin Username: **student**

   - Admin Password: **Pa55w.rd1234**

1. Place a checkmark next to **I agree to the terms and conditions stated above**, and then click **Purchase**.

1. Wait for the deployment to complete. This will take about 5 minutes.

#### Task 2: Review autoscaling settings of the Azure VM scale set

1. In Azure Portal, navigate to the blade representing the newly deployed **Azure VM scale set**.

1. From the VM scale set blade, navigate to the its **Scaling** blade.

1. Note that the Azure VM scale set is configured to scale dynamically based on a metric using the following criteria:

   - Scale out: increase instance count by 1 when average percentage of CPU > 60

   - Scale in: decrease instance count by 1 when average percentage of CPU < 30

   - Minimum number of instances: 1

   - Maximum number of instances: 10

1. Modify the maximum number of instances to 3 and **save** your changes.

> **Result**: After you completed this exercise, you have deployed an Azure VM scale set and reviewed its autoscaling settings.

## Exercise 2: Implementing monitoring and alerting by using Azure Monitor

The main tasks for this exercise are as follows:

1. Create Azure VM scale set metrics-based alerts

1. Configure Azure VM scale set autoscaling-based notifications

1. Test Azure VM scale set monitoring and alerting.

#### Task 1: Create Azure VM scale set metrics-based alerts

1. In the Azure portal, navigate to the blade representing the newly deployed Azure VM scale set and, from there, switch to the **Monitoring - Metrics** blade.

1. On the **Monitoring - Metrics** blade, use the filter to display **Percentage CPU** metric with an aggregation value of **Avg** of the VM scale set resource you provisioned in the previous exercise of this lab.

1. Review the resulting chart and note the average percentage CPU within the last few minutes.

1. Navigate to the **Alerts** blade in the **Monitoring** section.

1. From the **Alerts** blade, navigate to the **Manage actions** blade.

1. In the **Manage actions** section, click **Add action group**, set the action group name to **az30001 action group**, set short name: **az30001**, select the Azure subscription you used in the previous exercise, accept the default name of the resource group of **Default-ActivityLogAlerts (to be created)**, set the action name: **az30001-email**, and set the action type to **Email/SMS/Push/Voice**. 

1. On the **Email/SMS/Push/Voice** blade, set an email address, a mobile phone number, or a phone number that you want to use to receive alerts generated by this rule.

1. On the Add action group page, click **OK**.

1. Navigate to the **Alerts** blade.

1. From the **Alerts** blade, navigate to the **New alert rule** blade.
1. In the **Resource** section, select the VM scale set you provisioned in the previous exercise of this lab.

1. In the **Condition** section, click **Add condition**, select the **Percentage CPU** metric, leave the dimension settings and condition type with their default values, set the condition to **Greater than**, set the time aggregation to **Average**, set the threshold to **60**, set the Aggregation granularity (period) to **1 minute**, set the frequency to **Every 1 minute** and click **done**.

1. In the **Actions** section, click **Select action group**, select previously created action group **az30001 action group** and click **done**.

1. In the **Alert Details** section, set the alert rule name to **Percentage CPU of the VM scale set is greater than 60 percent**, its description to **Percentage CPU of the VM scale set is greater than 60 percent**, its severity to **Sev 3**, and set enable rule upon creation to **Yes**.

1. Click **Create alert rule**.

   > **Note**: It can take up to 10 minutes for a metric alert rule to become active. 

#### Task 2: Configure Azure VM scale set autoscaling-based notifications

1. In the Azure portal, navigate to the blade representing the newly deployed Azure VM scale set and, from there, switch to the **Scaling** blade.

1. On the **Scaling** blade, click the **Notify** tab heading, configure the following settings, and **save** your changes:

   - Email administrators: enabled

   - Email co-administrators: disabled

   - Additional administrator emails(s): add an email address that you want to use to receive notifications about autoscaling events

#### Task 3: Test Azure VM scale set monitoring and alerting.

1. In the Azure portal, search for **Load balancers** and navigate to the Load Balancers blade. A load balancer should exist, representing the one created with the scale set you deployed from a template in a previous exercise of this lab.

1. Identify the value of the **Public IP address** assigned to the front end of the load balancer associated with the VM scale set.

1. From the lab computer, start Microsoft Edge and browse to **http://*Public IP address\*:9000** (where **_Public IP address_** is the IP address you identified in the previous step)

1. On the **Worker interface** page, click the **Start work** link.

1. Use the **CPU (average)** chart on the VM scale set Overview blade to monitor changes to the CPU utilization.

   > **Note**: Alternatively, you can navigate back to the **Metrics** blade and use the filter to display **Percentage CPU** metric of the VM scale set resource, and set the time to **Last 30 minutes**.

   > **Note**: You should receive an alert regarding increased CPU utilization within a couple of minutes

1. Switch to the **Instances** blade of the VM scale set in order to identify the number of its instances.

   > **Note**: Alternatively, you can navigate back to the **Scaling** blade, in the list of resources capable of autoscaling, click the name of the VM scale set, on the **Autoscale settings** blade, click **Run history**, and then review the list of autoscale events.

   > **Note**: Autoscaling should be triggered within a couple of minutes.

1. Switch to the Microsoft Edge window displaying worker instances page and click the **Stop work** link.

1. Monitor decrease in CPU utilization and scaling in events using the same methods that you used when scaling out the VM scale set.

> **Result**: After you completed this exercise, you have implemented and tested monitoring and alerting by using Azure Monitor.

## Exercise 3: Remove lab resources

### Task 1: Delete resource group

1. At the top of the portal, click the **Cloud Shell** icon to open the Cloud Shell pannel and swicth to the Bash shell if necessary.

1. At the **Cloud Shell** command prompt, type in the following command and press **Enter** to list all resource groups you created in this lab:

   ```
   az group list --query "[?starts_with(name,'az300010')].[name]" --output tsv
   ```

1. Verify that the output contains only the resource groups you created in this lab. These groups will be deleted in the next task.

#### Task 2: Delete resource groups

1. At the **Cloud Shell** command prompt, type in the following command and press **Enter** to delete the resource groups you created in this lab

   ```
   az group list --query "[?starts_with(name,'az300010')].[name]" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. Close the **Cloud Shell** prompt at the bottom of the portal.

> **Result**: After you completed this exercise, you removed the resources used in this lab.
