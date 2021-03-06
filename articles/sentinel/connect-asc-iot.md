---
title: 将用于 IoT 的 Azure Defender 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Azure Defender (以前的 Azure 安全中心) 用于 IoT 数据备份到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 67bc104434dc0db30f5973bec0979afb7480fe4c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621373"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>将数据从 Azure Defender (以前的 Azure 安全中心连接到 Azure Sentinel)  

使用用于 IoT 连接器的 Defender 将所有的 Defender for IoT 事件流式传输到 Azure Sentinel。 

这种集成使组织能够快速检测到经常跨越 IT 和边界的多阶段攻击。 此外，使用适用于 Azure Sentinel 的安全业务流程、自动化和响应 (之忠诚度) 功能的 Defender 可以通过内置的 " 
## <a name="prerequisites"></a>必备条件

- 对部署了 Azure Sentinel 的工作区的 **读取** 和 **写入** 权限
- 必须在相关 IoT 中心 (s **启用****用于 iot 的 Defender**) 
- 您必须对要连接的 **订阅** 具有 **参与者** 权限

## <a name="connect-to-defender-for-iot"></a>连接到 Defender for IoT

1. 在 Azure Sentinel 中，选择 " **数据连接器** "，然后选择 " **用于 iot 的 Defender** (可能仍被从库中称为" Iot) 的 Azure 安全中心 "。

1. 在右窗格底部，单击“打开连接器页”。 

1. 在要将其警报和设备警报流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁，单击“连接”。 
    - 如果在订阅中的至少一个 IoT 中心未启用 Defender for IoT，你将收到一条错误消息。 在 IoT 中心内为 IoT 启用 Defender 以删除错误。

1. 可以决定是否希望 Defender for IoT 中的警报在 Azure Sentinel 中自动生成事件。 在 " **创建事件**" 下，选择 " **启用** " 以启用默认分析规则，以便根据生成的警报自动创建事件。 可以在 "**分析**  >  **活动规则**" 下更改或编辑此规则。

> [!NOTE]
> 更改连接后， **订阅** 列表可能需要10秒钟或更长时间才能刷新。 

## <a name="log-analytics-alert-view"></a>Log Analytics 警报视图

若要在 Log Analytics 中使用相关架构显示 Defender for IoT 警报，请执行以下操作：

1. 打开“日志” > “SecurityInsights” > “SecurityAlert”，或搜索“SecurityAlert”。 

2. 使用以下 kql 筛选器进行筛选，以便仅查看 Defender for IoT 生成的警报：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>服务说明

连接 **订阅** 后，可以在 Azure Sentinel 中大约15分钟提供集线器数据。


## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Defender for IoT 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
