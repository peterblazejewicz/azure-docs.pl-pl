---
title: Create an internal load balancer by using the Azure CLI in Resource Manager | Microsoft Docs
description: Learn how to create an internal load balancer by using the Azure CLI in Resource Manager
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: sewhee

---
# Create an internal load balancer by using the Azure CLI
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[classic deployment model](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Deploy the solution by using the Azure CLI
The following steps show how to create an Internet-facing load balancer by using Azure Resource Manager with CLI. With Azure Resource Manager, each resource is created and configured individually, and then put together to create a resource.

You need to create and configure the following objects to deploy a load balancer:

* **Front-end IP configuration**: contains public IP addresses for incoming network traffic
* **Back-end address pool**: contains network interfaces (NICs) that enable the virtual machines to receive network traffic from the load balancer
* **Load-balancing rules**: contains rules that map a public port on the load balancer to port in the back-end address pool
* **Inbound NAT rules**: contains rules that map a public port on the load balancer to a port for a specific virtual machine in the back-end address pool
* **Probes**: contains health probes that are used to check the availability of virtual machines instances in the back-end address pool

For more information, see [Azure Resource Manager support for Load Balancer](load-balancer-arm.md).

## Set up CLI to use Resource Manager
1. If you have never used Azure CLI, see [Install and configure the Azure CLI](../xplat-cli-install.md). Follow the instructions up to the point where you select your Azure account and subscription.
2. Run the **azure config mode** command to switch to Resource Manager mode, as follows:
   
        azure config mode arm
   
    Expected output:
   
        info:    New mode is arm

## Create an internal load balancer step by step
1. Sign in to Azure.
   
        azure login
   
    When prompted, enter your Azure credentials.
2. Change the command tools to Azure Resource Manager mode.
   
        azure config mode arm

## Create a resource group
All resources in Azure Resource Manager are associated with a resource group. If you haven't done so yet, create a resource group.

    azure group create <resource group name> <location>

## Create an internal load balancer set
1. Create an internal load balancer
   
    In the following scenario, a resource group named nrprg is created in East US region.
   
        azure network lb create -n nrprg -l eastus
   
   > [!NOTE]
   > All resources for an internal load balancers, such as virtual networks and virtual network subnets, must be in the same resource group and in the same region.
   > 
   > 
2. Create a front-end IP address for the internal load balancer.
   
    The IP address that you use must be within the subnet range of your virtual network.
   
        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet
   
    Parameters used:
   
   * **-g**: resource group
   * **-l**: name of the internal load balancer set
   * **-n**: name of the front end IP
   * **-a**: private IP address within the subnet range
   * **-e**: subnet name
   * **-m**: virtual network name
3. Create the back-end address pool.
   
        azure network lb address-pool create -g nrprg -l ilbset -n beilb
   
    Parameters used:
   
   * **-g**: resource group
   * **-l**: name of the internal load balancer set
   * **-n**: name of the back end address pool
     
     After you define a front-end IP address and a back-end address pool, you can create load balancer rules, inbound NAT rules, and customized health probes.
4. Create a load balancer rule for the internal load balancer.
   
    When you follow the previous steps, the command creates a load-balancer rule for listening to port 1433 in the front-end pool and sending load-balanced network traffic to the back-end address pool, also using port 1433.
   
        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb
   
    Parameters used:
   
   * **-g**: resource group
   * **-l**: name of the internal load balancer set
   * **-n**: name of the load balancer rule
   * **-p**: protocol that is used for the rule
   * **-f**: port that listens to incoming network traffic in the load balancer front end
   * **-b**: port that receives the network traffic in the back-end address pool
5. Create inbound NAT rules.
   
    Inbound NAT rules are used to create endpoints in a load balancer that go to a specific virtual machine instance. The previous steps created two NAT rules  for remote desktop.
   
        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389
   
        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389
   
    Parameters used:
   
   * **-g**: resource group
   * **-l**: name of the internal load balancer set
   * **-n**: name of the inbound NAT rule
   * **-p**: protocol that is used for the rule
   * **-f**: port that listens to incoming network traffic in the load balancer front end
   * **-b**: port that receives the network traffic in the back-end address pool
6. Create health probes for the load balancer.
   
    A health probe checks all virtual machine instances to make sure they can send network traffic. The virtual machine instance with failed probe checks is removed from the load balancer until it goes back online and a probe check determines that it's healthy.
   
        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4
   
    Parameters used:
   
   * **-g**: resource group
   * **-l**: name of the internal load-balancer set
   * **-n**: name of the health probe
   * **-p**: protocol used by health probe
   * **-i**: probe interval in seconds
   * **-c**: number of checks

    >[AZURE.NOTE] The Microsoft Azure platform uses a static, publicly routable IPv4 address for a variety of administrative scenarios. The IP address is 168.63.129.16. This IP address should not be blocked by any firewalls, because this can cause unexpected behavior.
    >With respect to Azure internal load balancing, this IP address is used by monitoring probes from the load balancer to determine the health state for virtual machines in a load-balanced set. If a network security group is used to restrict traffic to Azure virtual machines in an internally load-balanced set or is applied to a virtual network subnet, ensure that a network security rule is added to allow traffic from 168.63.129.16.

## Create NICs
You need to create NICs (or modify existing ones) and associate them to NAT rules, load balancer rules, and probes.

1. Create an NIC named *lb-nic1-be*, and then associate it with the *rdp1* NAT rule and the *beilb* back-end address pool.
   
        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
   
    Parameters:
   
   * **-g**: resource group name
   * **-n**: name for the NIC resource
   * **--subnet-name**: name of the subnet
   * **--subnet-vnet-name**: name of the virtual network
   * **-d**: ID of the back-end pool resource, which starts with /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool>
   * **-e**: ID of the NAT rule to be associated to the NIC resource--starts with /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>

Expected output:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

1. Create an NIC named *lb-nic2-be*, and then associate it with the *rdp2* NAT rule and the *beilb* back-end address pool.
   
        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
2. Create a virtual machine named *DB1*, and then associate it with the NIC named *lb-nic1-be*. A storage account called *web1nrp* is created before the following command runs:
   
        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
   
   > [!IMPORTANT]
   > VMs in a load balancer need to be in the same availability set. Use `azure availset create` to create an availability set.
   > 
   > 
3. Create a virtual machine (VM) named *DB2*, and then associate it with the NIC named *lb-nic2-be*. A storage account called *web1nrp* was created before running the following command.
   
        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Delete a load balancer
To remove a load balancer, use the following command:

    azure network lb delete -g nrprg -n ilbset

In this example, **nrprg** is the resource group and **ilbset** is the internal load balancer name.

## Next steps
[Configure a load balancer distribution mode by using source IP affinity](load-balancer-distribution-mode.md)

[Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md)

