---
title: 为 Azure Cosmos DB 创建表 API 表
description: 为 Azure Cosmos DB 创建表 API 表
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c6efc013241925085ecc9a5e3ead804ba3e82dee
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565953"
---
# <a name="create-an-azure-cosmos-table-api-account-and-table-using-azure-cli"></a>使用 Azure CLI 创建 Azure Cosmos 表 API 帐户和表
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.9.1 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/create.sh "Create an Azure Cosmos DB Table API account and table.")]

## <a name="clean-up-deployment"></a>清理部署

运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | 创建 Azure Cosmos DB 帐户。 |
| [az cosmosdb table create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | 创建 Azure Cosmos 表 API 表。 |
| [az group delete](/cli/azure/resource#az-resource-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB CLI 的详细信息，请参阅 [Azure Cosmos DB CLI 文档](/cli/azure/cosmosdb)。

可以在 [Azure Cosmos DB CLI GitHub 存储库](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到所有 Azure Cosmos DB CLI 脚本示例。
