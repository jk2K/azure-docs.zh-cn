---
title: 文本分析 API v2 迁移指南
titleSuffix: Azure Cognitive Services
description: 了解如何移动你的应用程序以使用文本分析 API 版本 3。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 0faa7a6f5a3d2efc8bbef11308b308e3305a00d5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096315"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>迁移到文本分析 API 版本 3.x

如果你使用的是文本分析 API 版本 2.1，则本文可帮助你升级应用程序以使用版本 3.x。 版本 3.0 已正式发布并引入了新功能，例如扩展的[命名实体识别 (NER)](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) 和[模型版本控制](concepts/model-versioning.md)。 此外还提供了预览版的 v3.1 (v3.1-preview.x)，其中添加了[观点挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)等功能。 v2 中使用的模型不会收到将来的更新。 

## <a name="sentiment-analysis"></a>[情绪分析](#tab/sentiment-analysis)

### <a name="feature-changes"></a>功能更改 

对于发送到 API 的每个文档，版本 2.1 中的情绪分析会返回 0 到 1 之间的一个情绪分数，分数越接近 1 表示情绪越积极。 版本 3 改为返回句子和文档整体的情绪标签（例如“积极”或“消极”），以及它们的相关置信度分数。 

### <a name="steps-to-migrate"></a>迁移步骤

#### <a name="rest-api"></a>REST API

如果应用程序使用 REST API，请将其请求终结点更新为用于情绪分析的 v3 终结点。 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`。 你还需要更新应用程序，以使用 [API 的响应](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)中返回的情绪标签。 

有关 JSON 响应的示例，请参阅参考文档。
* [版本 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [版本 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [版本 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>客户端库

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER 和实体链接](#tab/named-entity-recognition)

### <a name="feature-changes"></a>功能更改

在 2.1 版中，文本分析 API 为命名实体识别 (NER) 和实体链接使用一个终结点。 版本 3 提供了扩展的命名实体检测，对 NER 和实体链接请求使用不同的终结点。 从第 3.1-NER 开始，还可以检测个人 `pii` 和健康 `phi` 信息。 

### <a name="steps-to-migrate"></a>迁移步骤

#### <a name="rest-api"></a>REST API

如果应用程序使用 REST API，请将其请求终结点更新为用于 NER 和/或实体链接的 v3 终结点。

实体链接
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

你还需要更新应用程序，以使用 [API 的响应](how-tos/text-analytics-how-to-entity-linking.md#view-results)中返回的[实体类别](named-entity-types.md)。

有关 JSON 响应的示例，请参阅参考文档。
* [版本 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [版本 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [版本 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>客户端库

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>版本2.1 实体类别

下表列出了为 NER 2.1 返回的实体类别。

| 类别   | 说明                          |
|------------|--------------------------------------|
| 人员   |   人员姓名。  |
|位置    | 自然地标和人造地标、结构、地理特征和地缘政治实体 |
|组织 | 公司、政治团体、乐队、体育俱乐部、政府机构和公共组织。 民族和宗教不包括在此实体类型中。 |
| PhoneNumber | 电话号码（仅限美国和欧洲电话号码）。 |
| 电子邮件 | 电子邮件地址。 |
| URL | 指向网站的 URL。 |
| IP | 网络 IP 地址。 |
| DateTime | 某天的日期和时间。| 
| Date | 日历日期。 |
| 时间 | 一天中的时间 |
| DateRange | 日期范围。 |
| TimeRange | 时间范围。 |
| 持续时间 | 持续时间。 |
| 设置 | 集，重复的时间。 |
| 数量 | 数字和数量。 |
| Number | 数字。 |
| 百分比 | 百分比。|
| Ordinal | 序号。 |
| Age | 年龄。 |
| 货币 | 货币。 |
| 维度 | 维度和度量。 |
| 温度 | 温度。 |

## <a name="language-detection"></a>[语言检测](#tab/language-detection)

### <a name="feature-changes"></a>功能更改 

除了终结点版本之外，v3 中的语言检测功能没有更改，但是 JSON 响应会包含 `ConfidenceScore` 而非 `score`。 V3 在输出中也仅返回一种语言。 

### <a name="steps-to-migrate"></a>迁移步骤

#### <a name="rest-api"></a>REST API

如果应用程序使用 REST API，请将其请求终结点更新为用于语言检测的 v3 终结点。 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`。 你还需要更新应用程序，以使用 [API 的响应](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)中的 `ConfidenceScore` 而非 `score`。 

有关 JSON 响应的示例，请参阅参考文档。
* [版本 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [版本 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [版本 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>客户端库

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[关键短语提取](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>功能更改 

除了终结点版本之外，v3 中的关键短语提取功能没有更改。

### <a name="steps-to-migrate"></a>迁移步骤

#### <a name="rest-api"></a>REST API

如果应用程序使用 REST API，请将其请求终结点更新为用于关键短语提取的 v3 终结点。 例如：`https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

有关 JSON 响应的示例，请参阅参考文档。
* [版本 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [版本 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [版本 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>客户端库

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>另请参阅

* [什么是文本分析 API](overview.md)
* [语言支持](language-support.md)
* [模型版本控制](concepts/model-versioning.md)
