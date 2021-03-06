---
title: 导入设备信息
description: 用于 IoT 传感器的 Defender 监视和分析镜像的流量。 在这些情况下，你可能需要导入数据，以便丰富已检测到的设备上的信息。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/06/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 333ffbf4107dfd005ba7e7fae6a079a618e0c645
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509429"
---
# <a name="import-device-information-to-a-sensor"></a>将设备信息导入传感器

用于 IoT 的 Azure Defender 传感器监视和分析镜像的流量。 在某些情况下，由于组织特定的网络配置策略，某些信息可能无法传输。

在这些情况下，你可能需要导入数据，以丰富有关已检测到的设备的信息。 有两个选项可用于将信息导入传感器：

- **从地图导入**：将设备名称、类型、组或 Purdue 层更新到地图。

- **从导入设置导** 入：导入设备 OS、IP 地址、修补程序级别或授权状态。

## <a name="import-from-the-map"></a>从地图导入

本部分介绍如何将设备名称、类型、组或 Purdue 层导入到设备映射。 可从地图中执行此操作。

下面是导入要求：

- **名称**：最多可包含30个字符。

- **类型** 或 **Purdue 层**：使用 " **设备属性** " 对话框中显示的选项。  (右键单击该设备，然后选择 " **查看属性**"。 ) 

- **设备组**：创建最多为30个字符的新组。 

> [!NOTE]
> 若要避免冲突，请不要将从一个传感器导出的数据导入到另一个传感器。

导入：

1. 在侧边菜单中，选择 " **设备**"。

2. 在 " **设备** " 窗口的右上角，选择 "" :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="设备窗口的屏幕截图。":::

3. 选择 " **导出设备**"。 导出的文件中将显示范围广泛的信息。 此信息包括设备使用的协议和设备授权状态。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="导出的文件中的信息。":::

4. 在 CSV 文件中，仅更改设备名称、类型、组和 Purdue 层。 然后保存文件。 

   使用导出的文件中显示的大小写标准。 例如，对于 Purdue 层，使用全部首字母大写。

5. 从 "**设备**" 窗口中的 "**导入/导出**" 下拉菜单中，选择 "**导入设备**"。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="通过设备窗口导入设备。":::

6. 选择 " **导入设备** "，然后选择要导入的 CSV 文件。 导入状态消息将出现在屏幕上，直到 " **导入设备** " 对话框关闭。

## <a name="import-from-import-settings"></a>从导入设置导入

本部分介绍如何将设备 IP 地址、OS、修补级别或授权状态导入到设备映射。 可以从 " **导入设置** " 对话框执行此操作。

导入 IP 地址、OS 和修补程序级别：

1. 从[帮助中心](https://cyberx-labs.zendesk.com/hc/en-us)下载[devices_info_2 的 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data)文件，并按如下所示输入信息：

   - **Ip 地址**：输入设备 IP 地址。

   - **操作系统**：从下拉列表中选择。

   - **上次更新**：使用 yyyy-mm-dd 格式。

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="选项屏幕。":::

2. 在侧边菜单中，选择 " **导入设置**"。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="导入设置。":::

3. 若要上传所需的配置，请在 " **设备信息** " 部分中选择 " **添加** 并上传已准备的 CSV 文件。

导入授权状态：

1. 从 "IoT 帮助中心" 下载并保存 [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 文件。 验证是否已将文件保存为 CSV。

2. 输入如下所示的信息：

   - **Ip 地址**：设备 ip 地址。

   - **名称**：授权的设备名称。 请确保名称准确无误。 为导入列表中的设备指定的名称将覆盖设备映射中显示的名称。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="包含导入设备列表的 Excel 文件。":::

3. 在侧边菜单中，选择 " **导入设置**"。

4. 在 " **授权的设备** " 部分中，选择 " **添加** "，并上传已保存的 CSV 文件。

导入信息时，会收到有关未在此列表中出现的所有设备的未经授权设备的警报。

## <a name="import-device-information-to-the-sensor"></a>将设备信息导入传感器

传感器监视和分析镜像的流量。 在某些情况下，由于组织特定的网络配置策略，某些信息可能无法传输。

在这些情况下，你可能需要导入数据，以便在已检测到的设备上丰富设备信息。 有两个选项可用于将信息导入传感器：

- **从地图导入**：将设备名称、类型、组或 Purdue 层更新到地图。

- **从导入设置导** 入：导入设备 OS、IP 地址、修补程序级别或授权状态。

### <a name="import-from-the-map"></a>从地图导入

本部分介绍如何将设备名称、类型、组或 Purdue 层导入到设备映射。 可从地图中执行此操作。

下面是导入要求：

- **名称**：最多可包含30个字符。

- **类型** 或 **Purdue 层**：使用 " **设备属性** " 对话框中显示的选项。  (右键单击该设备，然后选择 " **查看属性**"。 ) 

- **设备组**：创建最多为30个字符的新组。 

> [!NOTE]
> 若要避免冲突，请不要将从一个传感器导出的数据导入到另一个传感器。

导入：

1. 在侧边菜单中，选择 " **设备**"。

2. 在 " **设备** " 窗口的右上角，选择 "" :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false"::: 。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="从中选择设备的窗口。":::

3. 选择 " **导出设备**"。 导出的文件中将显示范围广泛的信息。 示例包括设备使用的协议和设备授权状态。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="导出的文件中的信息。":::

4. 在 CSV 文件中，仅更改设备名称、类型、组和 Purdue 层。 然后保存文件。 

   使用导出的文件中显示的大小写标准。 例如，对于 Purdue 层，使用全部首字母大写。

5. 从 "**设备**" 窗口中的 "**导入/导出**" 下拉菜单中，选择 "**导入设备**"。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="导入设备。":::

6. 选择 " **导入设备** "，然后选择要导入的 CSV 文件。 导入状态消息将出现在屏幕上，直到 " **导入设备** " 对话框关闭。

### <a name="import-from-import-settings"></a>从导入设置导入 

本部分介绍如何将设备 IP 地址、OS、修补级别或授权状态导入到设备映射。 可以从 " **导入设置** " 对话框执行此操作。

导入 IP 地址、OS 和修补程序级别：

1. 从[帮助中心](https://cyberx-labs.zendesk.com/hc/en-us)下载[devices_info_2 的 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data)文件，并按如下所示输入信息：

   - **Ip 地址**：设备 ip 地址。

   - **操作系统**：从下拉列表中选择。

   - **上次更新日期**：使用 yyyy-mm-dd 格式。

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="屏幕上的内容。":::

2. 在侧边菜单中，选择 " **导入设置**"。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="填写 &quot;导入设置&quot; 屏幕。":::

3. 若要上传所需的配置，请在 " **设备信息** " 部分中选择 " **添加** 并上传已准备的 CSV 文件。

导入授权状态：

1. 从 "IoT 帮助中心" 下载并保存 [authorized_devices examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 文件。 验证是否已将文件保存为 CSV。

2. 输入如下所示的信息：

   - **Ip 地址**：设备 ip 地址。

   - **名称**：授权的设备名称。 验证名称是否准确。 为导入列表中的设备指定的名称将覆盖设备映射中显示的名称。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="导入到设备映射的列表。":::

3. 在侧边菜单中，选择 " **导入设置**"。

4. 在 " **授权的设备** " 部分中，选择 " **添加** "，并上传已保存的 CSV 文件。

导入信息时，会收到有关未在此列表中出现的所有设备的未经授权设备的警报。

## <a name="see-also"></a>另请参阅

[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)

[调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
