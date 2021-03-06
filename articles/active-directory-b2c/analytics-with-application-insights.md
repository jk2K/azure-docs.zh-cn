---
title: 使用 Application Insights 跟踪用户行为
titleSuffix: Azure AD B2C
description: 了解如何在 Application Insights 中启用 Azure AD B2C 用户旅程中的事件日志。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218547"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>使用 Application Insights 在 Azure Active Directory B2C 中跟踪用户行为

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) 支持使用提供给 Azure AD B2C 的检测密钥将事件数据直接发送到 [Application Insights](../azure-monitor/app/app-insights-overview.md)。 使用 Application Insights 技术配置文件，你可以获取用户旅程的详细自定义事件日志，以便执行以下操作：

* 洞察用户行为。
* 排查自己在开发或生产过程中的策略问题。
* 衡量性能。
* 通过 Application Insights 创建通知。

## <a name="overview"></a>概述

若要启用自定义事件日志，请添加 Application Insights 技术配置文件。 在技术配置文件中，定义要记录的 Application Insights 检测密钥、事件名称和声明。 然后，为了发布事件，此技术配置文件将作为业务流程步骤添加到[用户旅程](userjourneys.md)中。

使用 Application Insights 时，请注意以下事项：

- 在 Application Insights 中提供新日志之前，延迟时间通常不到五分钟。
- Azure AD B2C 允许选择要记录的声明。 不要包含包含个人数据的声明。
- 若要记录用户会话，可以使用关联 ID 统一事件。 
- 直接从 [用户旅程](userjourneys.md) 或 [sub 旅程](subjourneys.md)调用 Application Insights 技术配置文件。 不要使用 Application Insights 技术配置文件作为 [验证技术配置文件](validation-technical-profile.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

将 Azure AD B2C 与 Application Insights 配合使用时，只需创建资源并获取检测密钥。 有关信息，请参阅[创建 Application Insights 资源](../azure-monitor/app/create-new-resource.md)

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请通过选择顶部菜单中的“目录和订阅”筛选器，然后选择包含你的 Azure 订阅的目录，来确保你使用的是包含你的订阅的目录。 此租户不是 Azure AD B2C 租户。
3. 选择 Azure 门户左上角的“创建资源”，然后搜索并选择“Application Insights”。
4. 单击“创建”。
5. 输入此资源的名称。
6. 在“应用程序类型”下，选择“ASP.NET web 应用程序”。
7. 对于资源组，选择现有的组，或输入新组的名称。
8. 单击“创建”。
4. 创建 Application Insights 资源后，将其打开，展开“Essentials”并复制检测密钥。

![Application Insights 概览和检测密钥](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>定义声明

声明可在 Azure AD B2C 策略执行过程中提供数据的临时存储。 [声明架构](claimsschema.md)是发出声明的位置。

1. 打开策略的扩展文件， 例如，<em>`SocialAndLocalAccounts/``TrustFrameworkExtensions.xml`</em>。
1. 搜索 [BuildingBlocks](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 [ClaimsSchema](claimsschema.md) 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到 ClaimsSchema 元素。 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>新增技术配置文件

可以将技术配置文件视为自定义策略中的函数。 此表定义技术配置文件，这些文件用于打开会话和发布事件。 该解决方案使用 [技术配置文件包含](technicalprofiles.md#include-technical-profile) 方法。 其中技术配置文件包含其他技术配置文件来更改设置或添加新功能。

| 技术配置文件 | 任务 |
| ----------------- | -----|
| AppInsights-Common | 常用的一组配置文件。 包括、Application Insights 检测密钥、要记录的声明的集合，以及开发人员模式。 以下技术配置文件包括通用技术配置文件，并添加更多声明，如事件名称。 |
| AppInsights-SignInRequest | 在收到登录请求后记录包含一组声明的 `SignInRequest` 事件。 |
| AppInsights-UserSignUp | 当用户在注册/登录旅程中触发注册选项时记录 `UserSignUp` 事件。 |
| AppInsights-SignInComplete | 在成功完成了身份验证（已将令牌发送到信赖方应用程序）时记录 `SignInComplete` 事件。 |

将配置文件添加到初学者包中的 TrustFrameworkExtensions.xml 文件。 将以下元素添加到 ClaimsProviders 元素：

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> 将 `AppInsights-Common` 技术配置文件中的检测密钥更改为 Application Insights 资源提供的 GUID。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>添加技术配置文件，作为业务流程步骤

调用 `AppInsights-SignInRequest`（作为业务流程步骤 2），对是否已收到登录/注册请求进行跟踪：

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

添加调用 `AppInsights-UserSignup` 的新步骤后，再立即执行`SendClaims` 业务流程步骤。 当用户在注册/登录旅程中选择注册按钮时，会触发此步骤。

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

在 `SendClaims` 业务流程步骤后，立即调用 `AppInsights-SignInComplete`。 此步骤演示成功完成的过程。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 添加新的业务流程步骤以后，请按顺序对这些步骤重新编号，不要跳过从 1 到 N 的任何整数。


## <a name="upload-your-file-run-the-policy-and-view-events"></a>上传文件，运行策略，并查看事件

保存并上传 TrustFrameworkExtensions.xml 文件。 然后，通过应用程序调用信赖方策略，或者在 Azure 门户中使用“立即运行”。 稍等片刻，事件会在 Application Insights 中可用。

1. 在 Azure Active Directory 租户中打开 **Application Insights** 资源。
2. 选择 " **使用情况**"，然后选择 " **事件**"。
3. 将“期间”设置为“过去一小时”，将“截止时间”设置为“3 分钟”。  可能需要选择“刷新”才能查看结果。

![Application Insights USAGE-Events 边栏选项卡](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>收集更多数据

为了满足你的业务需求，你可能需要记录更多的声明。 若要添加声明，请首先 [定义声明](#define-claims)，然后将声明添加到输入声明集合。 添加到 *AppInsights-Common* 技术配置文件中的声明将显示在所有事件中。 添加到特定技术配置文件中的声明将仅出现在该事件中。 输入声明元素包含以下属性：

- **ClaimTypeReferenceId** -是对声明类型的引用。 
- **PartnerClaimType** -显示在 Azure Insights 中的属性的名称。 使用语法 `{property:NAME}`，其中 `NAME` 是要添加到该事件的属性。
- **DefaultValue** -要记录的预定义值，例如事件名称。 用户旅程中使用的声明，如标识提供者名称。 如果声明为空，将使用默认值。 例如， `identityProvider` 声明由联合技术配置文件（如 Facebook）设置。 如果声明为空，则表示用户使用本地帐户进行登录。 因此，默认值设置为 " *本地*"。 你还可以使用上下文值（例如应用程序 ID 或用户 IP 地址）来记录 [声明解析](claim-resolver-overview.md) 程序。

### <a name="manipulating-claims"></a>操作声明

您可以使用 [输入声明转换](custom-policy-trust-frameworks.md#manipulating-your-claims) 在发送到 Application Insights 之前修改输入声明或生成新的声明。 在下面的示例中，技术配置文件包含 *CheckIsAdmin* 输入声明转换。 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>添加事件

若要添加事件，请创建包含 *AppInsights-Common* 技术配置文件的新技术配置文件。 然后，将技术配置文件作为业务流程步骤添加到 [用户旅程](custom-policy-trust-frameworks.md#orchestration-steps)。 使用 " [前提条件](userjourneys.md#preconditions) " 在需要时触发事件。 例如，仅当用户通过 MFA 运行时才报告事件。

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

现在，你已经有了技术配置文件，请将该事件添加到用户旅程。 然后按顺序对步骤进行重新编号，而不会跳过从1到 N 的任何整数。

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>启用开发人员模式

使用 Application Insights 定义事件时，可以指示是否启用开发人员模式。 开发人员模式控制如何缓冲事件。 在事件量最少的开发环境中，启用开发人员模式会导致系统立即将事件发送到 Application Insights。 默认值是 `false`。 不要在生产环境中启用开发人员模式。

若要启用开发人员模式，请在 *AppInsights-Common* 技术配置文件中，将 `DeveloperMode` 元数据更改为 `true` ： 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>禁用遥测

若要禁用 Application insights 日志，请在 *AppInsights-Common* 技术配置文件中，将 `DisableTelemetry` 元数据更改为 `true` ： 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>后续步骤

- 了解如何 [使用 Azure 应用程序 Insights 创建自定义 KPI 仪表板](../azure-monitor/learn/tutorial-app-dashboards.md)。 

::: zone-end