---
title: 从 Azure Blob 和 Azure Data Lake Storage 共享和接收数据
description: 了解如何从 Azure Blob 存储和 Azure Data Lake Storage 共享和接收数据。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 242980ac1b89345ed9d8ff903e65129cff3cb917
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964093"
---
# <a name="share-and-receive-data-from-azure-blob-storage-and-azure-data-lake-storage"></a>从 Azure Blob 和 Azure Data Lake Storage 共享和接收数据

[!INCLUDE[appliesto-storage](includes/appliesto-storage.md)]

Azure 数据共享支持从存储帐户进行基于快照的共享。 本文介绍如何从 Azure Blob 存储、Azure Data Lake Storage Gen1 和 Azure Data Lake Storage Gen2 中共享和接收数据。

Azure 数据共享支持从 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共享文件、文件夹和文件系统。 它还支持从 Azure Blob 存储共享 blob、文件夹和容器。 目前仅支持块 blob。 可以通过 Azure Data Lake Gen2 或 Azure Blob 存储来接收从这些源共享的数据。

在基于快照的共享中共享文件系统、容器或文件夹时，数据使用者可以选择创建共享数据的完整副本。 或者，他们可以使用增量快照功能只复制新的或更新的文件。 增量快照功能基于文件的上次修改时间。 

快照期间将覆盖具有相同名称的现有文件。 不会在目标上删除从源中删除的文件。 源中的空子文件夹不会复制到目标。 
## <a name="share-data"></a>共享数据

使用以下部分中的信息通过 Azure 数据共享来共享数据。 
### <a name="prerequisites-to-share-data"></a>共享数据的先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 查找接收方的 Azure 登录电子邮件地址。 收件人的电子邮件别名不能用于你的目的。
* 如果源 Azure 数据存储位于与创建数据共享资源的订阅不同的 Azure 订阅中，请在 Azure 数据存储所在的订阅中注册 [DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration) 。 

### <a name="prerequisites-for-the-source-storage-account"></a>源存储帐户的先决条件

* 一个 Azure 存储帐户。 如果还没有帐户，请 [创建一个](../storage/common/storage-account-create.md)。
* 写入存储帐户的权限。 写入权限位于 *storageAccounts/写入*。 它属于参与者角色。
* 向存储帐户添加角色分配的权限。 此权限在 *Microsoft. Authorization/role 赋值/write* 中。 它是 "所有者" 角色的一部分。 

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="create-a-data-share-account"></a>创建数据共享帐户

在 Azure 资源组中创建 Azure Data Share 资源。

1. 在门户的左上角，打开菜单，然后选择 " **创建资源** (" 和 ") "。

1. 搜索“Data Share”。 

1. 选择 " **数据共享** " 和 " **创建**"。

1. 提供 Azure 数据共享资源的基本详细信息： 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 订阅 | 为数据共享帐户选择一个 Azure 订阅。|
    | 资源组 | *test-resource-group* | 使用现有资源组或创建资源组。 |
    | 位置 | *美国东部 2* | 选择 Data Share 帐户的区域。
    | 名称 | *datashareaccount* | 命名你的数据共享帐户。 |
    | | |

1. 选择 "查看" "**创建**"  >  以预配数据共享帐户。 预配新的数据共享帐户通常需大约2分钟。 

1. 部署完成后，选择“转到资源”。

### <a name="create-a-share"></a>创建共享

1. 请参阅 "数据共享 **概述** " 页。

   :::image type="content" source="./media/share-receive-data.png" alt-text="显示数据共享概述的屏幕截图。":::

1. 选择“开始共享数据”  。

1. 选择“创建”。   

1. 提供共享的详细信息。 指定名称、共享类型、共享内容说明以及使用条款（可选）。 

    ![显示数据共享详细信息的屏幕截图。](./media/enter-share-details.png "输入数据共享的详细信息。") 

1. 选择“继续”。 

1. 若要将数据集添加到共享中，请选择 " **添加数据集**"。 

    ![显示如何向共享添加数据集的屏幕截图。](./media/datasets.png "数据集。")

1. 选择要添加的数据集类型。 数据集类型的列表取决于你是在上一步中选择了基于快照的共享还是就地共享。 

    ![显示在何处选择数据集类型的屏幕截图。](./media/add-datasets.png "添加数据集。")    

1. 中转到要共享的对象。 然后选择 " **添加数据集**"。 

    ![显示如何选择要共享的对象的屏幕截图。](./media/select-datasets.png "选择数据集。")    

1. 在 " **收件人** " 选项卡上，通过选择 " **添加收件人**" 来添加你的数据使用者的电子邮件地址。 

    ![显示如何添加收件人电子邮件地址的屏幕截图。](./media/add-recipient.png "添加收件人。") 

1. 选择“继续”。 

1. 如果选择了快照共享类型，则可以设置快照计划来更新数据使用者的数据。 

    ![显示快照计划设置的屏幕截图。](./media/enable-snapshots.png "启用快照。") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续”。

1. 在 " **查看** " 和 "创建" 选项卡上，查看包内容、设置、收件人和同步设置。 然后选择“创建”。

