---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーの TrustFrameworkPolicy 要素を指定します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 558e9c3a3bfd43f6ceb958bc3be55d58e1eb7f91
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150242"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

カスタム ポリシーは、階層型チェーンで互いを参照する 1 つ以上の XML 形式ファイルとして表されます。 XML 要素は、要求スキーマ、要求変換、コンテンツ定義、クレーム プロバイダー、技術プロファイル、ユーザー体験、およびオーケストレーション手順などの、ポリシーの要素を定義します。 各ポリシー ファイルは、ポリシー ファイルの最上位の **TrustFrameworkPolicy** 要素内で定義されます。 

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** 要素には、次の属性が含まれています。

| Attribute | 必須 | 説明 |
|---------- | -------- | ----------- |
| PolicySchemaVersion | はい | ポリシーを実行するために使用されるスキーマ バージョン。 値は `0.3.0.0` である必要があります |
| TenantObjectId | いいえ  | Azure Active Directory (Azure AD) B2C テナントの一意のオブジェクト識別子。 |
| TenantId | はい | このポリシーが属するテナントの一意の識別子。 |
| PolicyId | はい | ポリシーの一意識別子。 この識別子には、プレフィックスとして *B2C_1A_* を付ける必要があります。 |
| PublicPolicyUri | はい | ポリシーの URI。テナント ID とポリシー ID の組み合わせです。 |
| DeploymentMode | いいえ  | 指定できる値: `Production`、`Debugging`、または `Development`。 `Production` は既定値です。 このプロパティを使用して、ポリシーをデバッグします。 詳細については、「[ログの収集](active-directory-b2c-troubleshoot-custom.md)」を参照してください。 |
| UserJourneyRecorderEndpoint | いいえ  | **DeploymentMode** が `Development` に設定されているときに使用されるエンドポイント。 値は `urn:journeyrecorder:applicationinsights` である必要があります 詳細については、「[ログの収集](active-directory-b2c-troubleshoot-custom.md)」を参照してください。 |


次の例は、**TrustFrameworkPolicy** 要素を指定する方法を示しています。

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>継承モデル

これらのタイプのポリシー ファイルは、通常、ユーザー体験で使用されます。

- **Base ファイル**。ほとんどの定義が含まれています。 ポリシーのトラブルシューティングおよび長期間の保守に役立つように、このファイルへの変更は最小限に抑えることをお勧めします。
- **Extensions** ファイル。テナントの固有の構成変更を保持しています このポリシー ファイルは、Base ファイルから派生したものです。 このファイルを使用して、新しい機能を追加するか、既存の機能をオーバーライドします。 たとえば、このファイルを使用して、新しい ID プロバイダーとフェデレーションします。
- **証明書利用者 (RP)** ファイル。証明書利用者アプリケーション (Web、モバイル、またはデスクトップ アプリケーションなど) から直接呼び出される、単一タスクに焦点を置いています。 サインアップやサインイン、パスワードのリセット、プロファイルの編集などの各自のタスクには、それぞれ独自の RP ポリシー ファイルが必要です。 このポリシー ファイルは、Extensions ファイルから派生したものです。 

証明書利用者アプリケーションは、RP ポリシー ファイルを呼び出して、特定のタスクを実行します。 たとえば、サインインフローを開始する場合です。 Azure AD B2C の Identity Experience Framework はまず Base ファイルから、次に Extensions ファイルから、最後に RP ポリシー ファイルからすべての要素を追加して、有効な現在のポリシーを組み立てます。 要素の種類と名前が同じ場合は RP ファイルの要素によって Extensions の要素がオーバーライドされ、Extensions によって Base がオーバーライドされます。 次の図は、ポリシー ファイルと証明書利用者アプリケーションの関係を示しています。

![継承モデル](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

継承モデルは次のとおりです。

- 親ポリシーと子ポリシーは同じスキーマです。
- どのレベルの子ポリシーでも、親ポリシーから継承することができ、新しい要素を追加することによって拡張することができます。
- レベルの数に制限はありません。

詳細については、「[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」を参照してください。

## <a name="base-policy"></a>Base ポリシー

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
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>ポリシー実行

Web、モバイル、またはデスクトップ アプリケーションなどの証明書利用者アプリケーションは、[証明書利用者 (RP) ポリシー](relyingparty.md)を呼び出します。 RP ポリシー ファイルは、サインイン、パスワードのリセット、またはプロファイルの編集など、特定のタスクを実行します。 RP ポリシーは、発行されるトークンの一部として、証明書利用者アプリケーションが受け取る要求の一覧を構成します。 複数のアプリケーションで同じポリシーを使用できます。 すべてのアプリケーションは、要求を含む同じトークンを受け取り、ユーザーは同じユーザー体験をします。 1 つのアプリケーションで複数のポリシーを使用できます。

RP ポリシー ファイル内で、**DefaultUserJourney** 要素を指定します。この要素は [UserJourney](userjourneys.md) をポイントします。 ユーザー体験は通常、Base または Extensions ポリシーで定義されます。

B2C_1A_signup_signin ポリシー:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase または B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

ユーザー体験は、ユーザーが体験する内容のビジネス ロジックを定義します。 各ユーザー体験は、認証と情報収集に関してひと続きのアクションを順番に実行する、一連のオーケストレーション ステップです。 

[スターター パック](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies)の **SocialAndLocalAccounts** ポリシー ファイルには、SignUpOrSignIn、ProfileEdit、PasswordReset のユーザー体験が含まれています。 電子メール アドレスの変更、ソーシャル アカウントのリンクやリンク解除、パスワードの再設定など、別のシナリオでさらに多くのユーザー体験を追加することができます。 

オーケストレーション ステップで、[技術プロファイル](technicalprofiles.md)を呼び出すことができます。 技術プロファイルは、さまざまな種類の利用者と通信するためのメカニズムが組み込まれたフレームワークを提供します。 たとえば、技術プロファイルでは、特に次のアクションを実行できます。

- ユーザー エクスペリエンスをレンダリングする。
- ユーザーが、ソーシャルまたはエンタープライズ アカウント (Facebook、Microsoft アカウント、Google、Salesforce、または他の ID プロバイダーなど) を使用してサインインできるようにする。
- MFA の電話による確認を設定する。
- Azure AD B2C ID ストアとの間でデータの読み書きを行う。
- カスタムの Restful API サービスを呼び出す。

![ポリシー実行](./media/trustframeworkpolicy/custom-policy-execution.png)

 **TrustFrameworkPolicy** 要素には、次の要素が含まれます。

- 上記の BasePolicy
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)

