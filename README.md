# AKS-HCIVM
<H1>Overview</H1>
This document outlines how to setup a VM in Azure before setting up AKS-HCI:<BR>
<a href="https://github.com/Azure/aks-hci/blob/main/eval/steps/1_AKSHCI_Azure.md#deploying-the-azure-vm">https://github.com/Azure/aks-hci/blob/main/eval/steps/1_AKSHCI_Azure.md#deploying-the-azure-vm</a><BR>
<P><P>
This will not work on an Azure Stack Hub VM because of differences in API versions, VM size availability, and Azure Service availability. <P>
<P>
The following instructions are for setting up and AKS-HCI cluster on a VM on Azure Stack Hub.<BR>
<P><P>
You can download the templates here: <BR>
<a href="https://github.com/darmour/AKS-HCIVM/tree/main/json">https://github.com/darmour/AKS-HCIVM/tree/main/json</a><BR>
<P><P>
The orginal template for deploying the VM in Azure uses the Azure DevTest Labs service to restart the VM.  Since this service is not available in Azure Stack Hub, the template was split into three templates and you will manually reboot the VM between running each template. Additionally, the DSC script uses Azure Root Hints for DNS which will cause the script to fail in Azure Stack Hub its final steps.  Rather than forking the repo, I will provide instructions to manually add a DNS forwarder allowing the DSC script to complete.

<H1>Deploying the VM</H1>
The easiest way to deploy the templates is to use Template Deployment in the Marketplace.
 
The first template to deploy is akshcihost.json. The parameters file, params.json, is also provided to simplify the deployment.  If you are deploying two of these VMs into the same subscription, make sure you change the networkSuffix parameter.

After you deploy the VM using the akshcihost.json template, Remote Desktop into the VM and apply all Windows Updates. <BR>
<P><P>
<b>Important:</b> While in the VM, open the command window and run Ipconfig /all.  Write down the DNS server IP address.  You will need this information later. <BR><P><P>

Reboot the VM.  Check for additional updates. <BR>
Reboot the VM again.

<P>
<H1>Custom Script Extension to Install Windows Admin Center</H1>
After you have patched and rebooted the VM, you need to apply the customer script extension using the ARM template, customscript.json.  You can deploy this template using Template Deployment in the Marketplace.  <P><P>Make sure you specify the VM Name and the Resource Group in the parameters.  <BR>The user name and password are also required parameters.
<P>
Once the deployment succeeds, restart the VM.
<H1>Desired State Config to setup Hyper-V & Windows Server Components</H1>
After you have rebooted the VM, you will need to apply the DSC script to setup the data disk, Hyper-V, DHCP, and DNS as well as configure the required server settings.
Use the dscextenstion.json ARM template to apply the DSC script to the VM.  <P><P>Like the custom script extension, you can use the Template Deployment in the Marketplace to deploy the ARM template.  
<P>After about 20 minutes you will get an error that a remote name, chocolatey.org, was not able to be resolved.

 
Remote desktop into the VM. <BE>
<P>
From the Start bar, under Windows Administrative Tools, open the DNS management app and select the local server. <P><P>Open Forwarders and enter the DNS IP address that you wrote down in the previous step.<BR>
Restart the VM.  <P><P>After the VM restarts the DSC script will continue and complete. 
<h1>Next Steps</H1>
From here you can continue to the instructions to setup AKS-HCI on the VM.  Those instructions will work the same for the Azure Stack Hub VM.
AKS-HCI setup using Windows Admin Center: <BR>
<a href="https://github.com/Azure/aks-hci/blob/main/eval/steps/2a_DeployAKSHCI_WAC.md">https://github.com/Azure/aks-hci/blob/main/eval/steps/2a_DeployAKSHCI_WAC.md</a>
<P>
<P>
AKS-HCI setup using PowerShell: <BR>
<a href="https://github.com/Azure/aks-hci/blob/main/eval/steps/2b_DeployAKSHCI_PS.md">https://github.com/Azure/aks-hci/blob/main/eval/steps/2b_DeployAKSHCI_PS.md</a>


