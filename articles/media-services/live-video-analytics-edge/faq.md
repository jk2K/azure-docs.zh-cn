---
title: IoT Edge 上的实时视频分析常见问题解答 - Azure
description: 本文解答了有关 IoT Edge 上的实时视频分析的常见问题。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 0cb378bf614582070dd1bdd0a11706b26437af53
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880044"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>IoT Edge 上的实时视频分析常见问题解答

本文解答了有关 Azure IoT Edge 上的实时视频分析的常见问题。

## <a name="general"></a>常规

我可以在图拓扑定义中使用哪些系统变量？

| 变量   |  说明  | 
| --- | --- | 
| [System.DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | 表示某个 UTC 时刻，通常以日期和当天的时间表示，格式如下：<br>yyyyMMddTHHmmssZ | 
| System.PreciseDateTime | 表示 ISO8601 文件兼容格式的协调世界时 (UTC) 日期/时间实例（精确到毫秒），格式如下：<br>yyyyMMddTHHmmss.fffZ | 
| System.GraphTopologyName   | 表示媒体图拓扑，保存图的蓝图。 | 
| System.GraphInstanceName |    表示媒体图实例，保存参数值并引用拓扑。 | 

## <a name="configuration-and-deployment"></a>配置和部署

**是否可以将媒体边缘模块部署到 Windows 10 设备？**

是的。 有关详细信息，请参阅 [Windows 10 上的 Linux 容器](/virtualization/windowscontainers/deploy-containers/linux-containers)。

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>从 IP 相机和 RTSP 设置捕获

**是否需要在设备上使用特殊的 SDK 来发送视频流？**

否。IoT Edge 上的实时视频分析支持使用 RTSP（实时流式处理协议）进行视频流式处理来捕获媒体，大多数 IP 相机支持此协议。

是否可以使用实时消息传送协议 (RTMP) 或平滑流式处理协议（如媒体服务实时事件）将媒体推送到 IoT Edge 上的实时视频分析？

否。实时视频分析仅支持使用 RTSP 从 IP 相机捕获视频。 任何支持通过 TCP/HTTP 进行 RTSP 流式处理的相机都应正常工作。 

是否可以在图实例中重置或更新 RTSP 源 URL？

可以。在图实例处于非活动状态时可以进行重置或更新。  

是否可以在测试和开发期间使用 RTSP 模拟器？

可以。可以在快速入门和教程中使用 [RTSP 模拟器](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)边缘模块来支持学习过程。 我们会尽力提供此模块，但它可能并非始终都可用。 强烈建议你不要让使用模拟器的时间超过数小时。 在计划生产部署之前，应使用实际的 RTSP 源进行测试。

**你们是否支持 ONVIF 在边缘发现 IP 照相机？**

否。我们不支持通过开放型网络视频接口论坛 (ONVIF) 在边缘发现设备。

## <a name="streaming-and-playback"></a>流式处理和播放

是否可以使用流式处理技术（例如 HLS 或 DASH）播放从边缘录制到 Azure 媒体服务的资产？

是的。 可以像处理 Azure 媒体服务中的任何其他资产一样，对录制的资产进行流式处理。 若要流式处理内容，必须已创建流式处理终结点，并使其处于正在运行状态。 通过使用标准流式处理定位器创建过程，你可以访问 Apple HTTP Live Streaming (HLS) 或通过 HTTP 的动态自适应流式处理（DASH，也称 MPEG-DASH）清单，以便将其流式传输到任何支持的播放器框架。 若要详细了解如何创建和发布 HLS 或 DASH 清单，请参阅[动态打包](../latest/dynamic-packaging-overview.md)。

**是否可以在存档资产上使用媒体服务的标准内容保护和 DRM 功能？**

是的。 所有标准动态加密内容保护和数字版权管理 (DRM) 功能都可用于从媒体图录制的资产。

**我可以使用哪些播放器来查看录制的资产中的内容？**

支持兼容的 HLS 版本 3 或版本 4 的所有标准播放机均受支持。 此外，还支持能够进行兼容 MPEG-DASH 播放的任何播放机。

建议用于测试的播放器包括：

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Apple 本机 HTTP Live Streaming](https://developer.apple.com/streaming/)
* HTML5 视频播放机中内置的 Edge、Chrome 或 Safari
* 支持 HLS 或 DASH 播放的商业播放器

**对媒体图形资产进行流式处理存在哪些限制？**

从媒体图流式处理实时资产或录制资产时会使用大规模基础结构和流式处理终结点，媒体服务支持相同的大规模基础结构和流式处理终结点，以便为媒体和娱乐客户、Over the Top (OTT) 客户以及广播客户进行按需和实时流式处理。 这意味着你可以快速轻松地启用 Azure 内容分发网络、Verizon 或 Akamai，以将你的内容传送给受众。受众规模可以少至几个观众，也可以多达数百万观众，具体取决于你的情况。

可以使用 Apple HLS 或 MPEG-DASH 来传送内容。

## <a name="design-your-ai-model"></a>设计 AI 模型 

我有多个封装在 Docker 容器中的 AI 模型。如何在实时视频分析中使用它们？ 

解决方案因推理服务器用来与实时视频分析通信的通信协议而异。 以下部分介绍每个协议的工作原理。

使用 HTTP 协议：

* 单个容器（单个 lvaExtension）：  

   在推理服务器中，可以使用单个端口，但不同 AI 模型的终结点不同。 例如，对于 Python 示例，可以为每个模型使用不同的 `route`，如下所示： 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   然后，在实时视频分析部署中将图进行实例化时，为每个实例设置推理服务器 URL，如下所示： 

   第一个实例：推理服务器 URL = `http://lvaExtension:44000/score/face_detection`<br/>
   第二个实例：推理服务器 URL = `http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > 也可在不同的端口上公开 AI 模型，并在将图进行实例化时调用它们。  

* 多个容器： 

   使用不同的名称部署每个容器。 以前，我们在实时视频分析文档集中介绍了如何部署名为 lvaExtension 的扩展。 现在，你可以开发两个不同的容器，每个容器使用相同的 HTTP 接口，这意味着它们具有相同的 `/score` 终结点。 使用不同的名称部署这两个容器，并确保两个容器在不同的端口上进行侦听。 

   例如，名为 `lvaExtension1` 的一个容器侦听端口 `44000`，另一个名为 `lvaExtension2` 的容器侦听端口 `44001`。 

   在实时视频分析拓扑中，请使用不同的推理 URL 将两个图进行实例化，如下所示： 

   第一个实例：推理服务器 URL = `http://lvaExtension1:44001/score`    
   第二个实例：推理服务器 URL = `http://lvaExtension2:44001/score`
   
使用 gRPC 协议： 

* 在使用实时视频分析模块 1.0 的情况下，当使用常规用途远程过程调用 (gRPC) 协议时，那样做的唯一方法是让 gRPC 服务器通过不同的端口公开不同的 AI 模型。 在[此代码示例](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)中，单个端口 (44000) 公开了所有 yolo 模型。 理论上，可以重写 yolo gRPC 服务器，在端口 44000 上公开一些模型，在端口 45000 上公开另外一些模型。 

* 在使用实时视频分析模块 2.0 的情况下，新属性会添加到 gRPC 扩展节点。 此属性 (extensionConfiguration) 是可用作 gRPC 协定的一部分的可选字符串。 在单个推理服务器中打包多个 AI 模型时，无需为每个 AI 模型公开一个节点。 对于图实例，你可以作为扩展提供者来定义如何通过使用 extensionConfiguration 属性来选择不同的 AI 模型。 在执行期间，实时视频分析会将此字符串传递给推理服务器，而推理服务器则可以使用它来调用所需的 AI 模型。 

我在围绕 AI 模型构建 gRPC 服务器，并且我希望能够支持其可供多个相机或图实例使用。应如何构建服务器？ 

 首先，请确保服务器可以一次处理多个请求，或者可以在并行线程中运行。 

例如，以下[实时视频分析 gRPC 示例](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)中设置了默认的并行通道数： 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

在上述 gRPC 服务器实例化中，服务器一次只能为每个相机或每个图拓扑实例打开三个通道。 不要尝试将三个以上的实例连接到服务器。 如果尝试打开三个以上的通道，请求会处于挂起状态，直到删除某个现有的通道。  

上面的 gRPC 服务器实现用在 Python 示例中。 作为开发人员，你可以实现自己的服务器或使用前面的默认实现来增加辅助角色数目，你需要将该数目设置为用于视频源的相机数。 

若要设置和使用多个相机，可以实例化多个图拓扑实例，每个实例指向相同的或不同的推理服务器（例如，上一段中提到的服务器）。 

我希望能在进行推理决策之前从上游接收多个帧。如何实现它？ 

当前的[默认示例](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis)在无状态模式下工作。 它们不会保留前面的调用的状态，甚至不会保留调用方。 这意味着多个拓扑实例可能会调用同一推理服务器，但服务器无法区分正在进行调用的是哪个调用方，或者无法区分每个调用方的状态。 

使用 HTTP 协议：

若要保留状态、每个调用方或图拓扑实例，请使用调用方特有的 HTTP 查询参数来调用推理服务器。 例如，每个实例的推理服务器 URL 地址如下所示：  

第一个拓扑实例 = `http://lvaExtension:44000/score?id=1`<br/>
第二个拓扑实例 = `http://lvaExtension:44000/score?id=2`

… 

在服务器端，分数路由知道谁正在进行调用。 如果 ID = 1，则它可以单独为该调用方或图拓扑实例保留状态。 然后，你可以将接收的视频帧保留在缓冲区中。 例如，可以使用数组，或者使用带有 DateTime 键的字典，并且值为帧。 然后，可以定义要在接收 x 个帧后进行处理（推断）的服务器。 

使用 gRPC 协议： 

在使用 gRPC 扩展的情况下，一个会话对应于一个相机源，因此不需提供 ID。 现在，在使用 extensionConfiguration 属性的情况下，可以将视频帧存储在缓冲区中，并定义要在接收 x 个帧后进行处理（推断）的服务器。 

特定容器上的所有 ProcessMediaStream 是否都运行同一 AI 模型？ 

否。 启动或停止从图实例中的最终用户进行的调用会构成一个会话，也可能存在相机断开连接或重新连接的情况。 如果相机是在流式处理视频，则目标是保留一个会话。 

* 两个发送视频进行处理的相机会创建两个会话。 
* 如果将一个相机放入一个包含两个 gRPC 扩展节点的图中，该相机会创建两个会话。 

每个会话都是实时视频分析与 gRPC 服务器之间的全双工连接，可以有不同的模型或管道。 

> [!NOTE]
> 如果相机断开连接或重新连接，并且相机脱机的时间超出容许限制，则实时视频分析会打开一个与 gRPC 服务器的新会话。 不需服务器跟踪这些会话的状态。 

实时视频分析还为图实例中的单个相机添加了针对多个 gRPC 扩展的支持。 你可以使用这些 gRPC 扩展以串行和/或并行的方式执行 AI 处理。 

> [!NOTE]
> 并行运行多个扩展会影响硬件资源。 在选择符合计算需求的硬件时，请记住这一点。 

同时出现的 ProcessMediaStream 的最大数量是多少？ 

实时视频分析不对此进行限制。  

如何确定我的推理服务器应使用 CPU、GPU 还是其他硬件加速器？ 

你的决定取决于开发的 AI 模型的复杂性，以及你希望如何使用 CPU 和硬件加速器。 在开发 AI 模型时，可以指定模型应使用的资源和应执行的操作。 

如何在处理后存储带边界框的图像？ 

目前，我们仅将边界框坐标作为推理消息提供。 可以构建一个可使用这些消息并覆盖视频帧上的边界框的自定义 MJPEG 流转化器。  

## <a name="grpc-compatibility"></a>gRPC 兼容性 

我如何知道媒体流描述符的必填字段有哪些？ 

对于尚未为其提供值的任何字段，系统会为其提供一个[由 gRPC 指定的默认值](https://developers.google.com/protocol-buffers/docs/proto3#default)。  

实时视频分析使用 proto3 版本的协议缓冲区语言。 实时视频分析协定使用的所有协议缓冲区数据都可以在[协议缓冲区文件](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)中找到。 

如何确保我使用的是最新的协议缓冲区文件？ 

可以在[协定文件站点](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc)上获取最新的协议缓冲区文件。 每当我们更新协定文件时，就会将其置于此位置。 我们尚无更新协议文件的即时计划，因此，请在文件顶部查找包名称以了解版本。 此内容应为： 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

对这些文件进行更新时，会将名称末尾的“v-value”递增。 

> [!NOTE]
> 由于实时视频分析使用语言的 proto3 版本，因此字段为可选，版本后向和前后兼容。 

可以通过实时视频分析使用哪些 gRPC 功能？哪些功能是必需的？哪些功能是可选的？ 

如果已履行协议缓冲区 (Protobuf) 协定，则可使用任何服务器端 gRPC 功能。 

## <a name="monitoring-and-metrics"></a>监视和指标

是否可以使用 Azure 事件网格监视边缘上的媒体图？

是的。 可以使用 Prometheus 指标并将其发布到事件网格。 

**是否可以使用 Azure Monitor 查看云端或边缘的媒体图形的运行状况、指标和性能？**

可以。我们支持此方法。 若要了解详细信息，请参阅 [Azure Monitor 指标概述](../../azure-monitor/platform/data-platform-metrics.md)。

**是否有任何工具可用于简化监视媒体服务 IoT Edge 模块？**

Visual Studio Code 支持 Azure IoT Tools 扩展，该扩展可用于轻松监视 LVAEdge 模块终结点。 可以使用此工具快速地开始监视 IoT 中心内置终结点中的“事件”，并查看从边缘设备路由到云的推理消息。 

此外，还可以使用此扩展编辑 LVAEdge 模块的模块孪生，以修改媒体图设置。

有关详细信息，请参阅[监视和日志记录](monitoring-logging.md)一文。

## <a name="billing-and-availability"></a>计费和可用性

IoT Edge 上的实时视频分析如何计费？

如需计费详细信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="next-steps"></a>后续步骤

[快速入门：IoT Edge 上的实时视频分析入门](get-started-detect-motion-emit-events-quickstart.md)