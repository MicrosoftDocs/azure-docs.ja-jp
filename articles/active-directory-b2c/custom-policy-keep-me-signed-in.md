---
title: Azure Active Directory B2C の "サインインしたままにする"
description: Azure Active Directory B2C で "サインインしたままにする (KMSI)" を設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: de5dd051804f3a0a7d1b0d32b998262af13e8926
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389192"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で "サインインしたままにする (KMSI)" を有効にする

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) ディレクトリにローカル アカウントを持つ Web およびネイティブ アプリケーションのユーザーに対して、"サインインしたままにする (KMSI)" 機能を有効にすることができます。 この機能を使用して、ユーザー名とパスワードの再入力を求めることなく、アプリケーションに戻ってきたユーザーにアクセスを許可します。 このアクセスは、ユーザーがサインアウトすると失効します。

公共のコンピューターではこのオプションを有効にしないでください。

![[サインインしたままにする] チェックボックスを示すサインアップ サインイン ページの例](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>前提条件

- ローカル アカウントのサインインを許可するように構成されている Azure AD B2C テナント。 KMSI は、外部 ID プロバイダー アカウントではサポートされていません。
- [カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。

## <a name="configure-the-page-identifier"></a>ページ識別子を設定する

KMSI を有効にするには、コンテンツ定義の `DataUri` 要素を[ページ識別子](contentdefinitions.md#datauri) `unifiedssp` に設定し、[ページ バージョン](page-layout.md)を *1.1.0* 以上に設定します。

1. ポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。 この拡張ファイルは、カスタム ポリシー スターター パックに含まれているポリシー ファイルの 1 つであり、[カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの前提条件の中で、取得済みになっている必要があります。
1. **BuildingBlocks** 要素を検索します。 要素が存在しない場合は追加します。
1. **ContentDefinitions** 要素をポリシーの **BuildingBlocks** 要素に追加します。

    カスタム ポリシーは次のコード スニペットのようになります。

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>メタデータをセルフアサート技術プロファイルに追加する

サインアップおよびサインイン ページに KMSI チェック ボックスを追加するには、`setting.enableRememberMe` メタデータを [true] に設定します。 拡張ファイルで SelfAsserted-LocalAccountSignin-Email 技術プロファイルを上書きします。

1. ClaimsProviders 要素を見つけます。 要素が存在しない場合は追加します。
1. ClaimsProviders 要素に次のクレーム プロバイダーを追加します。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 拡張ファイルを保存します。

## <a name="configure-a-relying-party-file"></a>証明書利用者ファイルを設定する

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. カスタム ポリシー ファイルを開きます。 たとえば、*SignUpOrSignin.xml* などです。
1. まだ存在しない場合は、子ノード `<UserJourneyBehaviors>` を `<RelyingParty>` ノードに追加します。 `<DefaultUserJourney ReferenceId="User journey Id" />` の直後に配置する必要があります。たとえば、`<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` のようにします。
1. 次のノードを `<UserJourneyBehaviors>` 要素の子として追加します。

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **SessionExpiryType** - `SessionExpiryInSeconds` と `KeepAliveInDays` に指定されている時間だけセッションが延長されることを示します。 `Rolling` 値 (既定) の場合、ユーザーが認証を実行するたびにセッションが延長されます。 `Absolute` 値の場合、指定された時間が経過すると、再認証がユーザーに強制されます。

    - **SessionExpiryInSeconds** - *[サインインしたままにする]* が有効になっていないときか、 *[サインインしたままにする]* をユーザーが選択しない場合のセッション Cookie の有効期間。 セッションは `SessionExpiryInSeconds` の経過後かブラウザーを閉じると期限切れとなります。

    - **KeepAliveInDays** - *[サインインしたままにする]* が有効になっており、 *[サインインしたままにする]* をユーザーが選択したときのセッション Cookie の有効期間。  `KeepAliveInDays` の値は `SessionExpiryInSeconds` の値より優先され、セッションの有効期限を示します。 ユーザーがブラウザーを閉じ、後で再度開くと、KeepAliveInDays の時間内であれば、引き続き自動的にサインインできます。

    詳細については、「[UserJourneyBehaviors](relyingparty.md#userjourneybehaviors)」を参照してください。

次の例に示すように、KeepAliveInDays の値は比較的長い期間 (30 日間) に設定できますが、SessionExpiryInSeconds の値は短期間 (1200 秒) に設定することをお勧めします。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-your-policy"></a>ポリシーのテスト

1. 変更内容を保存し、ファイルをアップロードします。
1. アップロードしたカスタムのポリシーをテストするには、Azure portal でポリシー ページに移動し、 **[今すぐ実行]** を選択します。
1. **ユーザー名**と**パスワード**を入力し、 **[Keep me signed in]\(サインインしたままにする\)** を選択して **[sign-in]\(サインイン\)** をクリックします。
1. Azure Portal に戻ります。 ポリシー ページに移動し、 **[コピー]** を選択してサインイン URL をコピーします。
1. ブラウザーのアドレス バーで、`&prompt=login` クエリ文字列パラメーターを削除します。これにより、ユーザーはその要求に対して資格情報を入力するように強制されます。
1. ブラウザーで、 **[Go]\(移動\)** をクリックします。 Azure AD B2C は、再度のサインインを求めることなくアクセス トークンを発行するようになります。 

## <a name="next-steps"></a>次のステップ

サンプル ポリシーは[こちら](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)で確認できます。
