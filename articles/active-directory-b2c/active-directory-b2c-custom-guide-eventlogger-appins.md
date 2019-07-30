---
title: Azure Active Directory B2C から Application Insights のイベントを使用してユーザーの動作を追跡する | Microsoft Docs
description: カスタム ポリシーを使用して、Azure AD B2C ユーザー体験から Application Insights のイベント ログを有効にする方法について説明します (プレビュー)。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e18157c95dac0de90c50b4b7e8591e32c5b76aaf
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227230"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Application Insights を使用した Azure Active Directory B2C でのユーザー動作の追跡

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory (Azure AD) B2C を Azure Application Insights と連携させて使用すると、ユーザー体験の詳細なイベント ログをカスタマイズして取得することができます。 この記事では、次のことについて説明します。

* ユーザーの動作を把握する。
* 開発時または運用時にポリシーの問題を解決する。
* パフォーマンスを測定する。
* Application Insights の通知を作成する。

## <a name="how-it-works"></a>動作のしくみ

Azure AD B2C の Identity Experience Framework には、プロバイダー `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0` が含まれています。 このプロバイダーは、Azure AD B2C に提供されているインストルメンテーション キーを使用して、イベント データを Application Insights に直接送信します。

技術プロファイルは、このプロバイダーを使用して Azure AD B2C のイベントを定義します。 このプロファイルには、イベントの名前、記録される要求、およびインストルメンテーション キーを指定します。 イベントを投稿するために、カスタムのユーザー体験には技術プロファイルが `orchestration step` または `validation technical profile` として追加されます。

Application Insight は、相関 ID を使用してこのイベントを統合し、ユーザー セッションを記録することができます。 Application Insights では、数秒でイベントとセッションを使用できるようになります。また、多くの視覚化、エクスポート、および分析のツールが用意されています。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関するページの手順を完了します。 この記事では、カスタム ポリシー スターター パックを使用していることを前提としています。 しかし、スターター パックは必須ではありません。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

Application Insights と Azure AD B2C を併用している場合、唯一の要件はリソースを作成してインストルメンテーション キーを取得することです。

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いのサブスクリプションを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのサブスクリプションを含むディレクトリを選択します。 このテナントは自社の Azure AD B2C テナントではありません。
3. Azure portal の左上隅にある **[リソースの作成]** を選択し、 **[Application Insights]** を検索して選択します。
4. **Create** をクリックしてください。
5. リソースの **[名前]** を入力します。
6. **[アプリケーションの種類]** で **[ASP.NET Web アプリケーション]** を選択します。
7. **[リソース グループ]** で、既存のグループを選択するか、新しいグループの名前を入力します。
8. **Create** をクリックしてください。
4. Application Insights リソースを作成したら、そのリソースを開き、 **[要点]** を展開して、インストルメンテーション キーをコピーします。

![Application Insights の概要とインストルメンテーション キー](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>新しい ClaimType 定義の追加

スターター パックから *TrustFrameworkExtensions.xml* ファイルを開き、[BuildingBlocks](buildingblocks.md) 要素に次の要素を追加します。

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>新しい技術プロファイルの追加

技術プロファイルは、Azure AD B2C の Identity Experience Framework の機能と考えることができます。 この表では、セッションを開いてイベントを投稿するために使用される、次の技術プロファイルが定義されています。

| 技術プロファイル | タスク |
| ----------------- | -----|
| AzureInsights-Common | すべての AzureInsights 技術プロファイルに含まれる共通パラメーター セットを作成します。 | 
| AzureInsights-SignInRequest | サインイン要求を受け取ったときに一連の要求を含む SignIn イベントを作成します。 | 
| AzureInsights-UserSignup | ユーザーがサインアップ/サインインの体験でサインアップ オプションをトリガーしたときに、UserSignup イベントを作成します。 | 
| AzureInsights-SignInComplete | トークンが証明書利用者アプリケーションに送信されたときに、認証の正常完了を記録します。 | 

スターター パックの *TrustFrameworkExtensions.xml* ファイルにプロファイルを追加します。 **ClaimsProviders** 要素に次の要素を追加します。

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> `AzureInsights-Common` 技術プロファイルのインストルメンテーション キーをお使いの Application Insights が提供する GUID に変更します。

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>技術プロファイルをオーケストレーション ステップとして追加する

オーケストレーション ステップ 2 として `Azure-Insights-SignInRequest` を呼び出し、サインイン/サインアップ要求が受信されたことを追跡します。

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` オーケストレーション ステップの*直前*に、`Azure-Insights-UserSignup` を呼び出す新しいステップを追加します。 これは、ユーザーがサインイン/サインアップ体験でサインアップ ボタンを選択したときにトリガーされます。

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` オーケストレーション ステップの直後に `Azure-Insights-SignInComplete` を呼び出します。 このステップは、正常に完了した体験を示します。

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
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

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>次の手順

ニーズに合わせて、要求の種類とイベントをユーザー体験に追加します。 [要求リゾルバー](claim-resolver-overview.md)や任意の種類の文字列要求を使用し、Application Insights イベントや AzureInsights-Common 技術プロファイルに **Input Claim** 要素を追加して、要求を追加できます。 

- **ClaimTypeReferenceId** は要求の種類への参照です。
- **PartnerClaimType** は、Azure Insights で表示されるプロパティの名前です。 `{property:NAME}` の構文を使用します (`NAME` はイベントに追加されるプロパティです)。 
- **DefaultValue** では、任意の文字列値または要求リゾルバーが使用されます。 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

