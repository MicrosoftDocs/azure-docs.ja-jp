---
title: Application Insights でのユーザー動作の追跡
titleSuffix: Azure AD B2C
description: Azure AD B2C ユーザー体験から Application Insights のイベント ログを有効にする方法について説明します。
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
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218555"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Application Insights を使用した Azure Active Directory B2C でのユーザー動作の追跡

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) では、Azure AD B2C に提供されるインストルメンテーション キーを使用して、イベント データを [Application Insights](../azure-monitor/app/app-insights-overview.md) に直接送信できます。 Application Insights の技術プロファイルを使用して、ユーザー体験の詳細なイベント ログをカスタマイズして取得することで、次のことが可能となります。

* ユーザーの動作を把握する。
* 開発時または運用時にポリシーの問題を解決する。
* パフォーマンスを測定する。
* Application Insights の通知を作成する。

## <a name="overview"></a>概要

カスタムのイベント ログを有効にするには、Application Insights 技術プロファイルを追加します。 技術プロファイルで、Application Insights インストルメンテーション キー、イベント名、および記録する要求を定義します。 イベントをポストするために、技術プロファイルはその後、オーケストレーション ステップとして[ユーザー体験](userjourneys.md)に追加されます。

Application Insights を使用する場合は、次の点を考慮してください。

- Application Insights で新しいログが利用できるようになるまで、少し時間がかかります (通常は 5 分以内)。
- Azure AD B2C では、記録する要求を選択できます。 個人データが含まれている要求は含めないでください。
- ユーザー セッションを記録するために、関連付け ID を使用してイベントを統合できます。 
- [ユーザー体験](userjourneys.md)または[サブ体験](subjourneys.md)から、Application Insights 技術プロファイルを直接呼び出します。 Application Insights 技術プロファイルを[検証技術プロファイル](validation-technical-profile.md)として使用しないでください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

Application Insights と Azure AD B2C を併用している場合、唯一の要件はリソースを作成してインストルメンテーション キーを取得することです。 詳細については、「[Application Insights リソースの作成](../azure-monitor/app/create-new-resource.md)」を参照してください。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. お使いのサブスクリプションを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、お使いのサブスクリプションを含むディレクトリを選択します。 このテナントは自社の Azure AD B2C テナントではありません。
3. Azure portal の左上隅にある **[リソースの作成]** を選択し、 **[Application Insights]** を検索して選択します。
4. **Create** をクリックしてください。
5. リソースの **[名前]** を入力します。
6. **[アプリケーションの種類]** で **[ASP.NET Web アプリケーション]** を選択します。
7. **[リソース グループ]** で、既存のグループを選択するか、新しいグループの名前を入力します。
8. **Create** をクリックしてください。
4. Application Insights リソースを作成したら、そのリソースを開き、 **[要点]** を展開して、インストルメンテーション キーをコピーします。

