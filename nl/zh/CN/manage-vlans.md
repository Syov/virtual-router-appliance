---

copyright:
  years: 2017
lastupdated: "2018-11-10"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}

# 管理 VLAN
{: #managing-your-vlans}

您可以在[网关设备详细信息屏幕](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-view-vra-details)中执行各种操作。

## 将 VLAN 与网关设备相关联

VLAN 需要与网关设备关联，然后才能对其进行路由。VLAN 关联是有资格的 VLAN 与网关的链接，关联后未来就可以将其路由到网关设备。关联过程不会自动将 VLAN 路由到网关设备；在将 VLAN 路由到网关之前，VLAN 将继续使用前端和后端客户路由器。 

一次只能将 VLAN 与一个网关相关联，并且 VLAN 不能有防火墙。执行以下过程来将 VLAN 与网关相关联。

1. 在客户门户网站中[访问网关设备详细信息屏幕](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-view-vra-details)。 
2. 从**关联 VLAN** 下拉列表中，选择所需的 VLAN。
3. 单击**关联**按钮以关联 VLAN。

将 VLAN 关联到网关设备后，该 VLAN 就会显示在“网关设备详细信息”屏幕的“关联的 VLAN”部分中。在此部分中，可以将 VLAN 路由到网关，也可以解除它与网关的关联。通过重复上述步骤，可以随时将其他有资格的 VLAN 与网关设备相关联。

## 路由关联的 VLAN

关联的 VLAN 已链接到网关设备，但在路由该 VLAN 之前，进出该 VLAN 的流量不会命中相应的网关。路由关联的 VLAN 后，所有前端和后端流量都会通过网关设备（而不是客户路由器）进行路由。 

执行以下过程来路由关联的 VLAN：

1. 在客户门户网站中[访问网关设备详细信息屏幕](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-view-vra-details)。 
2. 在“关联的 VLAN”部分中找到所需的 VLAN。
3. 从“操作”下拉菜单中，选择**路由 VLAN**。
4. 单击**是**以路由 VLAN。 

路由 VLAN 后，所有前端和后端流量都会从客户路由器移至网关。通过访问网关的管理工具，可取得与流量和网关设备本身相关的其他控制权。可随时通过[绕过网关设备](#bypass-gateway-appliance-routing-for-a-vlan)来中断通过网关进行的路由。

## 绕过 VLAN 的网关设备路由

路由 VLAN 后，所有前端流量和后端流量都通过网关传递。可随时绕过网关设备，使流量恢复为通过前端和后端客户路由器（FCR 和 BCR）传递。 

绕过 VLAN 将允许 VLAN 保持与网关的关联。如果 VLAN 不应该再与网关设备相关联，请参阅[解除 VLAN 与网关设备的关联](#disassociate-a-vlan-from-a-gateway-appliance)。 

执行以下过程来绕过 VLAN 的网关路由：

1. 在客户门户网站中[访问网关设备详细信息屏幕](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-view-vra-details)。 
2. 在“关联的 VLAN”部分中找到所需的 VLAN。
3. 从“操作”下拉菜单中，选择**绕过 VLAN**。
4. 单击**是**以绕过网关。 

在绕过网关后，所有前端和后端流量都将通过与 VLAN 关联的 FCR 和 BCR 进行路由。VLAN 将保持与网关设备相关联，并且可以随时恢复路由到网关设备。

## 解除 VLAN 与网关设备的关联

通过[关联](#associate-a-vlan-to-a-gateway-appliance)，一次可以将 VLAN 链接到一个网关设备。关联允许随时将 VLAN 路由到网关设备。如果 VLAN 应该与其他网关设备相关联，或者如果 VLAN 不应该再与其网关相关联，那么需要解除关联。解除关联会除去 VLAN 与网关设备的“链接”，从而允许根据需要将其与其他网关相关联。 

执行以下过程来解除 VLAN 与网关设备的关联：

1. 在客户门户网站中[访问网关设备详细信息屏幕](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-view-vra-details)。 
2. 在“关联的 VLAN”部分中找到所需的 VLAN。
3. 从**操作**下拉菜单中，选择**解除关联**。 
4. 单击**是**以解除 VLAN 的关联。 

解除 VLAN 与网关设备的关联后，该 VLAN 即可与其他网关相关联。该 VLAN 还可以随时恢复与网关设备相关联。解除 VLAN 与网关设备的关联后，该 VLAN 的流量即无法通过该网关传递。VLAN 必须与网关设备关联，然后才能对其进行路由。

## 通过同一网络接口路由多个 VLAN
虚拟路由器设备能够通过同一网络接口（例如，`dp0bond0` 或 `dp0bond1`）路由多个 VLAN。实现方法是将交换机端口设置为中继方式，并在设备上配置虚拟接口 (VIF)。

例如： 

```
set interfaces bonding dp0bond0 vif 1432 address 10.0.10.1/24
set interfaces bonding dp0bond0 vif 1693 address 10.0.20.1/24
```

以上命令在 `dp0bond0` 接口上创建两个虚拟接口。接口 `dp0bond0.1432` 用于处理 VLAN 1432 的流量，而接口 `dp0bond0.1693` 用于处理 VLAN 1693 的流量。

## 向单个 VLAN 添加多个子网

以下是一个示例配置，在最后添加了公用 VLAN (1451) 的样本子网 (`159.8.67.96/28`)。每个 VIF（VLAN 接口）的地址不会在 BCR（后端客户路由器）或 FCR（前端客户路由器）中路由。它仅用于两个 Vyatta 之间的 VRRP/高可用性通信。 

可以从任意未使用的专用 IP 空间中选择子网。因此，此处通常排除 `10.0.0.0/8`。以下示例中选择了 `192.168.0.0/16` 中的子网，但也可使用 `172.16.0.0/12` 中的子网。 

`virtual-address` 是应该配置新子网的位置。在大多数情况下，子网的网关 IP 地址是应该配置的内容。绑定到 VIF 的网关 IP 将会用作在 VRA 后的新子网中设置的任意裸机或虚拟服务器的下一个网关地址。 

以下示例显示将绑定到该 VIF 的 `159.8.67.97/28`，这样 `159.8.67.98/28` 子网的所有流量都可以由 VRA 进行管理。

```
set interfaces bonding dp0bond0 vif 1623 address '192.168.10.2/30'
set interfaces bonding dp0bond0 vif 1623 vrrp vrrp-group 2 sync-group 'vgroup2'
set interfaces bonding dp0bond0 vif 1623 vrrp vrrp-group 2 virtual-address '10.127.132.129/26'
set interfaces bonding dp0bond0 vif 1750 address '192.168.20.2/30'
set interfaces bonding dp0bond0 vif 1750 vrrp vrrp-group 2 sync-group 'vgroup2'
set interfaces bonding dp0bond0 vif 1750 vrrp vrrp-group 2 virtual-address '10.126.19.129/26'
set interfaces bonding dp0bond1 vif 788 address '192.168.150.2/30'
set interfaces bonding dp0bond1 vif 788 vrrp vrrp-group 2 sync-group 'vgroup2'
set interfaces bonding dp0bond1 vif 788 vrrp vrrp-group 2 virtual-address '159.8.106.129/28'
set interfaces bonding dp0bond1 vif 1451 address '192.168.200.2/30'
set interfaces bonding dp0bond1 vif 1451 vrrp vrrp-group 2 sync-group 'vgroup2'
set interfaces bonding dp0bond1 vif 1451 vrrp vrrp-group 2 virtual-address '159.8.67.97/28'
set interfaces bonding dp0bond1 vif 1451 vrrp vrrp-group 2 virtual-address '159.8.86.49/29'
```
