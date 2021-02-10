---
title: HTML テンプレートを使用したユーザー インターフェイスのカスタマイズ
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションのユーザー インターフェイスを、HTML テンプレートを使用してカスタマイズする方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/28/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 78ad2540029d78084485ae2004194f9f7c2d6052
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050548"
---
# <a name="customize-the-user-interface-with-html-templates-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で HTML テンプレートを使用してユーザー インターフェイスをカスタマイズする

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) に表示されるユーザー インターフェイスを顧客に合わせてブランド化したりカスタマイズすることは、アプリケーション内でシームレスなユーザー エクスペリエンスを提供するのに役立ちます。 そのような操作性には、サインアップ、サインイン、プロファイル編集、パスワード リセットが含まれます。 この記事では、ユーザー インターフェイス (UI) のカスタマイズ方法について説明します。 

> [!TIP]
> ユーザー フロー ページのバナー ロゴ、背景画像、背景色のみを変更する場合は、[会社のブランド](customize-ui.md)機能を試します。

## <a name="custom-html-and-css-overview"></a>カスタム HTML および CSS の概要

Azure AD B2C では、[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を使用して、顧客のブラウザーでコードが実行されます。 実行時に、コンテンツは、ユーザー フローまたはカスタム ポリシーで指定された URL から読み込まれます。 ユーザー エクスペリエンスの各ページでは、そのページに指定した URL からコンテンツが読み込まれます。 URL から読み込まれたコンテンツが Azure AD B2C によって挿入された HTML フラグメントに統合され、ページが顧客に表示されます。

![カスタム ページ コンテンツの余白](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>カスタム HTML ページ コンテンツ

独自のブランドを使用して HTML ページを作成し、カスタム ページ コンテンツを提供します。 このページは、静的な `*.html` ページ、または .NET、Node.js、PHP などの動的ページにすることができます。

カスタム ページ コンテンツには、CSS や JavaScript などの HTML 要素を含めることができますが、iframe などの安全ではない要素を含めることはできません。 唯一の必須要素は、`id` が `api` に設定された div 要素です。たとえば、HTML ページ内の `<div id="api"></div>` などです。

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>既定の Azure AD B2C ページをカスタマイズする

カスタム ページ コンテンツを最初から作成する代わりに、Azure AD B2C の既定のページ コンテンツをカスタマイズすることができます。

次の表に、Azure AD B2C によって提供される既定のページ コンテンツの一覧を示します。 ファイルをダウンロードして、独自のカスタム ページを作成するための開始点としてご利用ください。

| 既定のページ | 説明 | コンテンツ定義 ID<br/>(カスタム ポリシーのみ) |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **エラー ページ**。 このページは、例外またはエラーが発生した場合に表示されます。 | *api.error* |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **セルフアサート ページ**。 このファイルは、ソーシャル アカウントのサインアップ ページ、ローカル アカウントのサインアップ ページ、ローカル アカウントのサインイン ページ、パスワードのリセットなどのカスタム ページ コンテンツとして使用します。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなど、さまざまな入力コントロールを含めることができます。 | *api.localaccountsignin*、*api.localaccountsignup*、*api.localaccountpasswordreset*、*api.selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多要素認証ページ**。 このページでは、ユーザーがサインアップまたはサインインするときに、(文字または音声を使用して) 電話番号を確認することができます。 | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **プロファイルの更新ページ**。 このページには、ユーザーがプロファイルを更新する場合にアクセスできるフォームが含まれます。 このページは、パスワード入力フィールドを除いて、ソーシャル アカウントのサインアップ ページに似ています。 | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統合されたサインアップ ページまたはサインイン ページ**。 このページでは、ユーザーのサインアップおよびサインイン プロセスを処理します。 ユーザーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかを使用できます。 | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>ページ コンテンツのホスト

独自の HTML ファイルと CSS ファイルを使用して UI をカスタマイズする場合は、CORS をサポートする、公開されている任意の HTTPS エンドポイントで UI コンテンツをホストします。 たとえば、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)、[Azure App Services](../app-service/index.yml)、Web サーバー、CDN、AWS S3、またはファイル共有システムなどです。

## <a name="guidelines-for-using-custom-page-content"></a>カスタム ページ コンテンツを使用するためのガイドライン

- メディア、CSS、JavaScript ファイルなどの外部リソースを HTML ファイルに含める場合は、絶対 URL を使用します。
- [ページ レイアウト バージョン](page-layout.md) 1.2.0 以降を使用すると、HTML タグに `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できます。 `data-preload="true"` では、ページはユーザーに表示される前に構築されます。 この属性は、スタイルが設定されていない HTML がユーザーに表示されることなく、CSS ファイルをプリロードすることでページが "ちらつく" のを防ぐのに役立ちます。 次の HTML コード スニペットは、`data-preload` タグの使用方法を示しています。
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 既定のページ コンテンツから開始し、その上に構築することをお勧めします。
- カスタム コンテンツに [JavaScript を含める](javascript-and-page-layout.md)ことができます。
- サポートされているブラウザーのバージョン:
  - Internet Explorer 11、10、Microsoft Edge
  - Internet Explorer 8 と 9 (サポートに制限あり)
  - Google Chrome 42.0 以降
  - Mozilla Firefox 38.0 以降
  - iOS および macOS 用 Safari、バージョン 12 以降
- セキュリティの制限により、Azure AD B2C では `frame`、`iframe`、`form` HTML 要素はサポートされません。

## <a name="localize-content"></a>コンテンツのローカライズ

Azure AD B2C テナントで [[言語のカスタマイズ]](language-customization.md) を有効にすることで HTML コンテンツをローカライズします。 この機能を有効にすると、Azure AD B2C で OpenID Connect パラメーター `ui_locales` をエンドポイントに転送できるようになります。 コンテンツ サーバーではこのパラメーターを使用し、言語固有の HTML ページを提供できます。

使用されているロケールに基づき、さまざまな場所からコンテンツを取得できます。 CORS 対応エンドポイントでは、特定の言語のコンテンツをホストするようにフォルダー構造を設定します。 ワイルドカード値 `{Culture:RFC5646}` を使うと、適切な言語が呼び出されます。

たとえば、カスタム ページ URI は次のようになります。

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

コンテンツを以下から取得すれば、フランス語でページを読み込むことができます。

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>カスタム ページ コンテンツのチュートリアル

プロセスの概要を以下に示します。

1. カスタム ページ コンテンツをホストするための場所を準備します (パブリックにアクセスできる、CORS が有効な HTTPS エンドポイント)。
1. `unified.html` など、既定のページ コンテンツ ファイルをダウンロードしてカスタマイズします。
1. 公開されている HTTPS エンドポイントでカスタム ページ コンテンツを公開します。
1. Web アプリのクロスオリジン リソース共有 (CORS) を設定します。
1. ポリシーがカスタム ポリシー コンテンツ URI を指すようにします。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

### <a name="1-create-your-html-content"></a>1.HTML コンテンツを作成する

タイトルに製品のブランド名を使用したカスタム ページ コンテンツを作成します。

1. 次の HTML スニペットをコピーします。 これは、 *\<body\>* タグ内に配置される、 *\<div id="api"\>\</div\>* と呼ばれる空の要素を持つ整形式の HTML5です。 この要素は、Azure AD B2C コンテンツが挿入される場所を示します。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. コピーしたスニペットをテキスト エディターに貼り付けます
1. CSS を使用して、Azure AD B2C によってページに挿入される UI 要素のスタイルを設定します。 サインアップで挿入される HTML 要素の設定も含まれる単純な CSS ファイルの例を次に示します。

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  *customize-ui.html* としてファイルを保存します。

> [!NOTE]
> login.microsoftonline.com を使用すると、セキュリティ上の制限により HTML の form 要素が削除されます。 カスタム HTML コンテンツで HTML の form 要素を使用する場合は、[b2clogin.com を使用](b2clogin.md)します。

### <a name="2-create-an-azure-blob-storage-account"></a>2.Azure BLOB ストレージ アカウントを作成する

この記事では、Azure Blob Storage を使用してコンテンツをホストします。 コンテンツを Web サーバーにホストすることもできますが、[Web サーバーで CORS を有効にする](https://enable-cors.org/server.html)必要があります。

HTML コンテンツを Blob Storage でホストするには、次の手順を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **ハブ** メニューで、 **[新規]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順にクリックします。
1. ストレージ アカウントの **サブスクリプション** を選択します。
1. **リソース グループ** を作成するか、既存のリソース グループを選択します。
1. ストレージ アカウント用に一意の **名前** を入力します。
1. ストレージ アカウントの **地理的な場所** を選択します。
1. **[デプロイ モデル]** は **[リソース マネージャー]** のまま残してかまいません。
1. **[パフォーマンス]** は **[標準]** のまま残してかまいません。
1. **[Account Kind]** (アカウントの種類) を **[Blob Storage]** に変更します。
1. **[レプリケーション]** は **[RA-GRS]** のまま残してかまいません。
1. **[アクセス レベル]** は **[ホット]** のまま残してかまいません。
1. **[確認および作成]** を選択して、ストレージ アカウントを作成します。
    デプロイが完了したら、**ストレージ アカウント** のページが自動的に開きます。

#### <a name="21-create-a-container"></a>2.1 コンテナーを作成する

Blob ストレージ内にパブリック コンテナーを作成するには、次の手順を実行します。

1. 左側のメニューの **[Blob service]** で **[BLOB]** を選択します。
1. **[+ コンテナー]** を選択します。
1. **[名前]** に「*root*」と入力します。 名前には任意の名前 (*contoso* など) を指定できますが、この例ではわかりやすくするために *root* を使用します。
1. **[パブリック アクセス レベル]** で **[BLOB]** を選択し、 **[OK]** を選択します。
1. **[root]** を選択して、新しいコンテナーを開きます。

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 カスタム ページ コンテンツ ファイルをアップロードする

1. **[アップロード]** を選択します。
1. **[ファイルの選択]** の横にあるフォルダー アイコンを選択します。
1. 先ほど「ページ UI のカスタマイズ」セクションで作成した **customize-ui.html** に移動し、それを選択します。
1. サブフォルダーにアップロードする場合、 **[詳細設定]** を展開し、 **[アップロード先のフォルダー]** にフォルダー名を入力します。
1. **[アップロード]** を選択します。
1. アップロードした **customize-ui.html** BLOB を選択します。
1. **[URL]** テキスト ボックスの右側にある **[クリップボードにコピー]** アイコンを選択して、URL をクリップボードにコピーします。
1. Web ブラウザーで、コピーした URL に移動して、アップロードした BLOB にアクセスできることを確認します。 アクセスできない場合 (たとえば、`ResourceNotFound` エラーが発生した場合)、コンテナーのアクセスの種類が **[BLOB]** に設定されていることを確認します。

### <a name="3-configure-cors"></a>3.CORS を構成する

次の手順を実行して、クロス オリジン リソース共有用に Blob ストレージを構成します。

1. メニューで **[CORS]** を選択します。
1. **[許可されるオリジン]** には、`https://your-tenant-name.b2clogin.com` を入力します。 `your-tenant-name`を Azure AD B2C テナントの名前に置き換えます。 たとえば、「 `https://fabrikam.b2clogin.com` 」のように入力します。 テナント名を入力するときは、すべて小文字を使用します。
1. **[許可されたメソッド]** に、`GET` と `OPTIONS` を両方選択します。
1. **[許可されたヘッダー]** に、アスタリスク (*) を入力します。
1. **[公開されるヘッダー]** に、アスタリスク (*) を入力します。
1. **[最長有効期間]** には「200」と入力します。
1. **[保存]** を選択します。

#### <a name="31-test-cors"></a>3.1 CORS をテストする

準備が整ったことを検証するには、次の手順を実行します。

1. CORS の構成手順を繰り返します。 **[許可されたオリジン]** には、「`https://www.test-cors.org`」を入力します。
1. [www.test-cors.org](https://www.test-cors.org/) に移動します。 
1. **[リモート URL]** ボックスに HTML ファイルの URL を貼り付けます。 たとえば、`https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html` のように指定します。
1. **[要求の送信]** を選択します。
    結果は `XHR status: 200` となるはずです。 
    エラーが発生した場合は、CORS の設定が正しいことを確認します。 場合によっては、ブラウザーのキャッシュをクリアするか、Ctrl + Shift + P キーを押してプライベート ブラウズ セッションを開く必要もあります。

::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4.ユーザー フローを更新する

1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択し、*B2C_1_signupsignin1* ユーザー フローを選択します。
1. **[ページ レイアウト]** を選択し、 **[統合されたサインアップまたはサインイン ページ]** の下で、 **[カスタム コンテンツの使用]** に対して **[はい]** を選択します。
1. **[カスタム ページ URI]** に、先に記録しておいた *custom-ui.html* ファイルの URL を入力します。
1. ページの最上部で **[保存]** を選択します。

### <a name="5-test-the-user-flow"></a>5.ユーザー フローをテストする

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択し、*B2C_1_signupsignin1* ユーザー フローを選択します。
1. ページの上部にある **[ユーザー フローを実行します]** をクリックします。
1. **[ユーザー フローを実行します]** ボタンをクリックします。

次の例のような、作成した CSS ファイルに基づいて要素が配置されているページが表示されるはずです。

![サインアップまたはサインイン ページとカスタム UI 要素を示している Web ブラウザー](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4.拡張ファイルを変更する

UI のカスタマイズを構成するには、**ContentDefinition** とその子要素を基本ファイルから拡張ファイルにコピーします。

1. ポリシーの基本ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>です。 この基本ファイルは、カスタム ポリシー スターター パックに含まれているポリシー ファイルの 1 つであり、[カスタム ポリシーの概要](./custom-policy-get-started.md)に関するページの前提条件の中で、取得済みになっている必要があります。
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

::: zone-end

## <a name="sample-templates"></a>サンプル テンプレート

ここでは、UI カスタマイズのサンプル テンプレートを示します。

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

このプロジェクトには、次のテンプレートが含まれています。
- [オーシャン ブルー](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [スレート グレー](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

サンプルを使用するには、次のようにします。

1. ローカル コンピューターにリポジトリを複製します。 テンプレート フォルダーの `/ocean_blue` または `/slate_gray` を選択します。
1. 前のセクションの説明に従い、テンプレート フォルダーと `/assets` フォルダーの下にあるすべてのファイルを Blob Storage にアップロードします。
1. 次に、`/ocean_blue` または `/slate_gray` のルートにある各 `\*.html` ファイルを開き、相対 URL のすべての出現箇所を、手順 2 でアップロードした css、images、および fonts ファイルの URL に置き換えます。 例:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    目的
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. `\*.html` ファイルを保存し、それらを Blob Storage にアップロードします。
1. ここで、前述のように、HTML ファイルを指すようにポリシーを変更します。
1. 欠落しているフォント、イメージ、または CSS がある場合は、拡張ポリシーおよび \*.html ファイルでお使いの参照を確認してください。

## <a name="use-company-branding-assets-in-custom-html"></a>カスタム HTML で会社のブランド資産を使用する

カスタム HTML で[会社のブランド](customize-ui.md#configure-company-branding)資産を使用するには、`<div id="api">` タグの外側に次のタグを追加します。 画像ソースが背景画像とバナー ロゴの画像に置き換えられます。

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

## <a name="next-steps"></a>次のステップ

[クライアント側の JavaScript コード](javascript-and-page-layout.md)を有効にする方法を確認します。