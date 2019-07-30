---
title: チュートリアル:Node.js を使用して Immersive Reader を起動する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、イマーシブ リーダーを起動する Node.js アプリケーションを作成します。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f8697042ed46e0ff333f736454346908d76cf039
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718382"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>チュートリアル:Immersive Reader の起動 (Node.js)

[概要](./overview.md)に関するページでは、イマーシブ リーダーの機能とそのしくみ (どのようにして言語学習者、新しい読者、学習障碍者の読解力向上のために実証済みの手法を実装するか) について説明しました。 このチュートリアルでは、イマーシブ リーダーを起動する Node.js Web アプリケーションの作成方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Express を使用して Node.js Web アプリを作成する
> * アクセス トークンの取得
> * イマーシブ リーダーを起動してサンプル コンテンツを表示する
> * コンテンツの言語を指定する
> * イマーシブ リーダーのインターフェイスの言語を指定する
> * イマーシブ リーダーを起動して数学コンテンツを表示する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* イマーシブ リーダーのサブスクリプション キー。 [こちらの手順](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に従って入手してください。
* [Node.js](https://nodejs.org/) と [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/) などの IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express を使用して Node.js Web アプリを作成する

`express-generator` ツールを使用して Node.js Web アプリを作成します。

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Yarn の依存関係をインストールし、依存関係 `request` と `dotenv` を追加します。これは、チュートリアルの後半で使用します。

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>アクセス トークンの取得

次に、サブスクリプション キーを使用してアクセス トークンを取得する バックエンド API を記述します。 この次の手順では、サブスクリプション キーとエンドポイントが必要です。 サブスクリプション キーは、Azure portal のイマーシブ リーダー リソースの [キー] ページで確認できます。 エンドポイントは、[概要] ページで確認できます。

サブスクリプション キーとエンドポイントを確認したら、 _.env_ という新しいファイルを作成し、そこに次のコードを貼り付けます。`{YOUR_SUBSCRIPTION_KEY}` と `{YOUR_ENDPOINT}` はそれぞれ、ご自分のサブスクリプション キーとエンドポイントに置き換えてください。

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

このファイルは、公開するべきでない機密情報を含んでいるため、ソース管理にはコミットしないでください。

次に、_app.js_ を開いて、ファイルの先頭に次を追加します。 これにより、サブスクリプション キーとエンドポイントが環境変数として Node に読み込まれます。

```javascript
require('dotenv').config();
```

_routes\index.js_ ファイルを開いて、ファイルの上部に次の import を追加します。

```javascript
var request = require('request');
```

次に、この行の直後に次のコードを追加します。 このコードにより、サブスクリプション キーを使用してアクセス トークンを取得し、その後そのトークンを返す API エンドポイントが作成されます。

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

この API エンドポイントは、何らかの認証 ([OAuth](https://oauth.net/2/) など) で保護する必要があります。この作業はこのチュートリアルの対象外です。

## <a name="launch-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

1. _views\layout.pug_ を開いて、`head` タグと `body` タグの間に次のコードを追加します。 これらの `script` タグによって、[Immersive Reader SDK](https://github.com/Microsoft/immersive-reader-sdk) と jQuery が読み込まれます。

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. _views\index.pug_ を開いて、その内容を次のコードに置き換えます。 このコードによって、ページにサンプル コンテンツが表示され、イマーシブ リーダーを起動するボタンが追加されます。

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. これで Web アプリの準備が整いました。 次を実行してアプリを起動します。

    ```bash
    npm start
    ```

4. ブラウザーを開き、 _http://localhost:3000_ に移動します。 ページに上記のコンテンツが表示されます。 **[イマーシブ リーダー]** ボタンをクリックすると、イマーシブ リーダーが起動し、コンテンツが表示されます。

## <a name="specify-the-language-of-your-content"></a>コンテンツの言語を指定する

イマーシブ リーダーでは、さまざまな言語がサポートされています。 次の手順に従って、コンテンツの言語を指定できます。

1. _views\index.pug_ を開き、前の手順で追加した `p(id=content)` タグの下に次のコードを追加します。 このコードによって、ページにスペイン語のコンテンツが追加されます。

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. JavaScript コードでは、`ImmersiveReader.launchAsync` への呼び出しの前に次を追加します。 このコードによって、スペイン語のコンテンツがイマーシブ リーダーに渡されます。

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. 再度 _http://localhost:3000_ に移動します。 ページにスペイン語のテキストが表示されます。 **[イマーシブ リーダー]** をクリックすると、それがイマーシブ リーダーにも表示されます。

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>イマーシブ リーダーのインターフェイスの言語を指定する

既定では、イマーシブ リーダーのインターフェイスの言語は、ブラウザーの言語設定と一致します。 次のコードを使用して、イマーシブ リーダーのインターフェイスの言語を指定することもできます。

1. _views\index.pug_ で、`ImmersiveReader.launchAsync(token, content)` への呼び出しを下のコードに置き換えます。

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. _http://localhost:3000_ に移動します。 イマーシブ リーダーを起動すると、インターフェイスがフランス語で表示されます。

## <a name="launch-the-immersive-reader-with-math-content"></a>イマーシブ リーダーを起動して数学コンテンツを表示する

[MathML](https://developer.mozilla.org/en-US/docs/Web/MathML) を使用して、イマーシブ リーダーに数学コンテンツを含めることができます。

1. _views\index.pug_ を編集し、`ImmersiveReader.launchAsync` への呼び出しの前に次のコードを含めます。

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. _http://localhost:3000_ に移動します。 イマーシブ リーダーを起動して一番下までスクロールすると、数式を確認できます。

## <a name="next-steps"></a>次の手順

* [Immersive Reader SDK](https://github.com/Microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp) でコード サンプルを見る