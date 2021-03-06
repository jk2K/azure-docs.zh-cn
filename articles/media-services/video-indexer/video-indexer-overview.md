---
title: 什么是 Azure 媒体服务视频索引器？
titleSuffix: Azure Media Services
description: 本文概要介绍了 Azure 媒体服务视频索引器服务。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/11/2020
ms.author: juliako
ms.openlocfilehash: 06f5e19718445f44dd2302faf280f083cce0774f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98783795"
---
# <a name="what-is-azure-media-services-video-indexer"></a>什么是 Azure 媒体服务视频索引器？

[!INCLUDE [regulation](./includes/regulation.md)]

视频索引器 (VI) 是 Azure 媒体服务 AI 解决方案，也是 Azure 认知服务品牌的一部分。 使用视频索引器，可以基于多通道（语音、声乐、视觉对象）使用机器学习模型来提取深度见解（无需数据分析或编码技能）。 另外，你还可以自定义和训练模型。 该服务可实现深度搜索，降低运营成本，创造新的盈利机会，并在大型的视频存档方面创造新的用户体验（具有较低准入门槛）。

若要开始使用视频索引器提取见解，你需要先创建帐户并上传视频。 将视频上传到视频索引器时，会通过运行不同的 AI 模型来分析视觉对象和音频。 当视频索引器分析视频时，由 AI 模型提取的见解。

创建视频索引器帐户并将其连接到媒体服务时，媒体和元数据文件存储在与该媒体服务帐户关联的 Azure 存储帐户中。 有关详细信息，请参阅 [创建连接到 Azure 的视频索引器帐户](connect-to-azure.md)。

下图是对视频索引器后台工作的阐释，并非技术说明。

