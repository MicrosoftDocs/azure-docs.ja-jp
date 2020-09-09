---
title: カスタム ポリシーで Application Insights 技術プロファイルを定義する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 内のカスタム ポリシーで Application Insights 技術プロファイルを定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 77bb53e2605913fcee6999284acb04616efc53af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201414"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C カスタム ポリシーで Application Insights 技術プロファイルを定義する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) では、Azure AD B2C に提供されるインストルメンテーション キーを使用して、イベント データを [Application Insights](../azure-monitor/app/app-insights-overview.md) に直接送信できます。  Application Insights の技術プロファイルを使用して、ユーザー体験の詳細なイベント ログをカスタマイズして取得することで、次のことが可能となります。

* ユーザーの動作を把握する。
* 開発時または運用時にポリシーの問題を解決する。
* パフォーマンスを測定する。
* Application Insights の通知を作成する。


## <a name="protocol"></a>Protocol

**Protocol** 要素の **Name** 属性は `Proprietary` に設定する必要があります。 Application Insights のために**ハンドラー**属性は、Azure AD B2C で使用されるプロトコル ハンドラーのアセンブリの完全修飾名を含める必要があります。`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

次の例では、一般的な Application Insights 技術プロファイルを示します。 他の Application Insights 技術プロファイルには、AzureInsights-Common 技術プロファイルが含まれており、その構成が利用されています。  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>入力クレーム

**InputClaims** 要素には、Application Insights に送信する要求の一覧が含まれます。 また、要求の名前を Application Insights に優先表示する名前にマップすることもできます。 次の例では、Application Insights にテレメトリを送信する方法を示します。 イベントのプロパティは、`{property:NAME}` 構文を使用して追加します。ここで、NAME は、イベントに追加するプロパティです。 DefaultValue には、静的な値か、サポートされている [要求リゾルバー](claim-resolver-overview.md)のいずれかによって解決される値を指定できます。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations** 要素には、Application Insights に送信する前に、入力要求を修正したり新しい要求を生成するために使用される、**InputClaimsTransformation** 要素のコレクションが存在する場合があります。

## <a name="persist-claims"></a>永続化した要求

PersistedClaims 要素は使用されません。

## <a name="output-claims"></a>出力クレーム

OutputClaims 要素と OutputClaimsTransformations 要素は使用されません。

## <a name="cryptographic-keys"></a>暗号化キー

CryptographicKeys 要素は使用されません。


## <a name="metadata"></a>Metadata

| 属性 | Required | 説明 |
| --------- | -------- | ----------- |
| InstrumentationKey| はい | Application Insights の[インストルメンテーション キー](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)。これは、イベントのログ記録に使用されます。 | 
| DeveloperMode| いいえ | 開発者モードが有効かどうかを示すブール値。 指定できる値: `true` または `false`(既定値)。 このメタデータは、イベントがどのようにバッファリングされるかを制御します。 イベントの量が最小限の開発環境では、開発者モードを有効にすると、イベントが直ちに Application Insights に送信されます。|  
|DisableTelemetry |いいえ |テレメトリを有効にする必要があるかどうかを示すブール値。 指定できる値: `true` または `false`(既定値)。| 


## <a name="next-steps"></a>次のステップ

- [Application Insights リソースを作成します](../azure-monitor/app/create-new-resource.md)。
- [Application Insights を使用して Azure Active Directory B2C でのユーザー動作を追跡する](analytics-with-application-insights.md)方法を学習します