![Application Insights の概要とインストルメンテーション キー](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>要求を定義する

要求は、Azure AD B2C ポリシーの実行中に、データの一時的なストレージを提供します。 [要求スキーマ](claimsschema.md)は、要求を宣言する場所です。

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。 

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

## <a name="add-new-technical-profiles"></a>新しい技術プロファイルの追加

技術プロファイルは、カスタム ポリシーの機能と考えることができます。 この表では、セッションを開いてイベントを投稿するために使用される、次の技術プロファイルが定義されています。 このソリューションでは、[技術プロファイルを含める](technicalprofiles.md#include-technical-profile)方法を使用します。 技術プロファイルには、設定を変更したり、新しい機能を追加したりするための別の技術プロファイルが含まれています。

| 技術プロファイル | タスク |
| ----------------- | -----|
| AppInsights-Common | 共通の構成セットを含む共通技術プロファイル。 Application Insights インストルメンテーションキー、記録する要求のコレクション、および開発者モードが含まれます。 次の技術プロファイルには共通技術プロファイルが含まれており、イベント名などのその他の要求が追加されます。 |
| AppInsights-SignInRequest | サインイン要求を受け取ったときに一連の要求を含む `SignInRequest` イベントを記録します。 |
| AppInsights-UserSignUp | ユーザーがサインアップまたはサインインの体験でサインアップ オプションをトリガーしたときに、`UserSignUp` イベントを記録します。 |
| AppInsights-SignInComplete | トークンが証明書利用者アプリケーションに送信されたとき、認証の正常完了時に `SignInComplete` イベントを記録します。 |

スターター パックの *TrustFrameworkExtensions.xml* ファイルにプロファイルを追加します。 **ClaimsProviders** 要素に次の要素を追加します。

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
> `AppInsights-Common` 技術プロファイルのインストルメンテーション キーをお使いの Application Insights が提供する GUID に変更します。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>技術プロファイルをオーケストレーション ステップとして追加する

オーケストレーション ステップ 2 として `AppInsights-SignInRequest` を呼び出し、サインイン/サインアップ要求が受信されたことを追跡します。

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` オーケストレーション ステップの *直前* に、`AppInsights-UserSignup` を呼び出す新しいステップを追加します。 これは、ユーザーがサインイン/サインアップ体験でサインアップ ボタンを選択したときにトリガーされます。

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

`SendClaims` オーケストレーション ステップの直後に `AppInsights-SignInComplete` を呼び出します。 このステップは、正常に完了した体験を示します。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 新しいオーケストレーション ステップを追加した後、ステップの番号には、1 から N の整数がスキップされずに順番に付け替えられます。


## <a name="upload-your-file-run-the-policy-and-view-events"></a>ファイルをアップロードし、ポリシーを実行してイベントを表示する

*TrustFrameworkExtensions.xml* ファイルを保存し、アップロードします。 次に、アプリケーションから証明書利用者ポリシーを呼び出すか、Azure portal で **[今すぐ実行]** を使用します。 1 分ほど待つと、Application Insights でイベントを使用できるようになります。

1. Azure Active Directory テナントで **Application Insights** リソースを開きます。
2. **[使用]** を選択してから、 **[イベント]** を選択します。
3. **[期間]** を **[過去 1 時間]** に、 **[By]\(単位\)** を **[3 minutes]\(3 分\)** に設定します。  状況によっては、 **[更新]** を選択して結果を表示する必要があります。

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>さらに多くのデータを収集する

ビジネス ニーズに合わせて、さらに多くの要求を記録することができます。 要求を追加するには、まず[要求を定義](#define-claims)してから、要求を入力要求コレクションに追加します。 *AppInsights-Common* 技術プロファイルに追加した要求は、すべてのイベントに表示されます。 特定の技術プロファイルに追加した要求は、そのイベントにのみ表示されます。 入力要求要素には、次の属性が含まれています。

- **ClaimTypeReferenceId** - 要求の種類への参照です。 
- **PartnerClaimType** - Azure Insights で表示されるプロパティの名前です。 `{property:NAME}` の構文を使用します (`NAME` はイベントに追加されるプロパティです)。
- **DefaultValue** -イベント名など、記録される定義済みの値です。 ID プロバイダー名など、ユーザー体験で使用される要求。 要求が空白の場合は、既定値が使用されます。 たとえば、`identityProvider` 要求は、Facebook などのフェデレーション技術プロファイルによって設定されます。 要求が空の場合は、ユーザーがローカル アカウントでサインインしていることを示します。 したがって、既定値は *Local* に設定されます。 また、アプリケーション ID やユーザー IP アドレスなどのコンテキスト値を含む[要求リゾルバー](claim-resolver-overview.md)を記録することもできます。

### <a name="manipulating-claims"></a>要求の操作

Application Insights に送信する前に、[入力要求変換](custom-policy-trust-frameworks.md#manipulating-your-claims)を使用して入力要求を変更したり、新しい要求を生成したりすることができます。 次の例では、技術プロファイルに *CheckIsAdmin* 入力要求変換が含まれています。 

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

### <a name="add-events"></a>イベントの追加

イベントを追加するには、*AppInsights-Common* 技術プロファイルが含まれている新しい技術プロファイルを作成します。 その後、[ユーザー体験](custom-policy-trust-frameworks.md#orchestration-steps)に、オーケストレーション ステップとして技術プロファイルを追加します。 必要に応じて、[前提条件](userjourneys.md#preconditions)を使用してイベントをトリガーします。 たとえば、ユーザーが MFA を介して実行した場合にのみイベントを報告します。

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

これで技術プロファイルができたので、イベントをユーザー体験に追加します。 その後、ステップの番号には、1 から N の整数がスキップされずに順番に付け替えられます。

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

## <a name="enable-developer-mode"></a>開発者モードを有効にする

Application Insights を使用してイベントを定義する場合は、開発者モードを有効にするかどうかを指定できます。 開発者モードでは、イベントがどのようにバッファリングされるかを制御します。 イベントの量が最小限の開発環境では、開発者モードを有効にすると、イベントが直ちに Application Insights に送信されます。 既定値は `false` です。 運用環境では開発者モードを有効にしないでください。

開発者モードを有効にするには、*AppInsights-Common* 技術プロファイルで、`DeveloperMode` メタデータを `true` に変更します。 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>遠隔測定を無効にする

Application Insights のログを無効にするには、*AppInsights-Common* 技術プロファイルで、`DisableTelemetry` メタデータを `true` に変更します。 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>次のステップ

- [Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する](../azure-monitor/learn/tutorial-app-dashboards.md)方法について説明します。 

::: zone-end