![Azure 媒体服务视频索引器流程图](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>符合性、隐私和安全性

需要重点提醒的是，在使用视频索引器时，你必须遵守所有适用法律，不得以侵犯他人权利或可能对他人有害的方式使用视频索引器或任何 Azure 服务。

在将任何视频/图像上传到视频索引器之前，必须拥有该视频/图像的适当使用权限，包括根据法律要求，获得视频/图像中的个人（如果有）授予的，在视频索引器和 Azure 中使用、处理和存储其数据的所有必要许可。 某些司法辖区可能会对收集、在线处理和存储某些类别的数据（例如生物识别数据）施加特殊的法律要求。 在根据特殊法律要求使用视频索引器和 Azure 处理与存储任何数据之前，必须确保符合可能适用于你的任何法律要求。

如需了解视频索引器中的合规性、隐私性和安全性，请访问 Microsoft [信任中心](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)。 若要了解 Microsoft 的隐私义务、数据处理和保留惯例，包括如何删除数据，请查看 Microsoft 的[隐私声明](https://privacy.microsoft.com/PrivacyStatement)、[联机服务条款](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST") 和[数据处理附录](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA")。 使用视频索引器，即表示你同意遵守 OST、DPA 和隐私声明。

## <a name="what-can-i-do-with-video-indexer"></a>我可以使用视频索引器进行哪些操作？

视频索引器的见解可应用于多个方案，其中包括：

* 深度搜索：使用从视频中提取的见解可增强整个视频库的搜索体验。 例如，对所说内容和人脸进行索引，可以实现在视频中查找特定瞬间的搜索体验，例如，查找视频中某个人说出某些话时刻，或者看到两个人出现在一起的时刻。 根据视频中的此类见解进行的搜索，适用于新闻机构、教育机构、广播公司、娱乐内容所有者、企业 LOB 应用。一般来说，它适用于拥有视频库、用户需要对照搜索的任何行业。
* 内容创建：根据视频索引器从你的内容中提取的见解，创建预告片、亮点片段、社交媒体内容或新闻剪辑。 人物和标签外观的关键帧、场景标记和时间戳使创建过程更顺畅和简单，并且并允许你访问要创建内容所需的视频部分。
* 辅助功能：无论你是想将内容提供给残障人士使用，还是要使用不同的语言将内容分发到不同地区，你都可以使用视频索引器提供多种语言的转录和翻译。
* 盈利：视频索引器有助于提高视频的价值。 例如，依赖于广告收入（新闻媒体、社交媒体等）的行业，可以将提取的见解用作附加信号，向广告服务器投放相关广告。
* 内容审核：使用文本和视觉内容审核模型可保护用户远离不当内容，并验证发布的内容是否与组织的价值观相符。 你可以自动阻止某些视频，或向用户发出有关这些内容的警报。
* *建议*：视频见解可以通过向用户重点显示相关视频瞬间来提高用户的参与度。 通过使用其他元数据标记每个视频，可以为用户推荐最相关的视频，并重点显示符合用户需求的视频的部分内容。

## <a name="features"></a>功能

下表显示了可使用视频索引器视频和音频模型从视频中检索的见解：

### <a name="video-insights"></a>视频见解

* **人脸检测**：检测和分组视频中显示的人脸。
* **名人识别**：视频索引器可自动识别超过 100 万个名人，如世界各国/地区领导人、男演员、女演员、运动员、研究人员、商业和科技领袖。 有关这些名人的数据也可以在各种网站（IMDB、维基百科等）上找到。
* **基于帐户的人脸识别**：视频索引器针对特定帐户训练模型。 然后，根据已训练的模型识别视频中的人脸。 有关详细信息，请参阅[从视频索引器网站自定义人员模型](customize-person-model-with-website.md)和[使用视频索引器 API 自定义人员模型](customize-person-model-with-api.md)。
* **人脸缩略图提取**（“最佳人脸”）：在每组人脸中自动识别捕获的最佳人脸（基于图像质量、大小和正面位置），并将其提取为图像资产。
* **视觉文本识别** (OCR)：提取视频中显示的可视文本。
* **视觉内容审核**：检测成人和/或挑逗性视觉对象。
* **标签识别**：识别显示的视觉对象和动作。
* 场景分割：根据视觉提示确定视频中的场景何时发生了变化。一个场景描绘的是一个单一事件，由一系列在语义上相关的连续镜头组成。
* **镜头检测**：根据视觉提示确定视频中的镜头何时发生了变化。镜头是指从同一台运动摄像机拍摄的一系列画面。 有关详细信息，请参阅[场景、镜头和关键帧](scenes-shots-keyframes.md)。
* **黑帧检测**：识别视频中的黑帧。
* **关键帧提取**：检测视频中稳定的关键帧。
* 滚动字幕：识别电视节目和电影末尾的滚动字幕的开头和结尾。
* 动画字符检测（预览版）：通过与[认知服务自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)集成来检测、分组和识别动画内容中的字符。 有关详细信息，请参阅[动画字符检测](animated-characters-recognition.md)。
* 编辑镜头类型检测：基于镜头类型（如广角镜头、中景镜头、特写、极致特写、双人镜头、多人、室外和室内等）进行标记。 有关详细信息，请参阅[编辑镜头类型检测](scenes-shots-keyframes.md#editorial-shot-type-detection)。

### <a name="audio-insights"></a>音频见解

* **音频听录**：将语音转换为 12 种语言的文本并允许扩展。 支持的语言包括英语、西班牙语、法语、德语、意大利语、中文（普通话）、日语、阿拉伯语、俄语、葡萄牙语、印地语和韩语。
* **自动语言检测**：自动识别主导性的讲述语言。 支持的语言包括英语、西班牙语、法语、德语、意大利语、中文（普通话）、日语、俄语和葡萄牙语。 如果无法准确识别语言，视频索引器会假定所讲语言为英语。 有关详细信息，请参阅[语言识别模型](language-identification-model.md)。
* **多语言语音识别和脚本**：自动标识音频中不同段的口述语言。 它会发送要转录的媒体文件的每个片段，然后将转录合并成一个完成的转录。 有关详细信息，请参阅[自动识别和转录多语言内容](multi-language-identification-transcription.md)。
* **隐藏式字幕**：以三种格式创建隐藏式字幕：VTT、TTML、SRT。
* **双通道处理**：自动检测单独的脚本并合并到单个时间轴。
* **噪声消减**：清理电话音频或有噪音的录制内容（基于 Skype 滤波器）。
* **脚本自定义** (CRIS)：训练自定义语音转文本模型，以创建行业特定的脚本。 有关详细信息，请参阅[从视频索引器网站自定义语言模型](customize-language-model-with-website.md)和[使用视频索引器 API 自定义语言模型](customize-language-model-with-api.md)。
* **说话人枚举**：映射和了解哪个说话人在何时说了哪些话。 可以在单个音频文件中检测十六个扬声器。
* **说话人统计信息**：提供说话人发言比率的统计数据。
* **文本内容审核**：检测音频脚本中的显式文本。
* **音效**：识别击掌、讲话和静音等音效。
* **情感检测**：基于语音（所说的内容）和语调（说话的方式）识别情感。 情感可能是：快乐、悲伤、愤怒或恐惧。
* **翻译**：将音频脚本翻译成 54 种不同的语言。

### <a name="audio-and-video-insights-multi-channels"></a>音频和视频见解（多通道）

通过一个通道编制索引时，这些模型的部分结果将可用。

* **关键字提取**：从语音和视觉文本中提取关键字。
* 命名实体提取：通过自然语言处理 (NLP) 从语音和视觉文本中提取品牌、位置和人员。
* **主题推理**：根据脚本推理主要主题。 包括第二级 IPTC 分类。
* **项目**：提取每个模型的丰富的“下一种详细程度”项目。
* **情绪分析**：在语音和视觉文本中识别积极、消极和中性情绪。

## <a name="how-can-i-get-started-with-video-indexer"></a>如何上手使用视频索引器？

可以通过以下三种方式访问视频索引器功能：

* 视频索引器门户：一种简单易用的解决方案，可让你评估产品、管理帐户以及自定义模型。

    有关该门户的详细信息，请参阅[视频索引器入门网站](video-indexer-get-started.md)。  

* API 集成：视频索引器的所有功能都通过 REST API 获得，可让你将解决方案集成到应用和基础结构中。

    如果是开发人员，请参阅 [使用视频索引器 REST API](video-indexer-use-apis.md)。

* 可嵌入的小组件：可用于将视频索引器见解、播放器和编辑器体验嵌入应用。

    有关详细信息，请参阅 [将视觉小组件嵌入应用程序](video-indexer-embed-widgets.md)。

如果使用的是网站，则见解会添加为元数据，并在门户中可见。 如果使用的是 API，则见解可用作 JSON 文件。

## <a name="supported-browsers"></a>支持的浏览器

以下列表显示了可用于视频索引器网站和嵌入小组件的应用的受支持的浏览器。 此列表还显示支持的最低浏览器版本：

- Edge，版本：16
- Firefox，版本：54
- Chrome，版本：58
- Safari，版本：11
- Opera，版本：44
- Opera Mobile，版本：59
- Android 浏览器，版本：81
- Samsung 浏览器，版本：7
- 适用于 Android 的 Chrome，版本：87
- 适用于 Android 的 Firefox，版本：83

## <a name="next-steps"></a>后续步骤

你已做好视频索引器入门准备。 有关详细信息，请参阅以下文章：

- [视频索引器入门网站](video-indexer-get-started.md)。
- [使用视频索引器 REST API 处理内容](video-indexer-use-apis.md)。
- [将视觉小组件嵌入应用程序](video-indexer-embed-widgets.md)。
