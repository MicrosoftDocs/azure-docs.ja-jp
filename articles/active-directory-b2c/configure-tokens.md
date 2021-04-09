---
title: トークンの構成 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でトークンの有効期間と互換性の設定を構成する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f22a55a65b7dc4fd4f714d880804e6fd65dbbe46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654376"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でトークンを構成する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) で[トークンの有効期間と互換性](tokens-overview.md)を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>トークンの有効期間の動作

次のようなトークンの有効期間を構成できます。

- **[アクセスと ID トークンの有効期間 (分)]** - OAuth 2.0 ベアラー トークンと ID トークンの有効期間。 既定値は 60 分 (1 時間) です。 最短は 5 分以内です。 最長 (この値を含む) は 1,440 分 (24 時間) です。
- **[更新トークンの有効期間 (日)]** - アプリケーションが `offline_access` スコープを許可されていた場合に、新しいアクセス トークンを取得するために更新トークンを使用できる最長期間です。 既定値は 14 日です。 最短は 1 日以内です。 最長 (この値を含む) は 90 日です。
- **[更新トークンのスライディング ウィンドウの有効期間]** - 更新トークンのスライディング ウィンドウの種類。 `Bounded` は、 **[有効期間の長さ (日)]** に指定されたとおりに更新トークンを延長できることを示しています。 `No expiry` は、更新トークンのスライディング ウィンドウの有効期間が決して期限切れにならないことを示しています。
- **[有効期間の長さ (日)]** - この期間が経過すると、アプリケーションによって取得された最新の更新トークンの有効期間にかかわらず、ユーザーは強制的に再認証を求められます。 この値は、 **[更新トークンの有効期間]** の値以上でなければなりません。

次の図は、更新トークンのスライディング ウィンドウの有効期間における動作を示しています。

![更新トークンの有効期間](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >PKCE で認可コード フローを使用するシングルページ アプリケーションでは、更新トークンの有効期間は常に 24 時間ですが、モバイル アプリ、デスクトップ アプリ、Web アプリではこの制限がありません。 「[ブラウザーでの更新トークンのセキュリティへの影響](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)」で詳細情報を参照してください。


## <a name="configure-token-lifetime"></a>トークンの有効期間を構成する

::: zone pivot="b2c-user-flow"

ユーザー フロー トークンの有効期間を構成するには、次を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー (ポリシー)]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[プロパティ]** を選択します。
1. **[トークンの有効期間]** で、お使いのアプリケーションのニーズに合わせてプロパティを調整します。
1. **[保存]** をクリックします。

::: zone-end

::: zone pivot="b2c-custom-policy"

トークンの互換性の設定を変更するには、拡張機能、または影響を与えるポリシーの証明書利用者ファイルに、[トークン発行者](jwt-issuer-technical-profile.md)の技術プロファイル メタデータを設定します。 トークン発行者の技術プロファイルは次の例のようになります。

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

以下の値は前の例で設定されています。

- **token_lifetime_secs** - アクセス トークンの有効期間 (秒)。 既定値は 3,600 (1 時間) です。 最小値は 300 (5 分) です。 最大値は 86,400 (24 時間) です。 
- **id_token_lifetime_secs** - ID トークンの有効期間 (秒)。 既定値は 3,600 (1 時間) です。 最小値は 300 (5 分) です。 最大値は 86,400 (24 時間) です。 
- **refresh_token_lifetime_secs** - 更新トークンの有効期間 (秒) です。 既定値は 1,209,600 (14 日) です。 最小値は 86,400 (24 時間) です。 最大値は 7,776,000 (90 日) です。 
- **rolling_refresh_token_lifetime_secs** - 更新トークンのスライディング ウィンドウの有効期間 (秒)。 既定値は 7,776,000 (90 日) です。 最小値は 86,400 (24 時間) です。 最大値は 31,536,000 (365 日) です。 スライディング ウィンドウの有効期間を適用しない場合は、`allow_infinite_rolling_refresh_token` の値を `true` に設定します。 
- **allow_infinite_rolling_refresh_token** - 更新トークンのスライディング ウィンドウの有効期間は無期限です。 

::: zone-end


## <a name="token-compatibility-settings"></a>トークンの互換性の設定

