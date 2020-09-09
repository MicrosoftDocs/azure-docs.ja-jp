---
title: カスタム ポリシーを使用してアプリのユーザー インターフェイスをカスタマイズする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でカスタム ポリシーを使用してユーザー インターフェイスをカスタマイズする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb833ff35dae4fe1c0c27204ec66fa6b4cdb82c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388886"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事の手順を完了して、独自のブランドと外観を持つカスタム ポリシーへのサインアップとサインインを作成します。 Azure Active Directory B2C (Azure AD B2C) を使用すると、ユーザーに表示される HTML および CSS コンテンツをほぼ完全に制御できます。 カスタム ポリシーを使用する場合、UI のカスタマイズは Azure Portal のコントロールではなく XML を使用して構成します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4.拡張ファイルを変更する

UI のカスタマイズを構成するには、**ContentDefinition** とその子要素を基本ファイルから拡張ファイルにコピーします。

1. ポリシーの基本ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em>です。 この基本ファイルは、カスタム ポリシー スターター パックに含まれているポリシー ファイルの 1 つであり、[カスタム ポリシーの概要](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)に関するページの前提条件の中で、取得済みになっている必要があります。
1. **ContentDefinitions** 要素を検索し、その内容全体をコピーします。
1. 拡張ファイルを開きます。 たとえば、*TrustFrameworkExtensions.xml* です。 **BuildingBlocks** 要素を検索します。 要素が存在しない場合は追加します。
1. コピーした **ContentDefinitions** 要素の内容全体を **BuildingBlocks** 要素の子として貼り付けます。
1. コピーした XML で `Id="api.signuporsignin"` を含む **ContentDefinition** 要素を検索します。
1. **LoadUri** の値を、ストレージにアップロードした HTML ファイルの URL に変更します。 たとえば、「 `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html` 」のように入力します。

    カスタム ポリシーは次のコード スニペットのようになります。

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 拡張ファイルを保存します。

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5.更新されたカスタム ポリシーをアップロードしてテストする

#### <a name="51-upload-the-custom-policy"></a>5.1 カスタム ポリシーをアップロードする

1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. **Azure AD B2C** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択します。
1. 前に変更した拡張ファイルをアップロードします。

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 **[今すぐ実行]** を使用してカスタム ポリシーをテストする

1. アップロードしたポリシーを選択し、 **[今すぐ実行]** を選択します。
1. メール アドレスを使用してサインアップできることを確認します。

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>動的なカスタム ページ コンテンツ URI を構成する

Azure AD B2C カスタム ポリシーを使用して、URL パスまたはクエリ文字列内でパラメーターを送信することができます。 パラメーターを HTML エンドポイントに渡すことで、ページのコンテンツを動的に変更することができます。 たとえば、Web またはモバイル アプリケーションから渡すパラメーターに基づいて、Azure AD B2C サインアップまたはサインイン ページの背景イメージを変更できます。 パラメーターには、アプリケーション ID、言語 ID、`campaignId` のようなカスタム クエリ文字列パラメーターなど、任意の[要求リゾルバー](claim-resolver-overview.md)を指定できます。

### <a name="sending-query-string-parameters"></a>クエリ文字列パラメーターの送信

クエリ文字列パラメーターを送信するには、[証明書利用者ポリシー](relyingparty.md)内で、次に示すように `ContentDefinitionParameters` 要素を追加します。

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

コンテンツ定義内で、`LoadUri` の値を `https://<app_name>.azurewebsites.net/home/unified` に変更します。 カスタム ポリシー `ContentDefinition` は、次のコード スニペットのようになります。

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C によってページが読み込まれるときに、Web サーバー エンドポイントの呼び出しが行われます。

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>動的ページ コンテンツ URI

使用されるパラメータ―に基づいて、さまざまな場所からコンテンツを取得できます。 CORS 対応エンドポイントでは、コンテンツをホストするようにフォルダー構造を設定します。 たとえば、次の構造でコンテンツを整理することができます。 ルートの "*フォルダー/言語ごとのフォルダー/お使いの html ファイル*" です。 たとえば、カスタム ページ URI は次のようになります。

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C では、その言語に対して 2 文字の ISO コードを送信します。フランス語であれば、`fr` です。

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>次のステップ

カスタマイズできる UI 要素の詳細については、[ユーザー フローの UI のカスタマイズに関するリファレンス ガイド](customize-ui-overview.md)を参照してください。