现已创建 Azure 数据共享。 数据共享的接收方可以接受邀请。 

## <a name="receive-data"></a>接收数据

以下各节介绍如何接收共享数据。
### <a name="prerequisites-to-receive-data"></a>接收数据的先决条件
在接受数据共享邀请之前，请确保满足以下先决条件： 

* Azure 订阅。 如果没有订阅，请创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* 来自 Azure 的邀请。 电子邮件主题应为 "Azure 数据共享邀请 *\<yourdataprovider\@domain.com>* "。
* 中注册的 [DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration) ：
    * 要在其中创建数据共享资源的 Azure 订阅。
    * 目标 Azure 数据存储所在的 Azure 订阅。

### <a name="prerequisites-for-a-target-storage-account"></a>目标存储帐户的先决条件

* 一个 Azure 存储帐户。 如果还没有帐户，请 [创建一个帐户](../storage/common/storage-account-create.md)。 
* 写入存储帐户的权限。 此权限位于 *storageAccounts/写入*。 它属于参与者角色。 
* 向存储帐户添加角色分配的权限。 此分配在 *Microsoft. Authorization/role 赋值/write* 中。 它是 "所有者" 角色的一部分。  

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="open-an-invitation"></a>打开邀请

可以通过电子邮件或直接从 Azure 门户打开邀请。

1. 若要从电子邮件中打开邀请，请在收件箱中查看数据提供商提供的邀请。 Microsoft Azure 中的邀请的标题为 "Azure 数据共享邀请 *\<yourdataprovider\@domain.com>* "。 选择 " **查看邀请** " 以查看 Azure 中的邀请。 

   若要从 Azure 门户中打开邀请，请搜索 " *数据共享邀请*"。 将显示一个数据共享邀请列表。

   ![显示 Azure 门户中邀请列表的屏幕截图。](./media/invitations.png "邀请列表。") 

1. 选择要查看的共享。 

### <a name="accept-an-invitation"></a>接受邀请
1. 查看所有字段，包括 **使用条款**。 如果同意条款，请选中该复选框。 

   ![显示使用条款区域的屏幕截图。](./media/terms-of-use.png "使用条款。") 

1. 在 " **目标数据共享帐户**" 下，选择要在其中部署数据共享的订阅和资源组。 然后填写以下字段：

   * 如果没有数据共享帐户，请在 " **数据共享帐户** " 字段中选择 " **新建** "。 否则，请选择将接受数据共享的现有数据共享帐户。 

   * 在 " **接收的共享名** " 字段中，保留数据提供程序指定的默认值或为接收的共享指定新名称。 

1. 选择 " **接受并配置**"。 已创建共享订阅。 

   ![显示在何处接受配置选项的屏幕截图。](./media/accept-options.png "接受选项") 

    收到的共享出现在你的数据共享帐户中。 

    如果不想接受邀请，请选择 " **拒绝**"。 

### <a name="configure-a-received-share"></a>配置接收的共享
按照本部分中的步骤配置接收数据的位置。

1. 在 " **数据集** " 选项卡上，选中要为其分配目标的数据集旁边的复选框。 选择 " **映射到目标** " 以选择目标数据存储。 

   ![显示如何映射到目标的屏幕截图。](./media/dataset-map-target.png "映射到目标。") 

1. 为数据选择 "目标数据存储"。 目标数据存储区中与接收的数据中的文件具有相同路径和名称的文件将被覆盖。 

   ![显示目标存储帐户选择位置的屏幕截图。](./media/map-target.png "目标存储。") 

1. 对于基于快照的共享，如果数据提供程序使用快照计划定期更新数据，则可以从 " **快照计划** " 选项卡启用计划。选择快照计划旁边的复选框。 然后选择“启用”。

   ![显示如何启用快照计划的屏幕截图。](./media/enable-snapshot-schedule.png "启用快照计划。")

### <a name="trigger-a-snapshot"></a>触发快照
本部分中的步骤仅适用于基于快照的共享。

1. 可以通过 " **详细信息** " 选项卡触发快照。在选项卡上，选择 " **触发器快照**"。 可以选择触发数据的完整快照或增量快照。 如果是第一次从数据提供程序接收数据，请选择 " **完全复制**"。 

   ![显示触发器快照选择的屏幕截图。](./media/trigger-snapshot.png "触发器快照。") 

1. 当最后一次运行状态为 " *成功*" 时，请前往目标数据存储以查看接收的数据。 选择 " **数据集**"，然后选择 "目标路径" 链接。 

   ![显示使用者数据集映射的屏幕截图。](./media/consumer-datasets.png "使用者数据集映射。") 

### <a name="view-history"></a>查看历史记录
只能在基于快照的共享中查看快照历史记录。 若要查看历史记录，请打开 " **历史记录** " 选项卡。在这里，可以看到过去30天内生成的所有快照的历史记录。 

## <a name="next-steps"></a>后续步骤
已了解如何使用 Azure 数据共享服务从存储帐户共享和接收数据。 若要了解如何从其他数据源共享，请参阅 [支持的数据存储](supported-data-stores.md)。