---
title: 搜索 JSON Blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 认知搜索 Blob 索引器抓取 Azure JSON Blob 以获取文本内容。 索引器可自动为所选数据源（如 Azure Blob 存储）引入数据。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259044"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>如何使用 Azure 认知搜索中的 Blob 索引器为 JSON Blob 编制索引

本文介绍如何为包含 JSON 文档的 blob [配置 blob 索引器](search-howto-indexing-azure-blob-storage.md) 。 Azure Blob 存储中的 JSON blob 通常采用以下形式之一：

+ 单个 JSON 文档
+ 一个 JSON 文档，其中包含格式正确的 JSON 元素的数组
+ 一个 JSON 文档，其中包含由换行符分隔的多个实体

Blob 索引器提供一个 **`parsingMode`** 参数，用于根据结构分析模式优化搜索文档的输出，包括以下选项：

| parsingMode | JSON 文档 | 描述 |
|--------------|-------------|--------------|
| **`json`** | 每个 Blob 各有一个 |  (默认) 会将 JSON blob 分析为单个文本块。 每个 JSON blob 都将成为单个搜索文档。 |
| **`jsonArray`** | 每个 Blob 有多个 | 分析 blob 中的 JSON 数组，其中数组的每个元素都将成为单独的搜索文档。  |
| **`jsonLines`** | 每个 Blob 有多个 | 分析包含多个 JSON 实体的 blob (也是数组) ，单独的元素由换行符分隔。 索引器在每个新行后开始新的搜索文档。 |

对于 **`jsonArray`** 和 **`jsonLines`** ，你应查看为 [一个 blob 编制索引以生成多个搜索文档](search-howto-index-one-to-many-blobs.md) ，以了解 blob 索引器如何处理从同一 blob 生成的多个搜索文档的文档键的歧义。

在索引器定义中，可以选择设置 [字段映射](search-indexer-field-mappings.md) ，以选择要使用源 JSON 文档的哪些属性来填充目标搜索索引。 例如，在使用 **`jsonArray`** 分析模式时，如果该数组作为较低级别的属性存在，则可以设置一个 **`document root`** 属性，指示数组在 blob 中的放置位置。

以下部分更详细地介绍了每种模式。 如果不熟悉索引器客户端和概念，请参阅 [创建搜索索引器](search-howto-create-indexers.md)。 还应熟悉 [基本 blob 索引器配置](search-howto-indexing-azure-blob-storage.md)的详细信息，这在此不重复。

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>为每个 blob (一个 JSON 文档编制索引) 

默认情况下，blob 索引器将 JSON blob 分析为单个文本块，为容器中的每个 blob 提供一个搜索文档。 如果 JSON 是结构化的，搜索文档可以反映该结构，单独的元素表示为单个字段。 例如，假设 Azure Blob 存储中包含以下 JSON 文档：

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Blob 索引器将 JSON 文档解析为单个搜索文档，通过将源中的 "text"、"datePublished" 和 "tags" 与具有相同名称和类型化目标索引字段的源匹配来加载索引。 假设某个索引包含“text”、“datePublished”和“tags”字段，则请求不需要存在字段映射，Blob 索引器就能推断正确的映射。

尽管默认行为是每个 JSON blob 一个搜索文档，但设置 "JSON" 分析模式会更改内容的内部字段映射，并将中 `content` 的字段升级到搜索索引中的实际字段。 分析模式的示例索引器定义如下 **`json`** 所示：

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> 与所有索引器一样，如果字段不能明确匹配，则应显式指定单独的 [字段映射](search-indexer-field-mappings.md) ，除非使用了可用于 blob 内容和元数据的隐式字段映射，如 [基本 blob 索引器配置](search-howto-indexing-azure-blob-storage.md)中所述。

### <a name="json-example-single-hotel-json-files"></a> (单宾馆 JSON 文件的 json 示例) 

GitHub 上的 [酒店 JSON 文档数据集](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) 对测试 JSON 分析非常有用，其中每个 blob 表示一个结构化的 json 文件。 您可以将数据文件上传到 Blob 存储，然后使用 **导入数据** 向导快速评估如何将此内容解析为单个搜索文档。 

