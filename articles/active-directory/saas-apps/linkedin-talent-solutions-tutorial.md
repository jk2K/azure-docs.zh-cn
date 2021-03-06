---
title: 教程：Azure Active Directory 单一登录 (SSO) 与领英人才解决方案的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与领英人才解决方案之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727322"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>教程：Azure Active Directory 单一登录 (SSO) 与领英人才解决方案的集成

本教程介绍如何将领英人才解决方案与 Azure Active Directory (Azure AD) 集成。 将领英人才解决方案与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问领英人才解决方案。
* 让用户使用其 Azure AD 帐户自动登录到领英人才解决方案。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 领英人才解决方案仪表板中的帐户中心的访问权限

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* 领英人才解决方案支持 SP 和 IDP 发起的 SSO
* 领英人才解决方案支持实时用户预配


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>从库中添加领英人才解决方案

若要配置领英人才解决方案与 Azure AD 的集成，需要从库中将领英人才解决方案添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中键入“领英人才解决方案”。 
1. 在结果面板中选择“领英人才解决方案”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>配置并测试领英人才解决方案的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试领英人才解决方案的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与领英人才解决方案中的相关用户之间建立链接关系。

若要配置并测试领英人才解决方案的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置领英人才解决方案 SSO](#configure-linkedin-talent-solutions-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建领英人才解决方案测试用户](#create-linkedin-talent-solutions-test-user)** - 在领英人才解决方案中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户中的“领英人才解决方案”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，如果有 **服务提供程序元数据文件**，请执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![image1](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![image2](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”  和“回复 URL”  值会自动填充在“基本 SAML 配置”部分：

    ![image3](common/idp-intiated.png)

    > [!Note]
    > 如果 **标识符** 和 **回复 URL** 值未自动填充，则请按要求手动填充这些值。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://www.linkedin.com/talent/`

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置领英人才解决方案”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问领英人才解决方案的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“领英人才解决方案”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-linkedin-talent-solutions-sso"></a>配置领英人才解决方案 SSO

1. 以管理员身份登录到领英人才解决方案网站。
1. 导航到“帐户中心”。 
1. 从导航栏中选择“设置”选项卡。

    ![设置页面](./media/linkedin-talent-solutions-tutorial/settings.png)

1. 展开“单一登录(SSO)”部分。 

1. 单击“下载”按钮以下载“元数据文件”，或单击“或单击此处以加载并复制窗体中的各个字段”链接以显示配置数据  。

    ![ 配置数据](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. 执行以下步骤以复制窗体中的各个字段。

    ![ 包含输入数据的配置](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. 复制“实体 ID”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“Azure AD 标识符”文本框中  。

    b. 复制“ACS URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中  。

    c. 将“SP X.509 证书(签名)”文本框的内容复制到记事本中，然后将其保存在计算机中。

1. 单击“上传 XML 文件”以上传从 Azure 门户复制的“联合元数据 XML”文件 。

    ![ 上传 XML 文件](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>配置领英人才解决方案测试用户

本部分将在领英人才解决方案中创建名为 Britta Simon 的用户。 领英人才解决方案支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果领英人才解决方案中尚不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到领英人才解决方案登录 URL，可在其中启动登录流。  

* 直接转到领英人才解决方案登录 URL，并在其中启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应会自动登录到为其设置了 SSO 的领英人才解决方案 

还可以使用 Microsoft“我的应用”在任何模式下测试此应用程序。 在“我的应用”中单击领英人才解决方案磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的领英人才解决方案。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置领英人才解决方案后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。