---
title: ユーザー インターフェイスをカスタマイズする
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C を使用するアプリケーションのユーザー インターフェイスをカスタマイズする方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d737d010f323a5d5b230091ad07ba530d25d6e51
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949408"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 内のユーザー インターフェイスをカスタマイズする

Azure Active Directory B2C (Azure AD B2C) に表示されるユーザー インターフェイスを顧客に合わせてブランド化したりカスタマイズすることは、アプリケーション内でシームレスなユーザー エクスペリエンスを提供するのに役立ちます。 そのような操作性には、サインアップ、サインイン、プロファイル編集、パスワード リセットが含まれます。 この記事では、ユーザー フローとカスタム ポリシーの両面から、ユーザー インターフェイス (UI) のカスタマイズ方法について説明します。

## <a name="ui-customization-in-different-scenarios"></a>さまざまなシナリオにおける UI のカスタマイズ

アプリケーションの UI をカスタマイズする方法はいくつかあり、どれを使用するかはシナリオに応じて決まります。

### <a name="user-flows"></a>ユーザー フロー

[ユーザー フロー](active-directory-b2c-reference-policies.md)を使用する場合は、組み込みの *ページ レイアウト テンプレート*を使用するか、独自の HTML と CSS を使用して、ユーザー フロー ページの外観を変更できます。 これらの各方法については、後で説明します。

ユーザー フローの UI カスタマイズを構成するには、[Azure portal](tutorial-customize-ui.md) を使用します。

### <a name="custom-policies"></a>カスタム ポリシー

[カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用してサインアップ、サインイン、パスワード リセット、プロファイル編集をアプリケーションで提供している場合、[ポリシー ファイルを使用して UI をカスタマイズ](active-directory-b2c-ui-customization-custom.md)します。

顧客の決定に基づく動的コンテンツを提供する必要がある場合、クエリ文字列で送信されたパラメーターに基づいて[ページの内容を動的に変更](active-directory-b2c-ui-customization-custom-dynamic.md)できるカスタム ポリシーを使用します。 たとえば、Web またはモバイル アプリケーションから渡すパラメーターに基づいて、Azure AD B2C サインアップまたはサインイン ページの背景イメージを変更できます。

### <a name="javascript"></a>JavaScript

クライアント側の JavaScript コードは、[ユーザー フロー](user-flow-javascript-overview.md)と[カスタム ポリシー](page-layout.md)の両方で有効にすることができます。

### <a name="sign-in-only-ui-customization"></a>サインイン専用 UI のカスタマイズ

サインインのみ、それに付随するパスワード リセット ページ、確認メールを提供している場合、[Azure AD サインイン ページ](../active-directory/fundamentals/customize-branding.md)の場合と同じカスタマイズ手順を使用します。

顧客がサインイン前にプロファイルを編集しようとすると、Azure AD サインイン ページのカスタマイズに使用した同じ手順でカスタマイズするページにリダイレクトされます。

## <a name="page-layout-templates"></a>ページ レイアウト テンプレート

ユーザー フローにはいくつかの組み込みテンプレートが用意されています。これらを使用すると、ユーザー エクスペリエンスのページにプロフェッショナルな外観を提供できます。 これらのレイアウト テンプレートは、独自のカスタマイズの開始点としても使用できます。

左側のメニューの **[カスタマイズ]** で、 **[ページ レイアウト]** を選択し、 **[テンプレート]** を選択します。

![Azure portal のユーザー フロー ページのテンプレート選択ドロップダウン](media/customize-ui-overview/template-selection.png)

次に、一覧からテンプレートを選択します。 次に示すのは、各テンプレートのサインイン ページの例です。

| オーシャン ブルー | スレート グレー | クラシック |
|:-:|:-:|:-:|
|![サインアップ サインイン ページ上に表示される、オーシャン ブルー テンプレートの例](media/customize-ui-overview/template-ocean-blue.png)|![サインアップ サインイン ページ上に表示される、スレート グレー テンプレートの例](media/customize-ui-overview/template-slate-gray.png)|![サインアップ サインイン ページ上に表示される、クラシック テンプレートの例](media/customize-ui-overview/template-classic.png)|

テンプレートを選択すると、選択したレイアウトがユーザー フローのすべてのページに適用されて、各ページの URI が **[カスタム ページ URI]** フィールドに表示されます。

## <a name="custom-html-and-css"></a>カスタム HTML および CSS

Azure AD B2C では、[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) と呼ばれる手法を使用して、顧客のブラウザーでコードが実行されます。

実行時、コンテンツは、ユーザー フローまたはカスタム ポリシーで指定された URL から読み込まれます。 ユーザー エクスペリエンスの各ページでは、そのページに指定した URL からコンテンツが読み込まれます。 URL から読み込まれたコンテンツが Azure AD B2C によって挿入された HTML フラグメントに統合され、ページが顧客に表示されます。

独自の HTML ファイルと CSS ファイルを使用して UI をカスタマイズする前に、次のガイダンスを確認してください。

- Azure AD B2C によって HTML コンテンツがページに**統合**されます。 Azure AD B2C によって提供される既定のコンテンツをコピーしたり、変更したりしないでください。 HTML コンテンツは最初から構築し、既定のコンテンツはあくまで参考としての利用にとどめることをお勧めします。
- **JavaScript**  は、[ ユーザー フロー](user-flow-javascript-overview.md)と[カスタム ポリシー](javascript-samples.md)のいずれの場合も、カスタム コンテンツに含めることができます。
- サポートされている**ブラウザーのバージョン**:
  - Internet Explorer 11、10、Microsoft Edge
  - Internet Explorer 8 と 9 (サポートに制限あり)
  - Google Chrome 42.0 以降
  - Mozilla Firefox 38.0 以降
- HTML には **form タグ**を含めないでください。 form タグがあると、Azure AD B2C によって挿入された HTML によって生成される POST 操作が妨げられます。

### <a name="where-do-i-store-ui-content"></a>UI コンテンツはどこに保存しますか。

独自の HTML ファイルと CSS ファイルを使用して UI をカスタマイズする場合、CORS をサポートしている、パブリックに使用可能な任意の HTTPS エンドポイントで UI コンテンツをホストできます。 たとえば、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)、Web サーバー、CDN、AWS S3、またはファイル共有システムなどです。

