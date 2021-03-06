---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Adobe Identity Management 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Adobe Identity Management 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fdca04c645e1bb956c8e9f294c702b639c8e2f74
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726362"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Adobe Identity Management 集成

本教程介绍如何将 Adobe Identity Management 与 Azure Active Directory (Azure AD) 集成。 将 Adobe Identity Management 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Adobe Identity Management。
* 让用户使用其 Azure AD 帐户自动登录到 Adobe Identity Management。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Adobe Identity Management 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Adobe Identity Management 支持“SP”发起的 SSO 

## <a name="adding-adobe-identity-management-from-the-gallery"></a>从库中添加 Adobe Identity Management

若要配置 Adobe Identity Management 与 Azure AD 的集成，需要从库中将 Adobe Identity Management 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Adobe Identity Management”   。
1. 从结果面板中选择“Adobe Identity Management”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>配置和测试 Adobe Identity Management 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Adobe Identity Management 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Adobe Identity Management 中的相关用户之间建立链接关系。

若要配置和测试 Adobe Identity Management 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Adobe Identity Management SSO](#configure-adobe-identity-management-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Adobe Identity Management 测试用户](#create-adobe-identity-management-test-user)** - 在 Adobe Identity Management 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Adobe Identity Management”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，键入 URL：`https://adobe.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`

    > [!NOTE]
    > 标识符非实际值。 请使用实际标识符更新此值。 请联系 [Adobe Identity Management 客户端支持团队](mailto:identity@adobe.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Adobe Identity Management”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 B. Simon 访问 Adobe Identity Management 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Adobe Identity Management”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-adobe-identity-management-sso"></a>配置 Adobe Identity Management SSO

1. 若要在 Adobe Identity Management 中自动执行配置，需要通过单击“安装扩展”来安装“我的应用安全登录”浏览器扩展 。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Adobe Identity Management”会将你定向到 Adobe Identity Management 应用程序。 在此处，提供管理员凭据以登录到 Adobe Identity Management。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-8。

    ![设置配置](common/setup-sso.png)

3. 如果要手动设置 Adobe Identity Management，请在另一个 Web 浏览器窗口中，以管理员身份登录到 Adobe Identity Management 公司站点。

4. 在“设置”选项卡中，单击“创建目录” 。

    ![Adobe Identity Management 设置](./media/adobe-identity-management-tutorial/settings.png)

5. 在文本框中指定目录名称并选择“联合 ID”，然后单击“下一步” 。

    ![Adobe Identity Management 创建目录](./media/adobe-identity-management-tutorial/create-directory.png)

6. 选择“其他 SAML 提供程序”并单击“下一步” 。
 
    ![Adobe Identity Management saml 提供程序](./media/adobe-identity-management-tutorial/saml-providers.png)

7. 单击“选择”以上传从 Azure 门户下载的“元数据 XML”文件 。

    ![Adobe Identity Management saml 配置](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. 单击“完成”。

### <a name="create-adobe-identity-management-test-user"></a>创建 Adobe Identity Management 测试用户

1. 转到“用户”选项卡，然后单击“添加用户” 。

    ![Adobe Identity Management 添加用户](./media/adobe-identity-management-tutorial/add-user.png)

2. 在“输入用户的电子邮件地址”文本框中，输入电子邮件地址 。

    ![Adobe Identity Management 保存用户](./media/adobe-identity-management-tutorial/save-user.png)

3. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Adobe Identity Management 登录 URL，可以从那里启动登录流。

* 直接转到 Adobe Identity Management 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的“Adobe Identity Management”磁贴时，会重定向到 Adobe Identity Management 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

配置 Adobe Identity Management 后，可以强制实施会话控制，实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。