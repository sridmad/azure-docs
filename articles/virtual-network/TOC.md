# Overview
## [Virtual networks](virtual-networks-overview.md)
## [Routing](virtual-networks-udr-overview.md)
## [Virtual network peering](virtual-network-peering-overview.md)
## [Virtual network service endpoints](virtual-network-service-endpoints-overview.md)
## [Virtual network for Azure services](virtual-network-for-azure-services.md)
## [Security](security-overview.md)
## [Business continuity](virtual-network-disaster-recovery-guidance.md)
## [IP addressing](virtual-network-ip-addresses-overview-arm.md)
## [DDoS Protection](ddos-protection-overview.md)
## [FAQ](virtual-networks-faq.md)
## Classic
### [IP addressing](virtual-network-ip-addresses-overview-classic.md)
### [Access control lists](virtual-networks-acl.md)

# Get Started
## [Create your first virtual network](virtual-network-get-started-vnet-subnet.md)

# How To
## Plan and design
### [Virtual networks](virtual-network-vnet-plan-design-arm.md)
### [Network security groups](virtual-networks-nsg.md)

## Deploy
### [Virtual networks](virtual-networks-create-vnet-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [Azure CLI](virtual-networks-create-vnet-arm-cli.md)
#### [Template](virtual-networks-create-vnet-arm-template-click.md)

### Network security groups
#### [Azure portal](virtual-networks-create-nsg-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [Azure CLI](virtual-networks-create-nsg-arm-cli.md)
#### [Template](virtual-networks-create-nsg-arm-template.md)
#### [Application security groups](create-network-security-group-preview.md)
#### Classic
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### User-defined routes
#### [Azure portal](create-user-defined-route-portal.md)
#### [Azure PowerShell](virtual-network-create-udr-arm-ps.md)
#### [Azure CLI](virtual-network-create-udr-arm-cli.md)
#### [Template](virtual-network-create-udr-arm-template.md)
#### Classic
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Azure CLI](virtual-network-create-udr-classic-cli.md)

### Virtual network peering
#### [Same deployment model - same subscription](virtual-network-create-peering.md)
#### [Same deployment model - different subscriptions](create-peering-different-subscriptions.md)
#### [Different deployment models - same subscription](create-peering-different-deployment-models.md)
#### [Different deployment models - different subscriptions](create-peering-different-deployment-models-subscriptions.md)

### [Virtual network service endpoints](virtual-network-service-endpoints-configure.md)

### Public IP address - availability zone
#### [Azure portal](create-public-ip-availability-zone-portal.md)
#### [Azure CLI](create-public-ip-availability-zone-cli.md)
#### [PowerShell](create-public-ip-availability-zone-powershell.md)

### Virtual machines
#### [Virtual machine network throughput](virtual-machine-network-throughput.md)
#### Create a VM with a static public IP address
##### [Azure portal](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [Template](virtual-network-deploy-static-pip-arm-template.md)
##### Classic
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Create a VM with a static private IP address
##### [Azure portal](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
##### Classic
###### [Azure portal](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Azure CLI](virtual-networks-static-private-ip-classic-cli.md)

#### Create a VM with multiple network interfaces
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Template](virtual-network-deploy-multinic-arm-template.md)

##### Classic
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Azure CLI](virtual-network-deploy-multinic-classic-cli.md)

#### Create a VM with multiple IP addresses
##### [Azure portal](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md)
##### [Azure CLI 1.0](virtual-network-multiple-ip-addresses-cli-nodejs.md)
##### [Template](virtual-network-multiple-ip-addresses-template.md)

#### [Create a VM with accelerated networking](virtual-network-create-vm-accelerated-networking.md)

### Connectivity scenarios
#### [Virtual network (VNet) to VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) to a VNet (Classic)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet to on-premises network (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet to on-premises network (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Highly available hybrid network architecture](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Security scenarios
#### [Secure networks with virtual appliances](virtual-network-scenario-udr-gw-nva.md)
#### [DMZ between Azure and the Internet](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Cloud service and network security](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Create a DMZ with NSGs](virtual-networks-dmz-nsg.md)
##### [Create a DMZ with NSGs (Classic)](virtual-networks-dmz-nsg-asm.md)
##### [Create a DMZ with firewall and NSGs (Classic)](virtual-networks-dmz-nsg-fw-asm.md)
##### [DMZ with firewall, UDR, and NSGs (Classic)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Sample application](virtual-networks-sample-app.md)

### Classic
#### [Virtual network](create-virtual-network-classic.md)
##### [Azure portal](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Azure CLI](virtual-networks-create-vnet-classic-cli.md)
#### [Specify DNS settings in a virtual network configuration file](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [Specify DNS settings in a service configuration file](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Configure
### Virtual machines
#### [Add or remove network interfaces](virtual-network-network-interface-vm.md)
#### [Name resolution for VMs and cloud services](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Use dynamic DNS to register hostnames in your own DNS server](virtual-networks-name-resolution-ddns.md)
#### [Optimize network throughput](virtual-network-optimize-network-bandwidth.md)
#### [View and modify hostnames](virtual-networks-viewing-and-modifying-hostnames.md)
#### Classic
##### Static IP addresses
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [CLI](virtual-networks-static-private-ip-classic-cli.md)
##### [Instance level public IP address](virtual-networks-instance-level-public-ip.md)

### Classic
#### Access control lists
##### [Azure portal](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## Manage
### [Virtual networks](virtual-network-manage-network.md)
#### [Subnets](virtual-network-manage-subnet.md)
#### [Peerings](virtual-network-manage-peering.md)
#### Classic
##### [Network configuration file](virtual-networks-using-network-configuration-file.md)
##### [Migrate from an affinity group to a region](virtual-networks-migrate-to-regional-vnet.md)
### Network security groups
#### [Azure portal](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Azure CLI](virtual-network-manage-nsg-arm-cli.md)

#### [Logs](virtual-network-nsg-manage-log.md)
### Network interfaces (NICs)
#### [Create, change, or delete NICs](virtual-network-network-interface.md)
#### [Add, change, or remove IP addresses](virtual-network-network-interface-addresses.md)
### Virtual machines
#### [Move a VM to a different subnet](virtual-networks-move-vm-role-to-subnet.md)
### [Public IP addresses](virtual-network-public-ip-address.md)
### DDoS Protection
#### [Azure portal](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Troubleshoot
### Network security groups
#### [Azure portal](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Routes
#### [Azure portal](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Throughput testing](virtual-network-bandwidth-testing.md)
### [Cannot delete virtual networks](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [VM to VM connectivity problems](virtual-network-troubleshoot-connectivity-problem-between-vms.md)

# Reference
## [Code samples](https://azure.microsoft.com/en-us/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (Classic)](/powershell/module/azure/)
## [Azure CLI](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (Classic)](https://msdn.microsoft.com/library/jj157182.aspx)


# Related
## [Virtual Machines](/azure/virtual-machines/)
## [Application Gateway](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Traffic Manager](/azure/traffic-manager/)
## [Load Balancer](/azure/load-balancer/)
## [VPN Gateway](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Resources
## [Azure roadmap](https://azure.microsoft.com/roadmap/?category=networking)
## [Networking blog](http://azure.microsoft.com/blog/topics/networking)
## [Networking forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Pricing](https://azure.microsoft.com/pricing/details/virtual-network)
## [Pricing calculator](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Network resource provider](resource-groups-networking.md)
