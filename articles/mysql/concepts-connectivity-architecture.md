---
title: 连接体系结构 - Azure Database for MySQL
description: 描述 Azure Database for MySQL 服务器的连接体系结构。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 2a557bb436b3bc10cf83beb450761465b43f621f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655350"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的连接体系结构
本文介绍 Azure Database for MySQL 的连接体系结构，以及如何在 Azure 内部和外部将流量从客户端定向到 Azure Database for MySQL 实例。

## <a name="connectivity-architecture"></a>连接体系结构
可以通过网关连接到 Azure Database for MySQL，该网关负责将传入连接路由到服务器在群集中的物理位置。 下图演示了流量流。

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="连接体系结构概述":::

当客户端连接到数据库时，指向服务器的连接字符串将解析为网关 IP 地址。 网关在端口3306上侦听 IP 地址。 在数据库群集中，流量转发到相应的 Azure Database for MySQL。 因此，若要连接到您的服务器（例如公司网络），必须打开 **客户端防火墙以允许出站流量访问我们的网关**。 下面是一个按区域分类的可供我们的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database for MySQL 网关 IP 地址

网关服务托管在一个 IP 地址后面的无状态计算节点组上，当你的客户端尝试连接到 Azure Database for MySQL 服务器时，将首先访问该 IP 地址。 

在日常服务维护过程中，我们会定期刷新托管网关的计算硬件，以确保我们提供最安全和高性能的体验。 刷新网关硬件后，将首先生成计算节点的新环。 这一新环为所有新创建的 Azure Database for MySQL 服务器提供了流量，在同一区域中，它将具有不同的 IP 地址，以使流量区分开来。 新环完全正常运行后，为现有服务器提供服务的较旧的网关硬件将计划解除授权。 在解除网关硬件的授权之前，运行其服务器并连接到较旧网关环的客户将通过电子邮件和 Azure 门户中的三个月提前通知。 网关的解除授权可能会影响服务器与服务器的连接 

* 在应用程序的连接字符串中对网关 IP 地址进行硬编码。 **不建议使用** 此方法。 你应在 <servername> 应用程序的连接字符串中使用 mysql.database.azure.com 的完全限定域名 (FQDN) 。 
* 不会在客户端防火墙中更新更新的网关 IP 地址，以允许出站流量到达新的网关环。

下表列出了适用于所有数据区域的 Azure Database for MySQL 网关的网关 IP 地址。 下表中保留了每个区域的网关 IP 地址的最新信息。 在下表中，列表示以下内容：

* **网关 IP 地址：** 此列列出了托管在最新一代硬件上的网关的当前 IP 地址。 如果要设置新服务器，我们建议打开客户端防火墙以允许此列中列出的 IP 地址的出站流量。
* **网关 IP 地址 (解除授权) ：** 此列列出了托管在较早代硬件上的网关的 IP 地址。 如果要设置新服务器，则可以忽略这些 IP 地址。 如果你有现成的服务器，请继续为这些 IP 地址保留防火墙的出站规则，因为我们尚未解除它的授权。 如果删除这些 IP 地址的防火墙规则，可能会出现连接错误。 相反，你应该在收到要解除授权的通知后，立即主动将 "网关 IP 地址" 列中列出的新 IP 地址添加到出站防火墙规则。 这将确保将服务器迁移到最新的网关硬件后，不会中断与服务器的连接。
* **网关 IP 地址 (取消) ：** 此列列出网关环的 IP 地址，这些地址已解除授权，不再处于操作中。 可以安全地从出站防火墙规则中删除这些 IP 地址。 


| **区域名称** | **网关 IP 地址** |**网关 IP 地址 (解除授权)** | **网关 IP 地址 (解除授权)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| 澳大利亚中部| 20.36.105.0  | | |
| 澳大利亚 Central2     | 20.36.113.0  | | |
| 澳大利亚东部 | 13.75.149.87, 40.79.161.1     |  | |
| 澳大利亚东南部 |191.239.192.109, 13.73.109.251   |  | |
| Brazil South |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| 加拿大中部 |40.85.224.249  | | |
| 加拿大东部 | 40.86.226.166    | | |
| 美国中部 | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38 | | |
| 中国东部 | 139.219.130.35    | | |
| 中国东部 2 | 40.73.82.1  | | |
| 中国北部 | 139.219.15.17    | | |
| 中国北部 2 | 40.73.50.0     | | |
| 东亚 | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| 美国东部 |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| 美国东部 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  | | |
| 法国中部 | 40.79.137.0, 40.79.129.1  | | |
| 法国南部 | 40.79.177.0     | | |
| 德国中部 | 51.4.144.100     | | |
| 德国东北部 | 51.5.144.179  | | |
| 印度中部 | 104.211.96.159     | | |
| 印度南部 | 104.211.224.146  | | |
| 印度西部 | 104.211.160.80    | | |
| Japan East | 13.78.61.196, 191.237.240.43, 40.79.192.23 | | |
| 日本西部 | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    | | |
| 韩国中部 | 52.231.32.42   | | |
| 韩国南部 | 52.231.200.86    | | |
| 美国中北部 | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    | | |
| 北欧 | 52.138.224.6, 52.138.224.7  |40.113.93.91 |191.235.193.75 |
| 南非北部  | 102.133.152.0    | | |
| 南非西部 | 102.133.24.0   | | |
| 美国中南部 |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| 东南亚 | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     | | |
| 阿联酋中部 | 20.37.72.64  | | |
| 阿拉伯联合酋长国北部 | 65.52.248.0    | | |
| 英国南部 | 51.140.184.11   | | |
| 英国西部 | 51.141.8.11  | | |
| 美国中西部 | 13.78.145.25     | | |
| 西欧 |13.69.105.208,104.40.169.187 |40.68.37.158 | 191.237.232.75|
| 美国西部 |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| 美国西部 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>连接重定向

Azure Database for MySQL 支持一个额外的连接策略（即“重定向”  ），该策略有助于降低客户端应用程序与 MySQL 服务器之间的网络延迟。 利用此功能，在建立与 Azure Database for MySQL 服务器的初始 TCP 会话后，服务器会将承载 MySQL 服务器的节点的后端地址返回到客户端。 此后，所有后续数据包会绕过网关直接流向服务器。 由于数据包会直接流向服务器，因此延迟和吞吐量这两个指标的表现得到了改善。

引擎版本为 5.6、5.7 和 8.0 的 Azure Database for MySQL 服务器支持此功能。

对重定向的支持可通过 Microsoft 开发的 PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展获得，也可在 [PECL](https://pecl.php.net/package/mysqlnd_azure) 上获得。 有关如何在应用程序中使用重定向的详细信息，请参阅[配置重定向](./howto-redirection.md)一文。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展对重定向的支持目前为预览版。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)
* [使用 Azure Database for MySQL 配置重定向](./howto-redirection.md)
