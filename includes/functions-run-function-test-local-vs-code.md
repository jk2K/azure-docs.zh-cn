---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 1a0521f76a2cf986f7036d1f701a40a156d16ee7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493431"
---
## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio Code 与 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) 相集成，便于你在发布到 Azure 之前在本地开发计算机上运行此项目。

1. 若要调用函数，请按 F5 启动函数应用项目<kbd></kbd>。 来自 Core Tools 的输出会显示在“终端”  面板中。 应用将在“终端”面板中启动。 可以看到 HTTP 触发函数的 URL 终结点在本地运行。

    ![本地函数 VS Code 输出](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    如果在 Windows 上运行时遇到问题，请确保用于 Visual Studio Code 的默认终端未设置为“WSL Bash”。

1. 运行 Core Tools 后，转到“Azure:Functions”区域。 在“Functions”下，展开“本地项目” > “Functions”  。 右键单击 (Windows) 或按 <kbd>Ctrl -</kbd> 单击 (macOS) `HttpExample` 函数，然后选择“立即执行函数...”。

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="立即在 Visual Studio Code 中执行函数":::

1. 在“输入请求正文”中，你将看到请求消息正文值 `{ "name": "Azure" }`。 按 Enter 将此请求消息发送给函数。  

1. 当函数在本地执行并返回响应时，Visual Studio Code 中将引发通知。 函数执行的相关信息将显示在“终端”面板中。

1. 按 Ctrl + C 停止 Core Tools 并断开调试器的连接<kbd></kbd>。
