---
title: Azure Functions 中的事件驱动的缩放
description: 介绍消耗计划和高级计划函数应用的扩展行为。
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: 8aca1ab6629f95ef9162e1247434bd3189d5a7d2
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937592"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Azure Functions 中的事件驱动的缩放

在消耗和高级计划中，Azure Functions 通过添加函数主机的其他实例来缩放 CPU 和内存资源。 实例数取决于触发函数的事件数。 

消耗计划中 Functions 主机的每个实例仅限使用 1.5 GB 内存和 1 个 CPU。  主机实例是整个函数应用，这意味着函数应用中的所有函数共享某个实例中的资源并同时缩放。 独立共享同一消耗计划的函数应用。  在高级计划中，计划大小确定该计划在该实例上的所有应用程序的可用内存和 CPU。  

函数代码文件存储在函数主要存储帐户中的 Azure 文件共享上。 删除函数应用的主存储帐户时，函数代码文件将被删除并且无法恢复。

## <a name="runtime-scaling"></a>运行时缩放

Azure Functions 使用名为“缩放控制器”的组件来监视事件率以及确定是要横向扩展还是横向缩减。 缩放控制器针对每种触发器类型使用试探法。 例如，使用 Azure 队列存储触发器时，它会根据队列长度和最旧队列消息的期限进行缩放。

Azure Functions 的缩放单位为函数应用。 横向扩展函数应用时，将分配额外的资源来运行 Azure Functions 主机的多个实例。 相反，计算需求下降时，扩展控制器将删除函数主机实例。 当函数应用内没有运行任何函数时，实例的数量最终 "放大" 为零。

![用于监视事件和创建实例的扩展控制器](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>冷启动

当你的函数应用空闲一定的分钟数后，平台可能会将用于运行你的应用的实例数量缩减为零。 下次请求将存在从零扩展到一所增加的延迟。 此延迟称为“冷启动”。 函数应用所需的依赖项的数目可能会影响冷启动时间。 冷启动对于同步操作（例如，必须返回响应的 HTTP 触发器）来说更是一个问题。 如果冷启动会影响你的功能，请考虑在高级计划或专用计划（启用了 **Always on** 设置）中运行。   

## <a name="understanding-scaling-behaviors"></a>了解缩放行为

缩放可根据多种因素而异，可根据选定的触发器和语言以不同的方式缩放。 需要注意缩放行为的以下几个细节：

* **最大实例数：** 单函数应用仅可扩大到最多200个实例。 不过，单个实例每次可以处理多个消息或请求，因此，对并发执行数没有规定的限制。  可根据需要[指定一个较低的最大值](#limit-scale-out)来限制缩放。
* **新实例速率：** 对于 HTTP 触发器，每秒最多分配一个新实例。 对于非 HTTP 触发器，将最多每隔 30 秒分配一次新实例。 在[高级计划](functions-premium-plan.md)中运行时，缩放速度会更快。
* **规模提高：** 对于服务总线触发器，使用对资源的 _管理_ 权限，以实现最有效的缩放。 使用 _侦听_ 权限时，由于队列长度不能用于通知缩放决策，缩放不够准确。 若要详细了解如何在服务总线访问策略中设置权限，请参阅[共享访问授权策略](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)。 有关事件中心触发器，请参阅参考文章中的[缩放指南](functions-bindings-event-hubs-trigger.md#scaling)。 

## <a name="limit-scale-out"></a>限制横向扩展

你可能想要限制应用用于横向扩展的实例的最大数量。 最常见的情况是，下游组件（如数据库）的吞吐量有限。  默认情况下，消耗计划函数向外扩展到尽可能多的200实例，高级计划函数将扩展到最多100个实例。  可修改 `functionAppScaleLimit` 值来指定特定应用的较低最大值。  `functionAppScaleLimit`对于 "无限制"，可以设置为 `0` 或 `null` ，或在 `1` 和应用最大之间设置有效的值。

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>可缩放应用的最佳做法和模式

函数应用的许多方面会影响其缩放方式，包括主机配置、运行时占用空间和资源效率。  有关详细信息，请查看[性能注意事项一文的“可扩展”部分](functions-best-practices.md#scalability-best-practices)。 还要注意随着函数应用的扩展，连接是如何实施的。 有关详细信息，请参阅[如何在 Azure Functions 中管理连接](manage-connections.md)。

有关 Python 和 Node.js 中的缩放的详细信息，请参阅 [Azure Functions Python 开发人员指南-缩放和并发性](functions-reference-python.md#scaling-and-performance) 和 [Azure Functions Node.js 开发人员指南-缩放和并发](functions-reference-node.md#scaling-and-concurrency)。

## <a name="billing-model"></a>计费模式

不同计划的计费在 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)中有详细介绍。 使用量在 Function App 级别聚合，只会统计函数代码的执行时间。 以下是计费单位：

* 以千兆字节/秒 (GB-s) 计量的资源消耗量。 根据内存大小和函数应用中所有函数的执行时间组合计算得出。 
* **执行**。 每次为响应事件触发而执行函数时记为一次。

在[帐单常见问题解答](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)中可以找到有关如何了解消费帐单的有用查询和信息。

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 宿主选项](functions-scale.md)

