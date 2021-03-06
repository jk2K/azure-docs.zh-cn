---
title: 从 Azure SQL 数据库和 Azure Synapse Analytics 共享和接收数据
description: 了解如何在 Azure SQL 数据库和 Azure Synapse Analytics 中共享和接收数据
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: bdbbf3e808e1dda0970aaf87d154ee79bea4dcb1
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964161"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>从 Azure SQL 数据库和 Azure Synapse Analytics 共享和接收数据

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure 数据共享支持基于快照的共享 Azure SQL 数据库和 Azure Synapse 分析。 本文介绍如何从这些源共享和接收数据。

Azure 数据共享支持从 Azure SQL 数据库和 Azure Synapse Analytics 中共享表和视图 (以前称为 Azure SQL DW) ，以及从 Azure Synapse Analytics (工作区) 专用 SQL 池共享表。 当前不支持从 Azure Synapse Analytics 共享 (工作区) 无服务器 SQL 池。 数据使用者可以选择接受将数据作为 csv 或 parquet 文件的 Azure Data Lake Storage Gen2 或 Azure Blob 存储，以及作为表的 Azure SQL 数据库和 Azure Synapse 分析。

在将数据接收到 Azure Data Lake Store Gen2 或 Azure Blob 存储时，如果已存在，则完全快照将覆盖目标文件的内容。
如果将数据接收到 SQL 表中，并且如果目标表尚不存在，则 Azure 数据共享会创建包含源架构的 SQL 表。 如果已存在具有相同名称的目标表，则将删除该目标表并使用最新的完整快照覆盖它。 当前不支持增量快照。

## <a name="share-data"></a>共享数据

### <a name="prerequisites-to-share-data"></a>共享数据的先决条件

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 收件人的 Azure 登录电子邮件地址（使用其电子邮件别名将无效）。
* 如果源 Azure 数据存储位于与你将用于创建 Data Share 资源的 Azure 订阅不同的 Azure 订阅中，请在 Azure 数据存储所在的订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。 

