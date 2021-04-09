---
title: Application Insights を使用してユーザーの動作を追跡する
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
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645927"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Application Insights を使用して Azure AD B2C でのユーザーの動作を追跡する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) では、Azure AD B2C に提供されるインストルメンテーション キーを使用して、イベント データを [Application Insights](../azure-monitor/app/app-insights-overview.md) に直接送信できます。 Application Insights の技術プロファイルを使用して、ユーザー体験の詳細なイベント ログをカスタマイズして取得することで、次のことが可能となります。

- ユーザーの動作を把握する。
- 開発時または運用時にポリシーの問題を解決する。
- パフォーマンスを測定する。
- Application Insights の通知を作成する。

## <a name="overview"></a>概要

カスタムのイベント ログを有効にするには、Application Insights 技術プロファイルを追加します。 技術プロファイルに、Application Insights インストルメンテーション キー、イベント名、および記録する要求を定義します。 イベントをポストするために、技術プロファイルをオーケストレーション ステップとして[ユーザー体験](userjourneys.md)に追加します。

Application Insights を使用する場合は、次の点を考慮してください。

- Application Insights で新しいログが利用できるようになるまで、少し時間がかかります (通常は 5 分以内)。
- Azure AD B2C では、記録する要求を選択できます。 個人データが含まれている要求は含めないでください。
- ユーザー セッションを記録するために、相関 ID を使用してイベントを統合できます。
- [ユーザー体験](userjourneys.md)または[サブ体験](subjourneys.md)から、Application Insights 技術プロファイルを直接呼び出します。 Application Insights 技術プロファイルを[検証技術プロファイル](validation-technical-profile.md)として使用しないでください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

Application Insights を Azure AD B2C と共に使用している場合は、リソースを作成してインストルメンテーション キーを取得するだけです。 詳細については、「[Application Insights リソースの作成](../azure-monitor/app/create-new-resource.md)」を参照してください。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. Azure サブスクリプションがあるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure サブスクリプションが含まれているディレクトリを選択します。 このテナントは Azure AD B2C テナントではありません。
1. Azure portal の左上隅にある **[リソースの作成]** を選択し、 **[Application Insights]** を検索して選択します。
1. **［作成］** を選択します
1. **[名前]** には、リソースの名前を入力します。
1. **[アプリケーションの種類]** で **[ASP.NET Web アプリケーション]** を選択します。
1. **[リソース グループ]** で、既存のグループを選択するか、新しいグループの名前を入力します。
1. **［作成］** を選択します
1. Application Insights の新しいリソースを開き、 **[Essentials]\(要点\)** を展開して、インストルメンテーション キーをコピーします。

