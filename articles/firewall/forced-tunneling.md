---
title: Azure Firewall forced tunneling
description: You can configure forced tunneling to route Internet-bound traffic to another firewall or network virtual appliance for further processing.
services: firewall
author: vhorne
ms.service: azure-firewall
ms.topic: article
ms.date: 03/22/2024
ms.author: victorh
---

# Azure Firewall forced tunneling

When you configure a new Azure Firewall, you can route all Internet-bound traffic to a designated next hop instead of going directly to the Internet. For example, you might have a default route advertised via BGP or using User Defined Route (UDR) to force traffic to an on-premises edge firewall or other network virtual appliance (NVA) to process network traffic before it's passed to the Internet. To support this configuration, you must create Azure Firewall with forced tunneling configuration enabled. This is a mandatory requirement to avoid service disruption. 

If you have a pre-existing firewall, you must stop/start the firewall in forced tunneling mode to support this configuration. Stopping/starting the firewall can be used to configure forced tunneling the firewall without the need to redeploy a new one. You should do this during maintenance hours to avoid disruptions. For more information, see the [Azure Firewall FAQ](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) about stopping and restarting a firewall in forced tunnelling mode.

You might prefer not to expose a public IP address directly to the Internet. In this case, you can deploy Azure Firewall in forced tunneling mode without a public IP address. This configuration creates a management interface with a public IP address that is used by Azure Firewall for its operations. The public IP address is used exclusively by the Azure platform and can't be used for any other purpose. The tenant data path network can be configured without a public IP address, and Internet traffic can be forced tunneled to another firewall or blocked.

Azure Firewall provides automatic SNAT for all outbound traffic to public IP addresses. Azure Firewall doesn’t SNAT when the destination IP address is a private IP address range per IANA RFC 1918. This logic works perfectly when you egress directly to the Internet. However, with forced tunneling enabled, Internet-bound traffic is SNATed to one of the firewall private IP addresses in the AzureFirewallSubnet. This hides the source address from your on-premises firewall. You can configure Azure Firewall to not SNAT regardless of the destination IP address by adding *0.0.0.0/0* as your private IP address range. With this configuration, Azure Firewall can never egress directly to the Internet. For more information, see [Azure Firewall SNAT private IP address ranges](snat-private-range.md).

> [!IMPORTANT]
> If you deploy Azure Firewall inside of a Virtual WAN Hub (Secured Virtual Hub), advertising the default route over Express Route or VPN Gateway is not currently supported. A fix is being investigated.

> [!IMPORTANT]
> DNAT isn't supported with forced tunneling enabled. Firewalls deployed with forced tunneling enabled can't support inbound access from the Internet because of asymmetric routing.

## Forced tunneling configuration

You can configure forced tunneling during Firewall creation by enabling forced tunneling mode as shown in the following screenshot. To support forced tunneling, Service Management traffic is separated from customer traffic. Another dedicated subnet named **AzureFirewallManagementSubnet** (minimum subnet size /26) is required with its own associated public IP address. This public IP address is for management traffic. It's used exclusively by the Azure platform and can't be used for any other purpose.

In forced tunneling mode, the Azure Firewall service incorporates the Management subnet (AzureFirewallManagementSubnet) for its *operational* purposes. By default, the service associates a system-provided route table to the Management subnet. The only route allowed on this subnet is a default route to the Internet and *Propagate gateway* routes must be disabled. Avoid associating customer route tables to the Management subnet when you create the firewall. 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="Configure forced tunneling":::

Within this configuration, the *AzureFirewallSubnet* can now include routes to any on-premises firewall or NVA to process traffic before it's passed to the Internet. You can also publish these routes via BGP to *AzureFirewallSubnet* if **Propagate gateway routes** is enabled on this subnet.

For example, you can create a default route on the *AzureFirewallSubnet* with your VPN gateway as the next hop to get to your on-premises device. Or you can enable **Propagate gateway routes** to get the appropriate routes to the on-premises network.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Virtual network gateway route propagation":::

If you enable forced tunneling, Internet-bound traffic is SNATed to one of the firewall private IP addresses in AzureFirewallSubnet, hiding the source from your on-premises firewall.

If your organization uses a public IP address range for private networks, Azure Firewall SNATs the traffic to one of the firewall private IP addresses in AzureFirewallSubnet. However, you can configure Azure Firewall to **not** SNAT your public IP address range. For more information, see [Azure Firewall SNAT private IP address ranges](snat-private-range.md).

Once you configure Azure Firewall to support forced tunneling, you can't undo the configuration. If you remove all other IP configurations on your firewall, the management IP configuration is removed as well, and the firewall is deallocated. The public IP address assigned to the management IP configuration can't be removed, but you can assign a different public IP address.

## Next steps

- [Tutorial: Deploy and configure Azure Firewall in a hybrid network using the Azure portal](tutorial-hybrid-portal.md)
