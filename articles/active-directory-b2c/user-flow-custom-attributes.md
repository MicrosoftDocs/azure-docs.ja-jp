---
title: Azure Active Directory B2C でカスタム属性を定義する | Microsoft Docs
description: 顧客に関する情報を収集するために、Azure Active Directory B2C でアプリケーションのカスタム属性を定義します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607919"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム属性を定義する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

「[カスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ](configure-user-input.md)」の記事では、組み込みの[ユーザー プロファイル属性](user-profile-attributes.md)を使用する方法について説明しています。 この記事では、Azure Active Directory B2C (Azure AD B2C) ディレクトリでカスタム属性を有効にします。 新しい属性は、後でカスタム要求として、[ユーザー フロー](user-flow-overview.md)や[カスタム ポリシー](custom-policy-get-started.md)で同時に使用することができます。

Azure AD B2C ディレクトリには、[組み込みの属性セット](user-profile-attributes.md)が付属します。 ただし、次のような場合には、特定のシナリオを管理するために、独自の属性を作成する必要があります。

* 顧客向けアプリケーションで、**LoyaltyId** 属性を保持する必要がある。
* ID プロバイダーが、保存する必要がある一意のユーザー識別子 (**uniqueUserGUID**) を保持している。
* カスタム ユーザー体験で、他のロジックを運用するために必要な、ユーザーの状態 (**migrationStatus**) を保持する必要がある。

用語の *拡張機能プロパティ*、*カスタム属性*、および *カスタム要求* では、この記事のコンテキストと同じ内容を参照します。 名前は、アプリケーション、オブジェクト、ポリシーなどのコンテキストによって異なります。

Azure AD B2C では、各ユーザー アカウントで保存される属性セットを拡張できます。 また、[Microsoft Graph API](microsoft-graph-operations.md) を使用してこれらの属性を読み書きすることもできます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>カスタム属性を作成する

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. Azure Portal の右上隅でディレクトリを切り替えて、Azure AD B2C テナントが含まれるディレクトリを使用していることを確認してください。 サブスクリプション情報を選択し、 **[ディレクトリの切り替え]** を選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー属性]** 、 **[追加]** の順に選択します。
1. カスタム属性の **[名前]** ("ShoeSize" など) を指定します。
1. **[データ型]** を選択します。 **[文字列]** 、 **[ブール値]** 、および **[Int] (整数)** のみを使用できます。
1. オプションで、情報目的のために **[説明]** を入力します。
1. **Create** をクリックしてください。

これで、このカスタム属性が **[ユーザー属性]** の一覧やユーザー フローで使用できるようになります。 カスタム属性が作成されるのは、いずれかのユーザー フローで初めて使用されるときだけであり、 **[ユーザー属性]** の一覧に追加されるときではありません。

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>ユーザー フローでカスタム属性を使用する

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. ポリシー ("B2C_1_SignupSignin" など) を選択して開きます。
1. **[ユーザー属性]** 、カスタム属性 ("ShoeSize" など) の順に選択します。 **[保存]** をクリックします。
1. **[アプリケーションの要求]** 、カスタム属性の順に選択します。
1. **[保存]** をクリックします。

新しく作成したカスタム属性を使用するユーザー フローを使用して新しいユーザーを作成したら、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) でオブジェクトのクエリを実行できます。 または、ユーザー フローに対して [[ユーザー フローを実行します]](./tutorial-create-user-flows.md) 機能を使用して、カスタマー エクスペリエンスを確認できます。 これで、**ShoeSize** がサインアップ中に収集される属性の一覧や、アプリケーションに送り返されるトークンに表示されるようになります。

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C 拡張アプリ

拡張属性は、ユーザーに関するデータを保持できる場合でも、アプリケーション オブジェクトにしか登録できません。 拡張属性は、`b2c-extensions-app` というアプリケーションに関連付けられます。 Azure AD B2C は、ユーザー データを保存するためにこのアプリケーションを使用します。このため、このアプリケーションは変更しないでください。 このアプリケーションは、Azure AD B2C (アプリの登録) から確認できます。 次の手順でアプリケーション プロパティを取得します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択してから、 **[すべてのアプリケーション]** を選択します。
1. `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` アプリケーションを選択します。
1. 次の識別子をクリップボードにコピーして保存します。
    * **アプリケーション ID**。 例: `11111111-1111-1111-1111-111111111111`.
    * **オブジェクト ID**。 例: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>カスタム ポリシーの変更

ポリシーでカスタム属性を有効にするには、AAD-Common 技術プロファイルのメタデータで、**アプリケーション ID** とアプリケーション **オブジェクト ID** を指定します。 *AAD-Common* 技術プロファイルは、その基盤である [Azure Active Directory](active-directory-technical-profile.md) 技術プロファイルに含まれており、Azure AD ユーザー管理のためのサポートを提供するものです。 他の Azure AD 技術プロファイルには、AAD-Common 技術プロファイルが含まれており、その構成が利用されています。 拡張ファイルで、AAD-Common 技術プロファイルをオーバーライドしてください。

1. お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。
1. ClaimsProviders 要素を見つけます。 ClaimsProviders 要素に新しい ClaimsProvider を追加します。
1. 記録しておいた **アプリケーション ID** を、開始 `<Item Key="ClientId">` と終了 `</Item>` の要素間に挿入します。
1. 記録しておいた **アプリケーション オブジェクト ID** を、開始 `<Item Key="ApplicationObjectId">` と終了 `</Item>` の要素間に挿入します。

    ```xml
    <!-- 
    <ClaimsProviders> -->
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
    <!-- 
    </ClaimsProviders> -->
    ```

## <a name="upload-your-custom-policy"></a>カスタム ポリシーのアップロード

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
5. **[カスタム ポリシーのアップロード]** を選択し、変更した TrustFrameworkExtensions.xml ポリシー ファイルをアップロードします。

> [!NOTE]
> Azure AD 技術プロファイルで要求がディレクトリに最初に保持される際には、カスタム属性が存在するかどうかがチェックされます。 ない場合は、カスタム属性を作成します。  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Azure portal を使用してカスタム属性を作成する

組み込みポリシーとカスタム ポリシー間では、同じ拡張属性が共有されます。 ポータルのエクスペリエンスを使用してカスタム属性を追加すると、これらの属性が、すべての B2C テナントに存在する **b2c-extensions-app** を使用して登録されます。

これらの属性は、カスタム ポリシーで使用する前後に、ポータルの UI を使用してを作成できます。 ポータルで属性 **loyaltyId** を作成するときに、次のようにこの属性を参照する必要があります。

|名前     |使用される場所 |
|---------|---------|
|`extension_loyaltyId`  | カスタム ポリシー|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

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

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>MS Graph API でのカスタム属性の使用

Microsoft Graph API では、拡張属性を使用したユーザーの作成と更新がサポートされています。 Graph API の拡張属性には、`extension_ApplicationClientID_attributename` の規則を使用して名前が付けられます。ここで `ApplicationClientID` は `b2c-extensions-app` アプリケーションの **アプリケーション (クライアント) ID** です。 拡張属性名で表されるように、**アプリケーション (クライアント) ID** にはハイフンが含まれないことに注意してください。 次に例を示します。

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>次のステップ

[カスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ](configure-user-input.md)に関するガイドラインに従ってください。 このサンプルでは、組み込みの要求の "city" を使用します。 カスタム属性を使用するには、"city" を独自のカスタム属性に置き換えます。
