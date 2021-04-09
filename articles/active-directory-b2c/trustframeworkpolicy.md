---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーの TrustFrameworkPolicy 要素を指定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470733"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

カスタム ポリシーは、階層型チェーンで互いを参照する 1 つ以上の XML 形式ファイルとして表されます。 XML 要素は、要求スキーマ、要求変換、コンテンツ定義、クレーム プロバイダー、技術プロファイル、ユーザー体験、およびオーケストレーション手順などの、ポリシーの要素を定義します。 各ポリシー ファイルは、ポリシー ファイルの最上位の **TrustFrameworkPolicy** 要素内で定義されます。

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** 要素には、次の属性が含まれています。

| 属性 | 必須 | 説明 |
|---------- | -------- | ----------- |
| PolicySchemaVersion | はい | ポリシーを実行するために使用されるスキーマ バージョン。 値は `0.3.0.0` である必要があります |
| TenantObjectId | いいえ | Azure Active Directory B2C (Azure AD B2C) テナントの一意のオブジェクト識別子。 |
| TenantId | はい | このポリシーが属するテナントの一意の識別子。 |
| PolicyId | はい | ポリシーの一意識別子。 この識別子には、プレフィックスとして *B2C_1A_* を付ける必要があります。 |
| PublicPolicyUri | はい | ポリシーの URI。テナント ID とポリシー ID の組み合わせです。 |
| DeploymentMode | いいえ | 指定できる値: `Production` または `Development`。 `Production` は既定値です。 このプロパティを使用して、ポリシーをデバッグします。 詳細については、「[ログの収集](troubleshoot-with-application-insights.md)」を参照してください。 |
| UserJourneyRecorderEndpoint | いいえ | ログ記録に使用されるエンドポイント。 属性が存在する場合は、値を `urn:journeyrecorder:applicationinsights` に設定する必要があります。 詳細については、「[ログの収集](troubleshoot-with-application-insights.md)」を参照してください。 |


次の例は、**TrustFrameworkPolicy** 要素を指定する方法を示しています。

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

**TrustFrameworkPolicy** 要素には、次の要素が含まれます。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| 基本ポリシーの識別子。 |
| [BuildingBlocks](buildingblocks.md) | 0:1 | ポリシーの構成要素。 |
| [ClaimsProviders](claimsproviders.md) | 0:1 | クレーム プロバイダーのコレクション。 |
| [UserJourneys](userjourneys.md) | 0:1 | ユーザー体験のコレクション。 |
| [RelyingParty](relyingparty.md) | 0:1 | 証明書利用者ポリシーの定義。 |

別のポリシーからポリシーを継承するには、ポリシー ファイルの **TrustFrameworkPolicy** 要素の下で **BasePolicy** 要素を宣言する必要があります。 **BasePolicy** 要素は、このポリシーの派生元である base ポリシーへの参照です。

**BasePolicy** 要素には、次の要素が含まれています。

| 要素 | 発生回数 | 説明 |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C テナントの識別子。 |
| PolicyId | 1:1 | 親ポリシーの識別子。 |


次の例は、base ポリシーを指定する方法を示しています。 この **B2C_1A_TrustFrameworkExtensions** ポリシーは、**B2C_1A_TrustFrameworkBase** ポリシーから派生したものです。

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

