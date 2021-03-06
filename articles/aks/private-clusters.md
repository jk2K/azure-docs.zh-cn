---
title: 创建专用 Azure Kubernetes 服务群集
description: 了解如何创建专用 Azure Kubernetes 服务 (AKS) 群集
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 2749e66375fbd808a9e87f252a813f1054ceff21
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525562"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>创建专用 Azure Kubernetes 服务群集

在专用群集中，控制平面或 API 服务器具有内部 IP 地址，这些地址在 [RFC1918 - 专用 Internet 的地址分配](https://tools.ietf.org/html/rfc1918)文档中定义。 通过使用专用群集，可以确保 API 服务器与节点池之间的网络流量仅保留在专用网络上。

控制平面或 API 服务器位于 Azure Kubernetes 服务 (AKS) 托管的 Azure 订阅中。 客户的群集或节点池在客户的订阅中。 服务器与群集或节点池可以通过 API 服务器虚拟网络中的 [Azure 专用链接服务][private-link-service]以及在客户 AKS 群集的子网中公开的专用终结点相互通信。

## <a name="region-availability"></a>上市区域

专用群集在 [支持 AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)的公共区域、azure 政府和 Azure 中国世纪互联区域提供。

> [!NOTE]
> 支持 Azure 政府站点，但由于缺少专用链接支持，当前不支持 US Gov 德克萨斯州。

## <a name="prerequisites"></a>先决条件

* Azure CLI 版本 2.2.0 或更高版本
* 仅标准 Azure 负载均衡器支持专用链接服务。 不支持基本 Azure 负载均衡器。  
* 若要使用自定义 DNS 服务器，请在自定义 DNS 服务器中将 Azure DNS IP 168.63.129.16 作为上游 DNS 服务器进行添加。

## <a name="create-a-private-aks-cluster"></a>创建专用 AKS 群集

### <a name="create-a-resource-group"></a>创建资源组

为 AKS 群集创建资源组或使用现有资源组。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>默认基本网络 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
其中的 `--enable-private-cluster` 是专用群集的必需标志。 

### <a name="advanced-networking"></a>高级网络  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
其中的 `--enable-private-cluster` 是专用群集的必需标志。 

> [!NOTE]
> 如果 Docker 桥地址 CIDR (172.17.0.1/16) 与子网 CIDR 冲突，请相应地更改 Docker 桥地址。

## <a name="configure-private-dns-zone"></a>配置专用 DNS 区域

可以利用以下参数配置专用 DNS 区域。

1. "系统" 是默认值。 如果省略了--AKS 参数，则会在节点资源组中创建专用 DNS 区域。
2. "无" 表示 AKS 不会创建专用 DNS 区域。  这要求你引入自己的 DNS 服务器，并为专用 FQDN 配置 DNS 解析。  如果未配置 DNS 解析，则仅可在代理节点内解析 DNS，并在部署后导致群集问题。
3. Azure global cloud 的 "自定义专用 dns 区域名称" 格式应为： `privatelink.<region>.azmk8s.io` 。 需要专用 DNS 区域的资源 Id。  此外，你将需要一个用户至少 `private dns zone contributor` 向自定义专用 dns 区域分配了角色的标识或服务主体。

### <a name="prerequisites"></a>必备条件

* AKS 预览版0.4.71 或更高版本
* API 2020-11-01 或更高版本

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>创建具有专用 DNS 区域的专用 AKS 群集

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [none|system|custom private dns zone ResourceId]
```
## <a name="options-for-connecting-to-the-private-cluster"></a>连接到专用群集的选项

API 服务器终结点没有公共 IP 地址。 若要管理 API 服务器，需要使用有权访问 AKS 群集的 Azure 虚拟网络 (VNet) 的 VM。 有几种选项可用于建立与专用群集的网络连接。

* 在与 AKS 群集相同的 Azure 虚拟网络 (VNet) 中创建 VM。
* 在单独的网络中使用 VM 并设置[虚拟网络对等互联][virtual-network-peering]。  有关此选项的详细信息，请参阅以下部分。
* 使用[快速路由或 VPN][express-route-or-VPN] 连接。

在与 AKS 群集相同的 VNET 中创建 VM 是最简单的选项。  快速路由和 VPN 会增加成本，且要求额外的网络复杂性。  虚拟网络对等互联要求计划网络 CIDR 范围，以确保不存在重叠范围。

## <a name="virtual-network-peering"></a>虚拟网络对等互连

如前所述，虚拟网络对等互连是一种访问专用群集的方法。 若要使用虚拟网络对等互连，需要在虚拟网络与专用 DNS 区域之间设置链接。
    
1. 转到 Azure 门户中的节点资源组。  
2. 选择专用 DNS 区域。   
3. 在左窗格中，选择“虚拟网络”链接。  
4. 创建新链接，将 VM 的虚拟网络添加到专用 DNS 区域。 DNS 区域链接需要几分钟时间才能变为可用。  
5. 在 Azure 门户中，导航到包含群集虚拟网络的资源组。  
6. 在右窗格中，选择“虚拟网络”。 虚拟网络名称的格式为 aks-vnet-\*。  
7. 在左窗格中，选择“对等互连”。  
8. 选择“添加”，添加 VM 的虚拟网络，然后创建对等互连。  
9. 转到你在其中具有 VM 的虚拟网络，选择“对等互连”、“AKS 虚拟网络”，然后创建对等互连。 如果 AKS 虚拟网络上的地址范围与 VM 的虚拟网络冲突，则对等互连将失败。 有关详细信息，请参阅[虚拟网络对等互联][virtual-network-peering]。

## <a name="hub-and-spoke-with-custom-dns"></a>具有自定义 DNS 的中心和分支

[中心和分支体系结构](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)通常用于在 Azure 中部署网络。 在许多此类部署中，会将分支 VNet 中的 DNS 设置配置为引用中心 DNS 转发器，以允许本地和基于 Azure 的 DNS 解析。 将 AKS 群集部署到此类网络环境中时，必须考虑一些特殊注意事项。

![专用群集中心和分支](media/private-clusters/aks-private-hub-spoke.png)

1. 默认情况下，预配专用群集后，会在群集托管资源组中创建专用终结点 (1) 和专用 DNS 区域 (2)。 群集使用专用区域中的 A 记录来解析专用终结点的 IP，以便与 API 服务器通信。

2. 专用 DNS 区域仅链接到群集节点附加到的 VNet (3)。 这意味着专用终结点只能由该链接 VNet 中的主机进行解析。 在 VNet 上不配置任何自定义 DNS（默认设置）的情况下，这可以正常工作，因为主机指向用于 DNS 的 168.63.129.16，因此可以解析专用 DNS 区域中的记录（由于存在链接）。

3. 在包含群集的 VNet 具有自定义 DNS 设置 (4) 的情况下，除非将专用 DNS 区域链接到包含自定义 DNS 解析程序的 VNet (5)，否则群集部署将失败。 可以在群集预配期间创建专用区域后手动创建此链接，也可以使用基于事件的部署机制（例如，Azure 事件网格和 Azure Functions）在检测到区域已创建后通过自动化来创建此链接。

> [!NOTE]
> 如果你[将自带路由表与 kubenet 配合使用](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet)，并且将自带 DNS 与专用群集配合使用，群集创建将会失败。 你需要在创建群集失败之后将节点资源组中的 [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) 关联到子网，以使创建能够成功。

## <a name="limitations"></a>限制 
* IP 授权范围不能应用于专用 API 服务器终结点，它们仅适用于公共 API 服务器
* [Azure 专用链接服务限制][private-link-service]适用于专用群集。
* 不支持具有专用群集的 Azure DevOps Microsoft 托管的代理。 请考虑使用[自托管代理](/azure/devops/pipelines/agents/agents?tabs=browser)。 
* 对于需要使 Azure 容器注册表能够与专用 AKS 配合使用的客户，容器注册表虚拟网络必须与代理群集虚拟网络对等互连。
* 不支持将现有 AKS 群集转换为专用群集
* 删除或修改客户子网中的专用终结点将导致群集停止运行。 
* 客户在自己的 DNS 服务器上更新 A 记录后，这些 Pod 仍会在迁移后将 apiserver FQDN 解析到较旧的 IP，直到重启这些 Pod。 客户需要在控制平面迁移之后重启 hostNetwork Pod 和 default-DNSPolicy Pod。
* 如果对控制平面进行维护，[AKS IP](./limit-egress-traffic.md) 可能会更改。 在这种情况下，你必须在自定义 DNS 服务器上更新指向 API 服务器专用 IP 的 A 记录，并重启使用 hostNetwork 的任何自定义 Pod 或部署。

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
