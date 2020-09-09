---
title: Application Insights でのユーザー動作の追跡
titleSuffix: Azure AD B2C
description: カスタム ポリシーを使用して、Azure AD B2C ユーザー体験から Application Insights のイベント ログを有効にする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385979"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Application Insights を使用した Azure Active Directory B2C でのユーザー動作の追跡

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) では、Azure AD B2C に提供されるインストルメンテーション キーを使用して、イベント データを [Application Insights](../azure-monitor/app/app-insights-overview.md) に直接送信できます。  Application Insights の技術プロファイルを使用して、ユーザー体験の詳細なイベント ログをカスタマイズして取得することで、次のことが可能となります。

* ユーザーの動作を把握する。
* 開発時または運用時にポリシーの問題を解決する。
* パフォーマンスを測定する。
* Application Insights の通知を作成する。

## <a name="how-it-works"></a>しくみ

[Application Insights](application-insights-technical-profile.md) の技術プロファイルには、Azure AD B2C からのイベントが定義されます。 このプロファイルには、イベントの名前、記録される要求、およびインストルメンテーション キーを指定します。 イベントをポストするために、技術プロファイルはその後、オーケストレーション ステップとして[ユーザー体験](userjourneys.md)に追加されます。

Application Insight は、相関 ID を使用してこのイベントを統合し、ユーザー セッションを記録することができます。 Application Insights では、数秒でイベントとセッションを使用できるようになります。また、多くの視覚化、エクスポート、および分析のツールが用意されています。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。

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

技術プロファイルは、Azure AD B2C の Identity Experience Framework の機能と考えることができます。 この表では、セッションを開いてイベントを投稿するために使用される、次の技術プロファイルが定義されています。

| 技術プロファイル | タスク |
| ----------------- | -----|
| AppInsights-Common | すべての Azure-Insights 技術プロファイルに含まれる共通パラメーター セット。 |
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

`SendClaims` オーケストレーション ステップの*直前*に、`AppInsights-UserSignup` を呼び出す新しいステップを追加します。 これは、ユーザーがサインイン/サインアップ体験でサインアップ ボタンを選択したときにトリガーされます。

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

*TrustFrameworkExtensions.xml* ファイルを保存し、アップロードします。 次に、アプリケーションから証明書利用者ポリシーを呼び出すか、Azure portal で **[今すぐ実行]** を使用します。 数秒で、Application Insights でイベントを使用できるようになります。

1. Azure Active Directory テナントで **Application Insights** リソースを開きます。
2. **[使用状況]**  >  **[イベント]** を選択します。
3. **[期間]** を **[過去 1 時間]** に、 **[By]\(単位\)** を **[3 minutes]\(3 分\)** に設定します。  状況によっては、 **[更新]** を選択して結果を表示する必要があります。

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>(省略可) さらにデータを収集する

ニーズに合わせて、要求の種類とイベントをユーザー体験に追加します。 [要求リゾルバー](claim-resolver-overview.md)や任意の種類の文字列要求を使用し、Application Insights イベントや AppInsights-Common 技術プロファイルに **Input Claim** 要素を追加して、要求を追加できます。

- **ClaimTypeReferenceId** は要求の種類への参照です。
- **PartnerClaimType** は、Azure Insights で表示されるプロパティの名前です。 `{property:NAME}` の構文を使用します (`NAME` はイベントに追加されるプロパティです)。
- **DefaultValue** では、任意の文字列値または要求リゾルバーが使用されます。

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>次のステップ

- IEF のリファレンスで [Application Insights](application-insights-technical-profile.md) 技術プロファイルについてさらに学習します。 
