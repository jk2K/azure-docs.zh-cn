---
title: Azure 通信服务 - Web 呼叫示例
titleSuffix: An Azure Communication Services sample
description: 了解通信服务 Web 呼叫示例
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a00f319c55665695879aac3c8ba711ce13fd5d4e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763044"
---
# <a name="get-started-with-the-web-calling-sample"></a>开始了解 Web 呼叫示例

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Github 上提供了此示例。](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Azure 通信服务 Web 呼叫示例演示了如何使用通信服务呼叫客户端库和 JavaScript 来构建呼叫体验。

在本地计算机上运行该示例之前，我们将通过本示例快速入门了解其工作原理。 然后，使用你自己的 Azure 通信服务资源将此示例部署到 Azure。

## <a name="overview"></a>概述

Web 呼叫示例是一个 Web 应用程序，可分步演练由通信服务 Web 呼叫客户端库提供的各种功能。 

此示例是为开发人员构建的，可让你轻松开始使用通信服务。 它的用户界面分为多个部分，每个部分都带有一个“显示代码”按钮，可用于将代码直接从浏览器复制到你自己的通信服务应用程序。

当 [Web 呼叫示例](https://github.com/Azure-Samples/communication-services-web-calling-tutorial)在计算机上运行时，将看到以下登陆页面：

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Web 呼叫教程 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Web 呼叫教程 2" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>用户预配和 SDK 初始化 

若要开始使用演示，请将[通信服务资源](../quickstarts/create-communication-resource.md)中的连接字符串输入到 `config.json` 中。 这将用于预配[用户访问令牌](../concepts/authentication.md)，以便可以初始化你的呼叫 SDK。

在“用户标识”输入中输入你自己的个人标识符。 如果此处未提供任何内容，则将生成一个随机的用户标识。 

单击“预配用户并初始化 SDK”，通过后端令牌预配服务预配的令牌初始化 SDK。 此后端服务位于 `/project/webpack.config.js` 中。

单击“显示代码”按钮，查看可以在你自己的解决方案中使用的示例代码。

初始化 SDK 后，应看到以下内容：

:::image type="content" source="./media/user-provisioning.png" alt-text="用户预配" lightbox="./media/user-provisioning.png":::

你现在可以使用通信服务资源来拨打电话！

## <a name="placing-and-receiving-calls"></a>拨打和接听电话

通信服务 Web 呼叫 SDK 支持一对一，一对多和群组通话  。

对于一对一或一对多传出呼叫，可以使用逗号分隔的值指定多个要呼叫的通信服务用户标识。 还可以使用逗号分隔的值来指定要呼叫的传统 (PSTN) 电话号码。 

呼叫 PSTN 电话号码时，请指定备用呼叫方 ID。 单击“拨打电话”按钮以拨打传出呼叫：

:::image type="content" source="./media/place-a-call.png" alt-text="拨打电话" lightbox="./media/place-a-call.png":::

若要加入群组通话，请输入标识此通话的 GUID，并单击“加入群组”按钮：

:::image type="content" source="./media/join-a-group-call.png" alt-text="加入群组通话" lightbox="./media/join-a-group-call.png":::

单击“显示代码”按钮，查看用于拨打电话、接听电话以及加入群组通话的示例代码。

有效的呼叫如下所示：

:::image type="content" source="./media/group-call.png" alt-text="组呼叫" lightbox="./media/group-call.png":::

此示例还提供了用于以下功能的代码片段：

  - 单击视频图标可打开/关闭摄像机
  - 单击麦克风图标可打开/关闭麦克风
  - 单击播放图标可保持/取消保持通话
  - 单击屏幕图标可开始/停止共享屏幕
  - 单击人员图标可将参与者添加到通话
  - 在参与者名单中单击“删除参与者”可从通话中删除特定参与者


## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"] 
>[从 GitHub 下载示例](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

有关详细信息，请参阅以下文章：

- 熟悉如何[使用呼叫客户端库](../quickstarts/voice-video-calling/calling-client-samples.md)
- 了解有关[呼叫工作原理](../concepts/voice-video-calling/about-call-types.md)的详细信息
- 查看 [API 参考文档](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)
- 查看 [Contoso Med 应用](https://github.com/Azure-Samples/communication-services-contoso-med-app)示例

## <a name="additional-reading"></a>其他阅读材料

- [Azure 通信 GitHub](https://github.com/Azure/communication) - 在官方 GitHub 页上查找更多示例和信息
- [Redux](https://redux.js.org/) - 客户端状态管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft 支持的 UI 库
- [React](https://reactjs.org/) - 用于构建用户界面的库
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - 用于构建 Web 应用程序的框架