重要な点は、CORS が有効になっている公開 HTTPS エンドポイントでコンテンツがホストされていることです。 コンテンツに指定するとき、絶対 URL を使用する必要があります。

## <a name="get-started-with-custom-html-and-css"></a>カスタム HTML および CSS の概要

ユーザー エクスペリエンス ページで独自の HTML と CSS の使用を開始する際には、次のガイドラインに従ってください。

- 整形された HTML コンテンツを作成し、空の `<div id="api"></div>` 要素を `<body>` の任意の場所に配置します。 この要素は、Azure AD B2C コンテンツを挿入する位置をマークします。 次に最小限のページの例を示します。

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- CSS を使用して、Azure AD B2C によってページに挿入される UI 要素のスタイルを設定します。 サインアップで挿入される HTML 要素の設定も含まれる単純な CSS ファイルの例を次に示します。

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

- (CORS が許可されている) HTTPS エンドポイントでコンテンツをホストします。 CORS を構成するときに、GET と OPTIONS の両方の要求メソッドを有効にする必要があります。
- 作成したコンテンツを使用するユーザー フローまたはカスタム ポリシーを作成するか、あるいはそのようにポリシーを編集します。

### <a name="html-fragments-from-azure-ad-b2c"></a>Azure AD B2C からの HTML フラグメント

コンテンツに配置されている `<div id="api"></div>` 要素に Azure AD B2C によって統合される HTML フラグメントをまとめたのが次の表です。

| 挿入されるページ | HTML の説明 |
| ------------- | ------------------- |
| ID プロバイダーの選択 | サインアップ時やサインイン時に顧客が選択できる ID プロバイダーのボタン一覧が含まれます。 これらのボタンには、Facebook や Google などのソーシャル ID プロバイダーとローカル アカウント (電子メール アドレスやユーザー名を使用するもの) が含まれます。 |
| ローカル アカウントのサインアップ | メール アドレスまたはユーザー名に基づいたローカル アカウントのサインアップ用のフォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなどのさまざまな入力コントロールを含めることができます。 |
| ソーシャル アカウントのサインアップ | Facebook や Google+ などのソーシャル ID プロバイダーの既存のアカウントを使用してサインアップするときに表示できます。 サインアップ フォームを使用して顧客から追加情報を収集する必要があるときに使用されます。 |
| 統合されたサインアップまたはサインイン | 顧客のサインアップとサインインの両方を処理します。顧客は、Facebook や Google などのソーシャル ID プロバイダーを使用することも、ローカル アカウントを使用することもできます。 |
| 多要素認証 | 顧客がサインアップやサインインをするときに、電話番号を (文字や音声を使用して) 確認できます。 |
| Error | エラー情報を顧客に提供します。 |

## <a name="localize-content"></a>コンテンツのローカライズ

Azure AD B2C テナントで [[言語のカスタマイズ]](active-directory-b2c-reference-language-customization.md) を有効にすることで HTML コンテンツをローカライズします。 この機能を有効にすると、Azure AD B2C で OpenID Connect パラメーター `ui-locales` をエンドポイントに転送できるようになります。 コンテンツ サーバーではこのパラメーターを使用し、言語固有の HTML ページを提供できます。

使用されているロケールに基づき、さまざまな場所からコンテンツを取得できます。 CORS 対応エンドポイントでは、特定の言語のコンテンツをホストするようにフォルダー構造を設定します。 ワイルドカード値 `{Culture:RFC5646}` を使うと、適切な言語が呼び出されます。

たとえば、カスタム ページ URI は次のようになります。

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

コンテンツを以下から取得すれば、フランス語でページを読み込むことができます。

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>例

サンプル テンプレート ファイルは、GitHub の [B2C-AzureBlobStorage-Client](https://github.com/azureadquickstarts/b2c-azureblobstorage-client) リポジトリにあります。

テンプレート内のサンプル HTML および CSS ファイルは、[/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates) ディレクトリにあります。

## <a name="next-steps"></a>次の手順

- **ユーザー フロー**を使用している場合は、次のチュートリアルで UI のカスタマイズを開始できます:

    「[Azure Active Directory B2C でアプリケーションのユーザー インターフェイスをカスタマイズする](tutorial-customize-ui.md)」。
- **カスタム ポリシー**を使用している場合は、次の記事で UI のカスタマイズを開始できます:

    「[Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](active-directory-b2c-ui-customization-custom.md)」。
