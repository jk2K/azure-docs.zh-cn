---
title: 通过 VM 设置备份 Azure VM
description: 本文介绍如何使用 Azure 备份服务备份单个 Azure VM 或多个 Azure VM。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667524"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>通过 VM 设置备份 Azure VM

本文说明如何使用 [Azure 备份](backup-overview.md)服务备份 Azure VM。 可以使用多个方法备份 Azure VM：

- 单个 Azure VM：本文中的说明介绍如何直接通过 VM 设置备份 Azure VM。
- 多个 Azure VM：可以设置一个恢复服务保管库，然后为多个 Azure VM 配置备份。 按照[此文](backup-azure-arm-vms-prepare.md)中针对此方案的说明操作。

## <a name="before-you-start"></a>开始之前

1. [了解](backup-architecture.md#how-does-azure-backup-work)备份工作原理，并[验证](backup-support-matrix.md#azure-vm-backup-support)支持要求。
2. [概要了解](backup-azure-vms-introduction.md) Azure VM 备份。

### <a name="azure-vm-agent-installation"></a>Azure VM 代理安装

若要备份 Azure Vm，Azure 备份会在计算机上运行的 VM 代理上安装扩展。 如果 VM 是从 Azure Marketplace 映像创建的，则代理将运行。 在某些情况下，例如，如果创建自定义 VM，或从本地迁移计算机，则可能需要手动安装代理。

- 如果需要手动安装 VM 代理，请按 [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) VM 的说明操作。
- 在安装代理后启用备份时，Azure 备份会将备份扩展安装到代理。 它可以在没有用户干预的情况下更新和修补扩展。

## <a name="back-up-from-azure-vm-settings"></a>通过 Azure VM 设置进行备份

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 选择 " **所有服务** "，并在筛选器中键入 " **虚拟机**"，然后选择 " **虚拟机**"。
3. 从 Vm 列表中，选择要备份的 VM。
4. 在 "VM" 菜单上，选择 " **备份**"。
5. 在“恢复服务保管库”中执行以下操作：****
   - 如果已有保管库，请选择 " **选择现有**"，然后选择保管库。
   - 如果没有保管库，请选择 " **新建**"。 指定保管库的名称。 它在 VM 所在的区域和资源组中创建。 直接通过 VM 设置启用备份时，不能修改这些设置。

        ![启用备份向导](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. 在 " **选择备份策略**" 中，执行下列操作之一：

   - 保留默认策略。 这样会每天一次在指定的时间备份 VM，并在保管库中保留备份 30 天。
   - 选择现有的备份策略（如果有）。
   - 创建一项新策略，然后定义策略设置。  

       ![选择备份策略](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. 选择“启用备份”。 这样会将备份策略与 VM 相关联。

    ![“启用备份”按钮](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. 可在门户通知中跟踪配置进度。
9. 作业完成后，在 "VM" 菜单中选择 " **备份**"。 页面会显示 VM 的备份状态、有关恢复点的信息、正在运行的作业以及发出的警报。

   ![备份状态](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. 启用备份后，会运行初始备份。 可以立即启动初始备份，也可以等待它按备份计划启动。
    - 在初始备份完成之前，“上次备份状态”**** 显示为“警告(初始备份挂起)”****。
    - 若要查看下一个计划的备份将运行的时间，请选择备份策略名称。

## <a name="run-a-backup-immediately"></a>立即运行备份

1. 若要立即运行备份，请在 "VM" 菜单**Backup**中选择 "  >  **立即**备份"。

    ![运行备份](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. 现在，在 " **备份**" 中，使用 "日历" 控件选择在何时保留恢复点 > **"确定"**。

    ![备份保留日期](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. 门户通知会告知你备份作业已触发。 若要监视备份进度，请选择 " **查看所有作业**"。

## <a name="back-up-from-the-recovery-services-vault"></a>从恢复服务保管库备份

按照 [本文](backup-azure-arm-vms-prepare.md) 中的说明操作，通过设置 Azure 备份恢复服务保管库，并在保管库中启用备份来启用 azure vm 备份。

## <a name="next-steps"></a>后续步骤

- 如果在学习本文中的任何过程时遇到困难，请参阅[故障排除指南](backup-azure-vms-troubleshoot.md)。
- [了解](backup-azure-manage-vms.md)备份管理。
