---
title: 独自の属性をカスタム ポリシーに追加する
titleSuffix: Azure AD B2C
description: 拡張プロパティおよびカスタム属性を使用し、ユーザー インターフェイスにそれらを含めるチュートリアル。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dbeb2540084fad2cfab3ce360dd15b60a75e5e59
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389328"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C:カスタム プロファイル ポリシーでカスタム属性を有効にする

「[カスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ](custom-policy-configure-user-input.md)」の記事では、組み込みの[ユーザー プロファイル属性](user-profile-attributes.md)を使用する方法について説明しています。 この記事では、Azure Active Directory B2C (Azure AD B2C) ディレクトリでカスタム属性を有効にします。 新しい属性は、後でカスタム要求として、[ユーザー フロー](user-flow-overview.md)や[カスタム ポリシー](custom-policy-get-started.md)で同時に使用することができます。

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>前提条件

「[Azure Active Directory B2C: カスタム ポリシーの概要](custom-policy-get-started.md)」に記載の手順に従ってください。

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>カスタム属性を使用して顧客に関する情報を収集する 

Azure AD B2C ディレクトリには、[組み込みの属性セット](user-profile-attributes.md)が付属します。 ただし、次のような場合には、特定のシナリオを管理するために、独自の属性を作成する必要があります。

* 顧客向けアプリケーションで、**LoyaltyId** 属性を保持する必要がある。
* ID プロバイダーが、保存する必要がある一意のユーザー識別子 (**uniqueUserGUID**) を保持している。
* カスタム ユーザー体験で、他のロジックを運用するために必要な、ユーザーの状態 (**migrationStatus**) を保持する必要がある。

Azure AD B2C では、各ユーザー アカウントで保存される属性セットを拡張できます。 また、[Microsoft Graph API](manage-user-accounts-graph-api.md) を使用してこれらの属性を読み書きすることもできます。

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 拡張アプリ

拡張属性は、ユーザーに関するデータを保持できる場合でも、アプリケーション オブジェクトにしか登録できません。 拡張属性は、b2c-extensions-app というアプリケーションに関連付けられます。 Azure AD B2C は、ユーザー データを保存するためにこのアプリケーションを使用します。このため、このアプリケーションは変更しないでください。 このアプリケーションは、Azure AD B2C (アプリの登録) から確認できます。

用語の*拡張機能プロパティ*、*カスタム属性*、および*カスタム要求*では、この記事のコンテキストと同じ内容を参照します。 名前は、アプリケーション、オブジェクト、ポリシーなどのコンテキストによって異なります。

## <a name="get-the-application-properties"></a>アプリケーション プロパティの取得

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択してから、 **[すべてのアプリケーション]** を選択します。
1. `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` アプリケーションを選択します。
1. 次の識別子をクリップボードにコピーして保存します。
    * **アプリケーション ID**。 例: `11111111-1111-1111-1111-111111111111`.
    * **オブジェクト ID**。 例: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>カスタム ポリシーの変更

ポリシーでカスタム属性を有効にするには、AAD-Common 技術プロファイルのメタデータで、**アプリケーション ID** とアプリケーション **オブジェクト ID** を指定します。 *AAD-Common* 技術プロファイルは、その基盤である [Azure Active Directory](active-directory-technical-profile.md) 技術プロファイルに含まれており、Azure AD ユーザー管理のためのサポートを提供するものです。 他の Azure AD 技術プロファイルには、AAD-Common 技術プロファイルが含まれており、その構成が利用されています。 拡張ファイルで、AAD-Common 技術プロファイルをオーバーライドしてください。

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. ClaimsProviders 要素を見つけます。 ClaimsProviders 要素に新しい ClaimsProvider を追加します。
1. `ApplicationObjectId` を、前に記録したオブジェクト ID に置き換えます。 次に、下記のスニペットで、`ClientId` を前に記録したアプリケーション ID に置き換えます。

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>カスタム ポリシーのアップロード

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. **[カスタム ポリシーのアップロード]** を選択し、変更した TrustFrameworkExtensions.xml ポリシー ファイルをアップロードします。

> [!NOTE]
> Azure AD 技術プロファイルで要求がディレクトリに最初に保持される際には、カスタム属性が存在するかどうかがチェックされます。 ない場合は、カスタム属性を作成します。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Azure portal を使用してカスタム属性を作成する

組み込みポリシーとカスタム ポリシー間では、同じ拡張属性が共有されます。 ポータルのエクスペリエンスを使用してカスタム属性を追加すると、これらの属性が、すべての B2C テナントに存在する **b2c-extensions-app** を使用して登録されます。

これらの属性は、カスタム ポリシーで使用する前後に、ポータルの UI を使用してを作成できます。 [Azure Active Directory B2C でカスタム属性を定義する](user-flow-custom-attributes.md)方法のガイドラインに従ってください。 ポータルで属性 **loyaltyId** を作成するときに、次のようにこの属性を参照する必要があります。

|名前     |使用される場所 |
|---------|---------|
|`extension_loyaltyId`  | カスタム ポリシー|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

次の例は、Azure AD B2C カスタム ポリシーの要求定義でカスタム属性を使用する方法を示したものです。

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

次の例は、技術プロファイル、入力、出力、および永続化された要求の Azure AD B2C カスタム ポリシーで、カスタム属性を使用する方法を示したものです。

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>ポリシーでカスタム属性を使用する

[カスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ](custom-policy-configure-user-input.md)に関するガイドラインに従ってください。 このサンプルでは、組み込みの要求の "city" を使用します。 カスタム属性を使用するには、"city" を独自のカスタム属性に置き換えます。


## <a name="next-steps"></a>次のステップ

各項目の詳細情報

- [Azure AD B2C のユーザー プロファイル属性](user-profile-attributes.md)
- [拡張属性の定義](user-profile-attributes.md#extension-attributes)
- [Microsoft Graph を使用して Azure AD B2C ユーザー アカウントを管理する](manage-user-accounts-graph-api.md)