![Application Insights の [概要] タブのインストルメンテーション キーを示すスクリーンショット。](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>要求を定義する

要求は、Azure AD B2C ポリシーの実行時に、データの一時的なストレージとなります。 要求は、[ClaimsSchema 要素](claimsschema.md)で宣言します。

1. お使いのポリシーの拡張ファイルを開きます。 ファイルは、`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** のようになります。
1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が表示されない場合は、追加します。
1. **ClaimsSchema** 要素を見つけます。 要素が表示されない場合は、追加します。
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

技術プロファイルは、カスタム ポリシーの機能と考えることができます。 これらの関数では、[技術プロファイルの包含](technicalprofiles.md#include-technical-profile)アプローチが使用されます。この場合、技術プロファイルには、設定の変更や新しい機能の追加を行うための別の技術プロファイルが含まれています。 次の表に、セッションを開いてイベントをポストするために使用される技術プロファイルを定義しています。

| 技術プロファイル | タスク |
| ----------------- | -----|
| AppInsights-Common | 一般的な構成セットが含まれた共通技術プロファイル。 これには、Application Insights のインストルメンテーションキー、記録する要求のコレクション、および開発者モードが含まれています。 もう 1 つの技術プロファイルには共通技術プロファイルが含まれており、イベント名などのその他の要求が追加されます。 |
| AppInsights-SignInRequest | サインイン要求を受け取ったときに一連の要求を含む **SignInRequest** イベントを記録します。 |
| AppInsights-UserSignUp | ユーザーがサインアップまたはサインイン体験でサインアップ オプションをトリガーしたときに、**UserSignUp** イベントを記録します。 |
| AppInsights-SignInComplete | トークンが証明書利用者アプリケーションに送信されたとき、認証の正常完了時に **SignInComplete** イベントを記録します。 |

スターター パックの *TrustFrameworkExtensions.xml* ファイルを開きます。 **ClaimsProvider** 要素に技術プロファイルを追加します。

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

技術プロファイルを参照する新しいオーケストレーション ステップを追加します。

> [!IMPORTANT]
> 新しいオーケストレーション ステップを追加した後、ステップの番号には、1 から N の整数がスキップされずに順番に付け替えられます。

1. `AppInsights-SignInRequest` を 2 番目のオーケストレーション ステップとしてを呼び出します。 このステップでは、サインアップまたはサインイン要求が受信されていることが追跡されます。

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. `SendClaims` オーケストレーション ステップの前に、`AppInsights-UserSignup` を呼び出す新しいステップを追加します。 これは、ユーザーがサインアップまたはサインイン体験でサインアップ ボタンを選択したときにトリガーされます。

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. `SendClaims` オーケストレーション ステップの後に `AppInsights-SignInComplete` を呼び出します。 このステップは、正常に完了した体験を示します。

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>ファイルをアップロードし、ポリシーを実行してイベントを表示する

*TrustFrameworkExtensions.xml* ファイルを保存し、アップロードします。 次に、アプリケーションから証明書利用者ポリシーを呼び出すか、Azure portal で **[今すぐ実行]** を使用します。 Application Insights でイベントが使用可能になるのを待ちます。

1. Azure Active Directory テナントで **Application Insights** リソースを開きます。
1. **[使用]** を選択し、次に **[イベント]** を選択します。
1. **[期間]** を **[過去 1 時間]** に、 **[By]\(単位\)** を **[3 minutes]\(3 分\)** に設定します。 結果を表示するには、ウィンドウを最新の情報に更新することが必要になる場合があります。

![Application Insights のイベント統計情報を示すスクリーンショット。](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>さらに多くのデータを収集する

ビジネス ニーズに合わせて、さらに多くの要求を記録できます。 要求を追加するには、まず[要求を定義](#define-claims)してから、要求を入力要求コレクションに追加します。 **AppInsights-Common** 技術プロファイルに追加した要求は、すべてのイベントに表示されます。 特定の技術プロファイルに追加した要求は、そのイベントにのみ表示されます。 入力要求要素には、次の属性が含まれています。

- **ClaimTypeReferenceId** は要求の種類への参照です。
- **PartnerClaimType** は、Azure Insights で表示されるプロパティの名前です。 `{property:NAME}` の構文を使用します (`NAME` はイベントに追加されるプロパティです)。
- **DefaultValue** は、イベント名など、記録される定義済みの値です。 ユーザー体験で使用される要求が空の場合は、既定値が使用されます。 たとえば、`identityProvider` 要求は、Facebook などのフェデレーション技術プロファイルによって設定されます。 要求が空の場合は、ユーザーがローカル アカウントでサインインしていることを示しています。 したがって、既定値は **Local** に設定されます。 また、アプリケーション ID やユーザー IP アドレスなどのコンテキスト値を含む[要求リゾルバー](claim-resolver-overview.md)を記録することもできます。

### <a name="manipulate-claims"></a>要求を操作する

Application Insights に入力要求を送信する前に、[入力要求変換](custom-policy-trust-frameworks.md#manipulating-your-claims)を使用して変更したり、新しいものを生成したりすることができます。 次の例では、技術プロファイルに `CheckIsAdmin` 入力要求変換が含まれています。

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

イベントを追加するには、`AppInsights-Common` 技術プロファイルが含まれている新しい技術プロファイルを作成します。 その後、[ユーザー体験](custom-policy-trust-frameworks.md#orchestration-steps)に、オーケストレーション ステップとして新しい技術プロファイルを追加します。 準備ができたら、イベントをトリガーする [Precondition](userjourneys.md#preconditions) 要素を使用します。 たとえば、ユーザーが多要素認証を実行した場合にのみイベントを報告します。

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>ユーザー体験にイベントを追加する場合は、必ずオーケストレーション ステップの番号を順次付け直してください。

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

開発者モードを有効にするには、`AppInsights-Common` 技術プロファイルで `DeveloperMode` メタデータを `true` に変更します。

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>遠隔測定を無効にする

Application Insights のログを無効にするには、`AppInsights-Common` 技術プロファイルで `DisableTelemetry` メタデータを `true` に変更します。

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>次のステップ

[Azure Application Insights を使ってカスタム KPI ダッシュボードを作成する](../azure-monitor/app/tutorial-app-dashboards.md)方法について説明します。

::: zone-end