数据集由五个 blob 组成，每个 blob 都包含一个地址集合和一个房间集合的酒店文档。 Blob 索引器检测两个集合，并反映索引架构中的输入文档的结构。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>分析 JSON 数组

或者，可以使用 JSON 数组选项。 当 blob 包含格式正确的 JSON 对象的数组，并且你希望每个元素成为单独的搜索文档时，此选项很有用。 使用 **`jsonArrays`** 时，以下 JSON blob 生成三个单独的文档，每个文档都有 `"id"` 和个 `"text"` 字段。  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

**`parameters`** 索引器的属性包含分析模式值。 对于 JSON 数组，索引器定义应如以下示例所示。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays 示例 (临床试验示例数据) 

GitHub 上的 [临床试验 JSON 数据集](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) 对于测试 JSON 数组分析非常有用。 您可以将数据文件上传到 Blob 存储，然后使用 **导入数据** 向导快速评估如何将此内容解析为单个搜索文档。 

数据集包含八个 blob，每个 blob 包含一个实体的 JSON 数组，共100个实体。 实体与填充哪些字段不同，但最终结果是所有 blob 中的每个实体的每个实体一个搜索文档。

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>解析嵌套的 JSON 数组

对于具有嵌套元素的 JSON 数组，可以指定 **`documentRoot`** 来指示多级别结构。 例如，如果 blob 如下所示：

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

使用此配置可为 `level2` 属性中包含的数组编制索引：

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>分析由换行符分隔的 JSON 实体

如果你的 blob 包含由换行符分隔的多个 JSON 实体，并且你希望每个元素成为单独的搜索文档，请使用 **`jsonLines`** 。

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

对于 JSON 行，索引器定义应如以下示例所示。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines 示例 (caselaw 示例数据) 

GitHub 上的 [CASELAW JSON 数据设置](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) 有助于测试 JSON 新行分析。 与其他示例一样，你可以将此数据上传到 Blob 存储，并使用 **导入数据** 向导快速评估分析模式对各个 blob 的影响。

数据集由一个 blob 组成，其中包含10个由新行分隔的 JSON 实体，其中每个实体描述单个合法事例。 最终结果为每个实体一个搜索文档。

## <a name="map-json-fields-to-search-fields"></a>将 JSON 字段映射到搜索字段

如果字段名称和类型不相同，则可以使用字段映射将源字段与目标字段相关联。 但字段映射还可用于匹配 JSON 文档的各个部分，并将它们 "提升" 到搜索文档的顶级字段。

下面的示例演示了这种情况。 有关一般字段映射的详细信息，请参阅 [字段映射](search-indexer-field-mappings.md)。

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

假设某个搜索索引包含以下字段：`Edm.String` 类型的 `text`、`Edm.DateTimeOffset` 类型的 `date`，以及 `Collection(Edm.String)` 类型的 `tags`。 请注意源中“datePublished”与索引中 `date` 字段之间的差异。 要将 JSON 映射到所需形状，请使用以下字段映射：

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

使用 [JSON 指针](https://tools.ietf.org/html/rfc6901) 表示法指定源字段。 以正斜杠开头引用 JSON 文档的根，并通过使用正斜杠分隔的路径选取所需属性（任意层级的嵌套）。

还可以通过使用从零开始的索引来引用个别数组元素。 例如，若要选取上述示例中“tags”数组的第一个元素，请使用如下所示的字段映射：

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> 如果 **`sourceFieldName`** 引用 JSON blob 中不存在的属性，则会跳过该映射，而不会出现错误。 此行为允许对具有不同架构的 JSON blob 继续进行索引 (这是一个常见用例) 。 由于没有验证检查，因此请仔细检查映射中的录入，以确保不会因为错误的原因而丢失文档。
>

## <a name="next-steps"></a>后续步骤

+ [配置 blob 索引器](search-howto-indexing-azure-blob-storage.md)
+ [定义字段映射](search-indexer-field-mappings.md)
+ [索引器概述](search-indexer-overview.md)
+ [如何使用 blob 索引器为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
+ [教程：在 Azure Blob 存储中搜索半结构化数据](search-semi-structured-data.md)