### <a name="prerequisites-for-sql-source"></a>SQL 源的先决条件
下面是从 SQL 源共享数据的先决条件列表。 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>从 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）共享的先决条件
可按照[分步演示](https://youtu.be/hIE-TjJD8Dc)配置先决条件。

* 包含要共享的表和视图的 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）。
* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。
* Data Share 资源托管标识访问数据库的权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中，导航到 SQL 服务器并将你自己设置为“Azure Active Directory 管理员”。
    1. 使用[查询编辑器](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory)或 SQL Server Management Studio 通过 Azure Active Directory 身份验证连接到 Azure SQL 数据库/数据仓库。 
    1. 执行以下脚本，以将 Data Share 资源托管标识添加为 db_datareader。 必须使用 Active Directory 而非 SQL Server 身份验证进行连接。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。  

* 具有“db_datareader”访问权限的 Azure SQL 数据库用户，可以浏览和选择要共享的表和/或视图。 

* SQL Server 防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到“SQL Server”。 从左侧导航栏中选择“防火墙和虚拟网络”。
    1. 对于“允许 Azure 服务和资源访问此服务器”，单击“是”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>从 Azure Synapse Analytics（工作区）SQL 池共享的先决条件

* 包含要共享的表的 Azure Synapse Analytics（工作区）专用 SQL 池。 目前不支持共享视图。 目前不支持从无服务器 SQL 池共享。
* 在 Synapse 工作区中写入 SQL 池的权限，该权限位于 Microsoft.Synapse/workspaces/sqlPools/write。 “参与者”角色有此权限。
* Data Share 资源托管标识访问 Synapse 工作区 SQL 池的权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“SQL Active Directory 管理员”，并将你自己设置为“Azure Active Directory 管理员”。
    1. 打开 Synapse Studio，从左侧导航栏中选择“管理”。 在“安全性”下选择“访问控制”。 为自己分配“SQL 管理员”或“工作区管理员”角色 。
    1. 在 Synapse Studio 中，从左侧导航栏中选择“开发”。 在 SQL 池中执行以下脚本，以将 Data Share 资源托管标识添加为 db_datareader。 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。  

* Synapse 工作区防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“防火墙”。
    1. 对于“允许 Azure 服务和资源访问此工作区”，单击“启用”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="create-a-data-share-account"></a>创建 Data Share 帐户

在 Azure 资源组中创建 Azure Data Share 资源。

1. 选择门户左上角的菜单按钮，然后选择“创建资源”(+)。

1. 搜索“Data Share”。 

1. 选择“Data Share”，然后选择“创建”  。

1. 根据以下信息填写 Azure Data Share 资源的基本详细信息。 

     **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 你的订阅 | 选择要用于 Data Share 帐户的 Azure 订阅。|
    | 资源组 | *test-resource-group* | 使用现有资源组或创建新资源组。 |
    | 位置 | *美国东部 2* | 选择 Data Share 帐户的区域。
    | 名称 | *datashareaccount* | 指定 Data Share 帐户的名称。 |
    | | |

1. 选择“审阅 + 创建”，然后选择“创建”来预配数据共享帐户 。 预配新的 Data Share 帐户通常需要大约 2 分钟或更少的时间。 

1. 部署完成后，选择“转到资源”。 

### <a name="create-a-share"></a>创建共享

1. 导航到 Data Share 概览页。

    ![共享数据](./media/share-receive-data.png "共享数据") 

1. 选择“开始共享数据”  。

1. 选择“创建”。   

1. 填写共享的详细信息。 指定名称、共享类型、共享内容说明以及使用条款（可选）。 

    ![EnterShareDetails](./media/enter-share-details.png "输入共享详细信息") 

1. 选择“继续”。

1. 若要向共享添加数据集，请选择“添加数据集”。 

    ![向共享添加数据集](./media/datasets.png "数据集")

1. 选择要添加的数据集类型。 你将会看到一个不同的数据集类型列表，具体取决于你在上一步中选择的共享类型（快照或就地）。 

    ![AddDatasets](./media/add-datasets.png "添加数据集")    

1. 选择 SQL server 或 Synapse 工作区，在出现提示时提供凭据，然后选择 " **下一步** " 以导航到要共享的对象，然后选择 "添加数据集"。 你可以从 Azure SQL 数据库和 Azure Synapse Analytics 中选择表和视图， (以前的 Azure SQL DW) 或 Azure Synapse Analytics (工作区中的表) 专用 SQL 池。 

    ![SelectDatasets](./media/select-datasets-sql.png "选择数据集")    

1. 在“接收者”选项卡中，选择“+ 添加接收者”，输入数据使用者的电子邮件地址。 电子邮件地址需要是收件人的 Azure 登录电子邮件。

    ![AddRecipients](./media/add-recipient.png "添加收件人") 

1. 选择“继续”。

1. 如果已选择了快照共享类型，则可以配置快照计划来向数据使用者提供数据更新。 

    ![EnableSnapshots](./media/enable-snapshots.png "启用快照") 

1. 选择开始时间和重复周期间隔。 

1. 选择“继续”。

1. 在“查看 + 创建”选项卡中查看包内容、设置、接收者和同步设置。 选择“创建”。

你的 Azure Data Share 现已创建，Data Share 的接收者现已准备好接受你的邀请。 

## <a name="receive-data"></a>接收数据

### <a name="prerequisites-to-receive-data"></a>接收数据的先决条件
在接受数据共享邀请之前，必须预配多个 Azure 资源，这些资源已在下面列出。 

确保在接受数据共享邀请之前已满足所有先决条件。 

* Azure 订阅：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Data Share 邀请：Microsoft Azure 发出的邀请，主题名为“来自 **<yourdataprovider@domain.com>** 的 Azure Data Share 邀请”。
* 在你将在其中创建 Data Share 资源的 Azure 订阅以及目标 Azure 数据存储所在的 Azure 订阅中注册 [Microsoft.DataShare 资源提供程序](concepts-roles-permissions.md#resource-provider-registration)。

### <a name="prerequisites-for-target-storage-account"></a>目标存储帐户的先决条件
如果选择将数据接收到 Azure 存储，以下是先决条件列表。

* 一个 Azure 存储帐户：如果没有，可以创建一个 [Azure 存储帐户](../storage/common/storage-account-create.md)。 
* 向存储帐户进行写入的权限，此权限位于 *Microsoft.Storage/storageAccounts/write* 中。 “参与者”角色有此权限。 
* 向存储帐户添加对数据共享资源的托管标识的角色分配的权限，该存储帐户位于 *Microsoft. 授权/角色分配/写入* 中。 “所有者”角色有此权限。  

### <a name="prerequisites-for-sql-target"></a>SQL 目标的先决条件
如果选择将数据接收到 Azure SQL 数据库和 Azure Synapse Analytics，请查看以下相关先决条件的列表。 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>将数据接收到 Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）的先决条件
可按照[分步演示](https://youtu.be/aeGISgK1xro)配置先决条件。

* Azure SQL 数据库或 Azure Synapse Analytics（以前称为 Azure SQL DW）。
* 向 SQL 服务器上的数据库进行写入的权限，此权限存在于 *Microsoft.Sql/servers/databases/write* 中。 “参与者”角色有此权限。 
* Data Share 资源托管标识用于访问 Azure SQL 数据库或 Azure Synapse Analytics 的权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中，导航到 SQL 服务器并将你自己设置为“Azure Active Directory 管理员”。
    1. 使用[查询编辑器](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory)或 SQL Server Management Studio 通过 Azure Active Directory 身份验证连接到 Azure SQL 数据库/数据仓库。 
    1. 执行以下脚本，以将 Data Share 托管标识添加为“db_datareader, db_datawriter, db_ddladmin”。 必须使用 Active Directory 而非 SQL Server 身份验证进行连接。 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。         

* SQL Server 防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中的 SQL Server 中，导航到“防火墙和虚拟网络” 
    1. 对于“允许 Azure 服务和资源访问此服务器”，单击“是”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>将数据接收到 Azure Synapse Analytics（工作区）SQL 池的先决条件

* Azure Synapse Analytics（工作区）专用 SQL 池。 目前不支持将数据接收到无服务器 SQL 池中。
* 在 Synapse 工作区中写入 SQL 池的权限，该权限位于 Microsoft.Synapse/workspaces/sqlPools/write。 “参与者”角色有此权限。
* Data Share 资源的托管标识用于访问 Synapse 工作区 SQL 池的权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“SQL Active Directory 管理员”，并将你自己设置为“Azure Active Directory 管理员”。
    1. 打开 Synapse Studio，从左侧导航栏中选择“管理”。 在“安全性”下选择“访问控制”。 为自己分配“SQL 管理员”或“工作区管理员”角色 。
    1. 在 Synapse Studio 中，从左侧导航栏中选择“开发”。 在 SQL 池中执行以下脚本，以将 Data Share 资源托管标识添加为“db_datareader, db_datawriter, db_ddladmin”。 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       请注意， *<share_acc_name>* 是 Data Share 资源的名称。 如果尚未创建 Data Share 资源，则可以稍后返回到该先决条件。  

* Synapse 工作区防火墙访问权限。 可以通过以下步骤完成此操作： 
    1. 在 Azure 门户中导航到 Synapse 工作区。 从左侧导航栏中选择“防火墙”。
    1. 对于“允许 Azure 服务和资源访问此工作区”，单击“启用”。
    1. 单击“+ 添加客户端 IP”。 客户端 IP 地址可能会更改。 下次从 Azure 门户共享 SQL 数据时，可能需要重复此过程。 还可以添加 IP 范围。
    1. 单击“ **保存**”。 

### <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

### <a name="open-invitation"></a>打开邀请

1. 可以从电子邮件或直接从 Azure 门户打开邀请。 

   若要从电子邮件打开邀请，请检查收件箱中是否有来自数据提供程序的邀请。 邀请来自 Microsoft Azure，标题为“来自 <yourdataprovider@domain.com> 的 Azure Data Share 邀请”。  单击“查看邀请”，以在 Azure 中查看邀请。  

   若要直接从 Azure 门户打开邀请，请在 Azure 门户中搜索“数据共享邀请”  。 这会转到“数据共享邀请”列表。

   ![邀请列表](./media/invitations.png "邀请列表") 

1. 选择要查看的共享。 

### <a name="accept-invitation"></a>接受邀请
1. 确保查看所有字段，包括“使用条款”。  如果同意使用条款，则必须勾选表示同意的框。 

   ![使用条款](./media/terms-of-use.png "使用条款") 

1. 在“目标 Data Share 帐户”下选择要在其中部署 Data Share 的订阅和资源组。  

   对于“Data Share 帐户”字段，如果没有现有的 Data Share 帐户，请选择“新建”。   否则，请选择一个现有的 Data Share 帐户来接受数据共享。 

   对于“已接收共享的名称”字段，可以保留数据提供者指定的默认值，也可以为已接收共享指定新名称  。 

   同意使用条款并指定用于管理已接收共享的数据共享帐户以后，请选择“接受并配置”。 将创建共享订阅。 

   ![接受选项](./media/accept-options.png "接受选项") 

   这会转到你的数据共享帐户中接收的共享。 

   如果不想接受邀请，请选择“拒绝”。 

### <a name="configure-received-share"></a>配置已接收共享
按照以下步骤配置要接收数据的位置。

1. 选择“数据集”选项卡。选中要为其分配目标位置的数据集旁边的复选框。 选择“+ 映射到目标”以选择目标数据存储。 

   ![映射到目标](./media/dataset-map-target.png "映射到目标") 

1. 选择要将数据置于其中的目标数据存储。 目标数据存储中具有相同路径和名称的任何数据文件或表将被覆盖。 

   ![目标存储帐户](./media/dataset-map-target-sql.png "目标数据存储") 

1. 对于基于快照的共享，如果数据提供程序已创建定期更新数据的快照计划，你还可以通过选择“快照计划”选项卡来启用快照计划。选中快照计划旁边的框，然后选择“+ 启用”。

   ![启用快照计划](./media/enable-snapshot-schedule.png "启用快照计划")

### <a name="trigger-a-snapshot"></a>触发快照
这些步骤仅适用于基于快照的共享。

1. 可以通过依次选择“详细信息”选项卡、“触发快照”来触发快照 。 在这里，可以触发数据的完整或增量快照。 如果是首次从数据提供者处接收数据，请选择完整副本。 对于 SQL 源，仅支持完整快照。

   ![触发快照](./media/trigger-snapshot.png "触发快照") 

1. 如果上次运行状态为“成功”，请转到目标数据存储来查看接收的数据。 选择“数据集”，然后单击“目标路径”中的链接。 

   ![使用者数据集](./media/consumer-datasets.png "使用者数据集映射") 

### <a name="view-history"></a>查看历史记录
此步骤仅适用于基于快照的共享。 若要查看快照历史记录，请选择“历史记录”选项卡。在这里可以找到包含过去 30 天生成的所有快照的历史记录。 

## <a name="supported-data-types"></a>支持的数据类型
当你从 SQL 源共享数据时，以下映射用于从 SQL Server 数据类型到 Azure 数据共享快照过程中的临时数据类型。 

| SQL Server 数据类型 | Azure 数据共享临时数据类型 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |布尔 |
| char |String, Char[] |
| date |DateTime |
| datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| 小数 |小数 |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| 图像 |Byte[] |
| int |Int32 |
| money |小数 |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |小数 |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |小数 |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |String |

>[!NOTE]
> 1. 对于映射到小数过渡类型的数据类型，当前快照最多支持精度为28。 如果数据需要的精度大于28，请考虑将转换为字符串。 
> 1.  如果要将数据从 Azure SQL 数据库共享到 Azure Synapse Analytics，并非所有数据类型都受支持。 有关详细信息，请参阅 [专用 SQL 池中的表数据类型](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) 。 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted 或动态数据掩码
Azure 数据共享目前不支持配置了 Always Encrypted 的 Azure SQL 数据库。 

对于包含动态数据掩码的源 SQL 表，数据将在收件人端显示为掩码。

## <a name="sql-snapshot-performance"></a>SQL 快照性能
SQL 快照性能受多种因素的影响。 我们始终建议你执行自己的性能测试。 下面是影响性能的一些示例因素。

* 硬件配置 (例如，源和目标 SQL 数据存储的 Vcore、内存、DWU) 。 
* 对源和目标数据存储区的并行访问。 如果要从同一 SQL 数据存储区共享多个表和视图，或者将多个表和视图接收到同一个 SQL 数据存储中，则性能会受到影响。   
* 源和目标数据存储区的位置。 

## <a name="troubleshoot-sql-snapshot-failure"></a>排查 SQL 快照故障
快照失败的最常见原因是数据共享没有源或目标数据存储的权限。 若要将数据共享权限授予源或目标 Azure SQL 数据库或 Azure Synapse Analytics (以前的 Azure SQL DW) ，必须在使用 Azure Active Directory authentication 连接到 SQL 数据库时运行提供的 SQL 脚本。 若要解决其他 SQL 快照故障，请参阅 [快照故障排除](data-share-troubleshoot.md#snapshots)。

## <a name="next-steps"></a>后续步骤
已了解如何使用 Azure 数据共享服务共享和接收来自 SQL 源的数据。 若要了解有关其他数据源的共享的详细信息，请继续阅读 [支持的数据存储](supported-data-stores.md)。