---
title: Azure Active Directory B2C でのユーザー インターフェイスのカスタマイズについて | Microsoft Docs
description: Azure Active Directory B2C を使用するアプリケーションのユーザー インターフェイスをカスタマイズする方法について説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 767e64d4d53702ede7b55edc747366ab3d32ae4d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996101"
---
# <a name="about-user-interface-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でのユーザー インターフェイスのカスタマイズについて

Azure Active Directory (Azure AD) B2C によってアプリケーションに提供されるユーザー インターフェイス (UI) をカスタマイズしたり、ブランド名を付けたりする機能は、一貫性のある操作性を顧客に与えるために重要です。 そのような操作性には、サインアップ、サインイン、プロファイル編集、パスワード リセットが含まれます。 この記事では、アプリケーションの UI カスタマイズに役立つ情報を提供します。

これらの操作性のニーズに基づき、さまざまな方法でアプリケーションの UI をカスタマイズします。 例: 

- [ユーザー フロー](active-directory-b2c-reference-policies.md)を使用してサインアップ、サインイン、パスワード リセット、プロファイル編集といった操作を提供している場合、[Azure portal を使用して UI をカスタマイズ](tutorial-customize-ui.md)します。
- v2 のユーザー フローを使用している場合は、[ページ レイアウト テンプレート](#page-layout-templates)を使用して、さらにカスタマイズを行うことなく、ユーザー フロー ページの外観を変更できます。 たとえば、オーシャン ブルーやスレート グレーのテーマをユーザー フローのすべてのページに適用できます。
- サインインのみ、それに付随するパスワード リセット ページ、確認メールを提供している場合、[Azure AD サインイン ページ](../active-directory/fundamentals/customize-branding.md)の場合と同じカスタマイズ手順を使用します。
- 顧客がサインイン前にプロファイルを編集しようとすると、Azure AD サインイン ページのカスタマイズに使用した同じ手順でカスタマイズするページにリダイレクトされます。
- [カスタム ポリシー](active-directory-b2c-overview-custom.md)を使用してサインアップ、サインイン、パスワード リセット、プロファイル編集をアプリケーションで提供している場合、[ポリシー ファイルを使用して UI をカスタマイズ](active-directory-b2c-ui-customization-custom.md)します。
- 顧客の決定に基づく動的コンテンツを提供する必要がある場合、クエリ文字列で送信されたパラメーターに基づいて[ページの内容を変更できるカスタム ポリシー](active-directory-b2c-ui-customization-custom-dynamic.md)を使用します。 たとえば、Web やモバイル アプリケーションから渡されるパラメーターに基づき、Azure AD B2C のサインアップまたはサインイン ページの背景イメージが変化します。
- Azure AD B2C の[ユーザー フロー](user-flow-javascript-overview.md)または[カスタム ポリシー](page-contract.md)で、JavaScript のクライアント側コードを有効にすることができます。

Azure AD B2C によって、顧客のブラウザーでコードが実行され、[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) と呼ばれる最新の手法が使用されます。 実行時、コンテンツは、ユーザー フローまたはポリシーで指定された URL から読み込まれます。 URL はページごとに指定します。 URL から読み込まれたコンテンツが Azure AD B2C から挿入された HTML フラグメントに統合され、顧客に表示されます。

独自の HTML ファイルと CSS ファイルを使用して UI をカスタマイズするときは、始める前に次のガイダンスを確認してください。

- Azure AD B2C によって HTML コンテンツがページに統合されます。 Azure AD B2C によって提供される既定のコンテンツをコピーしたり、変更したりしないでください。 HTML コンテンツは最初から構築し、既定のコンテンツはあくまで参考としての利用にとどめることをお勧めします。
- カスタム コンテンツに JavaScript を含めることができるようになりました。
- サポートされているブラウザーのバージョン: 
    - Internet Explorer 11、10、Microsoft Edge
    - Internet Explorer 8 と 9 (サポートに制限あり)
    - Google Chrome 42.0 以降
    - Mozilla Firefox 38.0 以降
- HTML に form タグが含まれていないことを確認してください。含まれている場合、Azure AD B2C から挿入された HTML によって生成された POST 操作が妨げられます。

## <a name="page-layout-templates"></a>ページ レイアウト テンプレート

v2 のユーザー フローでは、既定のページの見た目をよくし、独自のカスタマイズの適切なベースとして使用できる、デザイン済みのテンプレートを選択できます。

左側のメニューで、**[カスタマイズ]** の **[ページ レイアウト]** を選択します。 次に、**[テンプレート (プレビュー)]** を選択します。

![ページ レイアウト テンプレートを選択する](media/customize-ui-overview/template.png)

一覧からテンプレートを選択します。 たとえば、**[オーシャン ブルー]** テンプレートでは、次のレイアウトがユーザー フロー ページに適用されます。

![オーシャン ブルー テンプレート](media/customize-ui-overview/ocean-blue.png)

テンプレートを選択すると、選択したレイアウトがユーザー フローのすべてのページに適用されて、各ページの URI が **[カスタム ページ URI]** フィールドに表示されます。

## <a name="where-do-i-store-ui-content"></a>UI コンテンツはどこに保存しますか。

独自の HTML ファイルと CSS ファイルを使用して UI をカスタマイズするときは、[Azure BLOB ストレージ](../storage/blobs/storage-blobs-introduction.md)、Web サーバー、CDN、AWS S3、ファイル共有システムなど、任意の場所で UI コンテンツをホストできます。 重要な点は、CORS が有効になっている公開 HTTPS エンドポイントでコンテンツがホストされていることです。 コンテンツに指定するとき、絶対 URL を使用する必要があります。

## <a name="how-do-i-get-started"></a>開始するには?

UI をカスタマイズするには次を行います。

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

- (CORS が許可されている) HTTPS エンドポイントでコンテンツをホストします。 CORS を構成するときに、GET と OPTIONS の両方の要求メソッドを有効にする必要があります。
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

- 作成したコンテンツを使用するポリシーを作成するか、あるいはそのようにポリシーを編集します。

コンテンツに配置されている `<div id="api"></div>` 要素に Azure AD B2C によって統合される HTML フラグメントをまとめたのが次の表です。

| 挿入されるページ | HTML の説明 |
| ------------- | ------------------- |
| ID プロバイダーの選択 | サインアップ時やサインイン時に顧客が選択できる ID プロバイダーのボタン一覧が含まれます。 これらのボタンには、Facebook や Google などのソーシャル ID プロバイダーとローカル アカウント (電子メール アドレスやユーザー名を使用するもの) が含まれます。 |
| ローカル アカウントのサインアップ | メール アドレスまたはユーザー名に基づいたローカル アカウントのサインアップ用のフォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなどのさまざまな入力コントロールを含めることができます。 |
| ソーシャル アカウントのサインアップ | Facebook や Google+ などのソーシャル ID プロバイダーの既存のアカウントを使用してサインアップするときに表示できます。 サインアップ フォームを使用して顧客から追加情報を収集する必要があるときに使用されます。 |
| 統合されたサインアップまたはサインイン | 顧客のサインアップとサインインの両方を処理します。顧客は、Facebook や Google などのソーシャル ID プロバイダーを使用することも、ローカル アカウントを使用することもできます。 |
| 多要素認証 | 顧客がサインアップやサインインをするときに、電話番号を (文字や音声を使用して) 確認できます。 |
| Error | エラー情報を顧客に提供します。 |


## <a name="how-do-i-localize-content"></a>コンテンツをローカライズする方法は?

Azure AD B2C テナントで [[言語のカスタマイズ]](active-directory-b2c-reference-language-customization.md) を有効にすることで HTML コンテンツをローカライズします。 この機能を有効にすると、Azure AD B2C が Open ID Connect パラメーター `ui-locales` をエンドポイントに転送できるようになります。 コンテンツ サーバーではこのパラメーターを使用し、言語固有の HTML ページを提供できます。

使用されているロケールに基づき、さまざまな場所からコンテンツを取得できます。 CORS 対応エンドポイントでは、特定の言語のコンテンツをホストするようにフォルダー構造を設定します。 ワイルドカード値 {Culture:RFC5646} を使用すると、適切な言語が呼び出されます。 たとえば、カスタム ページ URI は `https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html` のようになります。 `https://contoso.blob.core.windows.net/fr/myHTML/unified.html` からコンテンツを取得すれば、フランス語でページを読み込むことができます。

## <a name="examples"></a>例

カスタマイズ サンプルが必要であれば、[こちら](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)からサンプル テンプレート ファイルをダウンロードしてご覧ください。

## <a name="next-steps"></a>次の手順

- ユーザー フローを使用している場合は、次のチュートリアルで UI のカスタマイズを開始できます:「[Azure Active Directory B2C でアプリケーションのユーザー インターフェイスをカスタマイズする](tutorial-customize-ui.md)」。
- カスタム ポリシーを使用している場合は、次の記事で UI のカスタマイズを開始できます:「[Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](active-directory-b2c-ui-customization-custom.md)」。