次のようなトークンの互換性を構成できます。

- **[発行者 (iss) 要求]** - アクセスと ID トークン発行者の形式。
- **[サブジェクト (sub) 要求]** - トークンによって情報がアサートされるプリンシパル (アプリケーションのユーザーなど)。 この値は変更不可で、再割り当ても再利用もできません。 そのため、この値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。 既定では、サブジェクト要求には、ディレクトリ内のユーザーのオブジェクト ID が設定されます。
- **[ユーザー フローを表す要求]** - この要求は、実行されたユーザー フローを示します。 指定できる値は `tfp`(既定値) または`acr`です。

::: zone pivot="b2c-user-flow"

ユーザー フローの互換性設定を構成するには、次のようにします。

1. **[ユーザー フロー (ポリシー)]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[プロパティ]** を選択します。
1. **[トークンの互換性設定]** で、お使いのアプリケーションのニーズに合わせてプロパティを調整します。
1. **[保存]** をクリックします。

::: zone-end

::: zone pivot="b2c-custom-policy"

トークンの互換性の設定を変更するには、拡張機能、または影響を与えるポリシーの証明書利用者ファイルに、[トークン発行者](jwt-issuer-technical-profile.md)の技術プロファイル メタデータを設定します。 トークン発行者の技術プロファイルは次の例のようになります。

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **発行者 (iss) 要求**: 発行者 (iss) 要求は、**IssuanceClaimPattern** メタデータ項目で設定します。 指定できる値は、`AuthorityAndTenantGuid` および `AuthorityWithTfp` です。
- **ポリシー ID を表す要求の設定**: この値を設定するためのオプションは、`TFP` (Trust Framework Policy) および `ACR` (Authentication Context Reference) です。 `TFP` が推奨値です。 **AuthenticationContextReferenceClaimPattern** に値 `None` を設定します。

    **ClaimsSchema** 要素に、次の要素を追加します。

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    **OutputClaims** 要素に、次の要素を追加します。

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR の場合は、**AuthenticationContextReferenceClaimPattern** 項目を削除します。

- **サブジェクト (サブ) 要求**: このオプションは、既定で ObjectID に設定されています。この設定を `Not Supported` に切り替える場合は、次の行を置き換えます。

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    置き換えた後の行は次のとおりです。

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>アプリに省略可能な要求を提供する

アプリケーション要求は、アプリケーションに返される値です。 ユーザー フローを更新して、目的の要求を含めます。

::: zone pivot="b2c-user-flow"

1. **[ユーザー フロー (ポリシー)]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[アプリケーション クレーム]** を選択します。
1. アプリケーションに返信する要求と属性を選択します。
1. **[保存]** をクリックします。

::: zone-end

::: zone pivot="b2c-custom-policy"

[証明書利用者ポリシーの技術プロファイル](relyingparty.md#technicalprofile)の出力要求は、アプリケーションに返される値です。 出力要求を追加すると、ユーザー体験が成功した後、その要求がトークンに発行され、アプリケーションに送信されます。 証明書利用者セクション内の技術プロファイル要素を変更して、目的の要求を出力要求として追加します。

1. カスタム ポリシー ファイルを開きます。 たとえば、SignUpOrSignin.xml などです。
1. OutputClaims 要素を検索します。 トークンに含める OutputClaim を追加します。 
1. 出力要求の属性を設定します。 

次の例では、`accountBalance` 要求を追加します。 accountBalance 要求が、残高としてアプリケーションに送信されます。 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim 要素には、次の属性が含まれています。

- **ClaimTypeReferenceId** - ポリシー ファイルまたは親ポリシー ファイルの [ClaimsSchema](claimsschema.md) セクションで既に定義されている種類の要求の識別子。
- **PartnerClaimType** - トークン内の要求の名前を変更できます。 
- **DefaultValue** - 既定値です。 また、テナント ID など、[要求リゾルバー](claim-resolver-overview.md)に対する既定値も設定できます。
- **AlwaysUseDefaultValue** - 既定値の使用を強制します。

::: zone-end

## <a name="next-steps"></a>次のステップ

[アクセス トークンを要求](access-tokens.md)方法の詳細を確認します。