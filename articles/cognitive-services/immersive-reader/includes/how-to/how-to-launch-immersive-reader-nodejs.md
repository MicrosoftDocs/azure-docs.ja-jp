---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 1b0d929e667023640906baa87a2fd25c61c5d488
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620300"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](../../how-to-create-immersive-reader.md)に従ってください。  環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
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

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 認証トークンを取得する

次に、Azure AD 認証トークンを取得するバックエンド API を記述します。

この部分については、上の Azure AD 認証構成の前提条件の手順におけるいくつかの値が必要です。 そのセッションで保存したテキスト ファイルを参照します。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

これらの値が用意できたら、_.env_ という名前の新しいファイルを作成し、そこに次のコードを貼り付けて、上記のカスタム プロパティ値を指定します。 引用符や中かっこ ({ }) は含めないでください。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

このファイルは、公開するべきでない機密情報を含んでいるため、ソース管理にはコミットしないでください。

次に、_app.js_ を開いて、ファイルの先頭に次を追加します。 これにより、.env ファイル内に定義したプロパティが環境変数として Node に読み込まれます。

```javascript
require('dotenv').config();
```

_routes\index.js_ ファイルを開いて、その内容を次のコードに置き換えます。

このコードは、サービス プリンシパルのパスワードを使用して Azure AD 認証トークンを取得する API エンドポイントを作成します。 さらに、サブドメインも取得します。 その後、そのトークンとサブドメインを含んだオブジェクトを返します。

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

**getimmersivereaderlaunchparams** API エンドポイントはなんらかの形式の認証 ([OAuth](https://oauth.net/2/) など) の背後で保護して、未承認のユーザーがトークンを取得し、ご使用のイマーシブ リーダー サービスと請求に対して使用できないようにする必要があります。この作業は、このチュートリアルの範囲を超えています。

## <a name="launch-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

1. _views\layout.pug_ を開いて、`head` タグと `body` タグの間に次のコードを追加します。 これらの `script` タグによって、[Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と jQuery が読み込まれます。

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
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

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
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

3. 再度 _http://localhost:3000_ に移動します。 ページにスペイン語のテキストが表示されます。**[イマーシブ リーダー]** をクリックすると、それがイマーシブ リーダーにも表示されます。

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>イマーシブ リーダーのインターフェイスの言語を指定する

既定では、イマーシブ リーダーのインターフェイスの言語は、ブラウザーの言語設定と一致します。 次のコードを使用して、イマーシブ リーダーのインターフェイスの言語を指定することもできます。

1. _views\index.pug_ で、`ImmersiveReader.launchAsync(token, subdomain, content)` への呼び出しを下のコードに置き換えます。

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
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

## <a name="next-steps"></a>次のステップ

* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](../../reference.md)を探索する
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) でコード サンプルを見る
