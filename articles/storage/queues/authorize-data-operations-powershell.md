---
title: 使用 Azure AD 凭据运行 PowerShell 命令以访问队列数据
titleSuffix: Azure Storage
description: PowerShell 支持 Azure AD 凭据登录以在 Azure 队列存储数据上运行命令。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 安全主体的 Azure 角色。
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 09/14/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: bf2696d329f852741c42219219600dc773090623
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590709"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>使用 Azure AD 凭据运行 PowerShell 命令以访问队列数据

Azure 存储为 PowerShell 提供扩展，使用户可使用 Azure Active Directory (Azure AD) 凭据登录并运行脚本命令。 使用 Azure AD 凭据登录 PowerShell 时，会返回 OAuth 2.0 访问令牌。 PowerShell 会自动使用该令牌对队列存储的后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可通过 Azure 基于角色的访问控制 (Azure RBAC) 向 Azure AD 安全主体分配对队列数据的权限。 有关 Azure 存储中 Azure 角色的详细信息，请参阅[通过 Azure RBAC 管理 Azure 存储数据访问权限](../common/storage-auth-aad-rbac-portal.md)。

## <a name="supported-operations"></a>支持的操作

Azure 存储扩展支持针对队列数据的操作。 可调用的操作取决于向 Azure AD 安全主体授予的权限，此安全主体用于登录 PowerShell。 通过 Azure RBAC 分配对队列的权限。 例如，如果为你分配了“队列数据读取者”角色，你可以运行从队列读取数据的脚本命令。 如果为你分配了“队列数据参与者”角色，你可以运行脚本命令来读取、写入或删除队列或其中所含数据。

若要详细了解针对队列的每个 Azure 存储操作所需的权限，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 凭据调用 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 登录并使用 Azure AD 凭据针对 Azure 存储运行后续操作，请创建一个存储上下文用于引用存储帐户，并包含 `-UseConnectedAccount` 参数。

以下示例演示如何在 Azure PowerShell 中使用 Azure AD 凭据在新的存储帐户中创建一个队列。 请务必将尖括号中的占位符值替换为你自己的值：

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登录到 Azure 帐户。

    ```powershell
    Connect-AzAccount
    ```

    若要详细了解如何使用 PowerShell 登录 Azure，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)。

1. 调用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 调用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建存储帐户。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 调用 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 获取用于指定新存储帐户的存储帐户上下文。 对存储帐户执行操作时，可以引用上下文而不是重复传入凭据。 包含 `-UseConnectedAccount` 参数，以使用 Azure AD 凭据调用任何后续数据操作：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 创建队列之前，请为自己分配[存储队列数据参与者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)角色。 即使你是帐户所有者，也需要显式权限才能针对存储帐户执行数据操作。 有关分配 Azure 角色的详细信息，请参阅[使用 Azure 门户分配用于访问 blob 和队列数据的 Azure 角色](../common/storage-auth-aad-rbac-portal.md)。

    > [!IMPORTANT]
    > 传播 Azure 角色分配可能需要花费几分钟时间。

1. 通过调用 [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) 来创建队列。 由于此调用使用在前面步骤中创建的上下文，因此将使用你的 Azure AD 凭据创建队列。

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 为 blob 和队列数据分配用于访问的 Azure 角色](../common/storage-auth-aad-rbac-powershell.md)
- [使用 Azure 资源托管标识授予对 Blob 和队列数据的访问权限](../common/storage-auth-aad-msi.md)
