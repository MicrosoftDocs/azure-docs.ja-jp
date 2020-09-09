---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "80116952"
---
## <a name="use-custom-page-content"></a>カスタム ページ コンテンツを使用する

ページ UI のカスタマイズ機能を使用することで、任意のカスタム ポリシーの外観をカスタマイズできます。 さらに、アプリケーションと Azure AD B2C との間で、ブランドと視覚的な一貫性を維持することもできます。

### <a name="how-it-works"></a>しくみ

Azure AD B2C では、[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) を使用して、顧客のブラウザーでコードが実行されます。 実行時に、コンテンツは、ユーザー フローまたはカスタム ポリシーで指定された URL から読み込まれます。 ユーザー エクスペリエンスの各ページでは、そのページに指定した URL からコンテンツが読み込まれます。 URL から読み込まれたコンテンツが Azure AD B2C によって挿入された HTML フラグメントに統合され、ページが顧客に表示されます。

![カスタム ページ コンテンツの余白](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>カスタム HTML ページ コンテンツ

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>既定の Azure AD B2C ページをカスタマイズする

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

独自の HTML ファイルと CSS ファイルを使用して UI をカスタマイズする場合は、CORS をサポートする、公開されている任意の HTTPS エンドポイントで UI コンテンツをホストします。 たとえば、[Azure Blob Storage](../articles/storage/blobs/storage-blobs-introduction.md)、[Azure App Services](/azure/app-service/)、Web サーバー、CDN、AWS S3、またはファイル共有システムなどです。

## <a name="guidelines-for-using-custom-page-content"></a>カスタム ページ コンテンツを使用するためのガイドライン

- メディア、CSS、JavaScript ファイルなどの外部リソースを HTML ファイルに含める場合は、絶対 URL を使用します。
- [ページ レイアウト バージョン](../articles/active-directory-b2c/page-layout.md) 1.2.0 以降を使用すると、HTML タグに `data-preload="true"` 属性を追加して、CSS と JavaScript の読み込み順序を制御できます。 `data-preload=true` では、ページはユーザーに表示される前に構築されます。 この属性は、スタイルが設定されていない HTML がユーザーに表示されることなく、CSS ファイルをプリロードすることでページが "ちらつく" のを防ぐのに役立ちます。 次の HTML コード スニペットは、`data-preload` タグの使用方法を示しています。
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 既定のページ コンテンツから開始し、その上に構築することをお勧めします。
- [ユーザー フロー](../articles/active-directory-b2c/user-flow-javascript-overview.md)と[カスタム ポリシー](../articles/active-directory-b2c/javascript-samples.md)のいずれの場合も、カスタム コンテンツに JavaScript を含めることができます。
- サポートされているブラウザーのバージョン:
  - Internet Explorer 11、10、Microsoft Edge
  - Internet Explorer 8 と 9 (サポートに制限あり)
  - Google Chrome 42.0 以降
  - Mozilla Firefox 38.0 以降
  - iOS および macOS 用 Safari、バージョン 12 以降
- セキュリティの制限により、Azure AD B2C では `frame`、`iframe`、`form` HTML 要素はサポートされません。

## <a name="custom-page-content-walkthrough"></a>カスタム ページ コンテンツのチュートリアル

プロセスの概要を以下に示します。

1. カスタム ページ コンテンツをホストするための場所を準備します (パブリックにアクセスできる、CORS が有効な HTTPS エンドポイント)。
1. `unified.html` など、既定のページ コンテンツ ファイルをダウンロードしてカスタマイズします。
1. 公開されている HTTPS エンドポイントでカスタム ページ コンテンツを公開します。
1. Web アプリのクロスオリジン リソース共有 (CORS) を設定します。
1. ポリシーがカスタム ポリシー コンテンツ URI を指すようにします。

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

1. コピーしたスニペットをテキスト エディターに貼り付け、*customize-ui.html* という名前を付けてファイルを保存します。

> [!NOTE]
> login.microsoftonline.com を使用すると、セキュリティ上の制限により HTML の form 要素が削除されます。 カスタム HTML コンテンツで HTML の form 要素を使用する場合は、[b2clogin.com を使用](../articles/active-directory-b2c/b2clogin.md)します。

### <a name="2-create-an-azure-blob-storage-account"></a>2.Azure BLOB ストレージ アカウントを作成する

この記事では、Azure Blob Storage を使用してコンテンツをホストします。 コンテンツを Web サーバーにホストすることもできますが、[Web サーバーで CORS を有効にする](https://enable-cors.org/server.html)必要があります。

HTML コンテンツを Blob Storage でホストするには、次の手順を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **ハブ** メニューで、 **[新規]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順にクリックします。
1. ストレージ アカウントの**サブスクリプション**を選択します。
1. **リソース グループ**を作成するか、既存のリソース グループを選択します。
1. ストレージ アカウント用に一意の**名前**を入力します。
1. ストレージ アカウントの**地理的な場所**を選択します。
1. **[デプロイ モデル]** は **[リソース マネージャー]** のまま残してかまいません。
1. **[パフォーマンス]** は **[標準]** のまま残してかまいません。
1. **[Account Kind]** (アカウントの種類) を **[Blob Storage]** に変更します。
1. **[レプリケーション]** は **[RA-GRS]** のまま残してかまいません。
1. **[アクセス レベル]** は **[ホット]** のまま残してかまいません。
1. **[確認および作成]** を選択して、ストレージ アカウントを作成します。
    デプロイが完了したら、**ストレージ アカウント**のページが自動的に開きます。

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
1. **[リモート URL]** ボックスに HTML ファイルの URL を貼り付けます。 たとえば、`https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html` のように指定します。
1. **[要求の送信]** を選択します。
    結果は `XHR status: 200` となるはずです。 
    エラーが発生した場合は、CORS の設定が正しいことを確認します。 場合によっては、ブラウザーのキャッシュをクリアするか、Ctrl + Shift + P キーを押してプライベート ブラウズ セッションを開く必要もあります。
