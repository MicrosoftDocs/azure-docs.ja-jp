---
title: イマーシブ リーダー Node.js クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Web アプリを一から構築してイマーシブ リーダー API 機能を追加します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: ffef03457f9d3faf385bd0a924474dfa6efc4121
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102619242"
---
[Immersive Reader](https://www.onenote.com/learningtools) は、新しい読者、言語学習者、ディスレクシア (失読症) などの学習障碍者の読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。 アプリケーションに Immersive Reader を使用すると、テキストを分離することによって、集中しやすくする、よく用いられる単語に画像を表示する、品詞を強調表示する、選択テキストを読み上げる、単語や文章をリアルタイムで翻訳する、といったことができます。

このクイックスタートでは、Web アプリを一から構築し、Immersive Reader クライアント ライブラリを使用して Immersive Reader を統合します。 このクイックスタートの完全なサンプルは [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs) で入手できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](../../how-to-create-immersive-reader.md)に従ってください。 環境のプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。
* [Node.js](https://nodejs.org/) と [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/) などの IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express を使用して Node.js Web アプリを作成する

`express-generator` ツールを使用して Node.js Web アプリを作成します。

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Yarn の依存関係をインストールし、依存関係 `request` と `dotenv` を追加します。これらは、クイックスタートの後の方で使用します。

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="set-up-authentication"></a>認証の設定

### <a name="configure-authentication-values"></a>認証の値の構成

プロジェクトのルートに、 _.env_ という名前の新しいファイルを作成します。 そこに次のコードを貼り付けて、イマーシブ リーダー リソースを作成したときに取得した値を指定します。
引用符や中かっこ ({ }) は含めないでください。

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

### <a name="update-the-router-to-acquire-the-token"></a>トークンを取得するためのルーターの更新
_routes\index.js_ ファイルを開いて、自動的に生成されたコードを次のコードに置き換えます。

このコードは、サービス プリンシパルのパスワードを使用して Azure AD 認証トークンを取得する API エンドポイントを作成します。 さらに、サブドメインも取得します。 その後、そのトークンとサブドメインを含んだオブジェクトを返します。

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**GetTokenAndSubdomain** API エンドポイントを、なんらかの認証形式 ([OAuth](https://oauth.net/2/) など) の背後で保護して、お使いのイマーシブ リーダー サービスと請求に対して使用するトークンを、未承認のユーザーが取得できないようにする必要があります。この作業については、このクイックスタートでは説明していません。

## <a name="add-sample-content"></a>サンプル コンテンツの追加

次に、この Web アプリにサンプル コンテンツを追加します。 _views\index.pug_ を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```


すべてのテキストに、テキストの言語を示す **lang** 属性があることに注意してください。 この属性は、イマーシブ リーダーが適切な言語と文法の機能を提供するために役立ちます。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

これで Web アプリの準備が整いました。 次を実行してアプリを起動します。

```bash
npm start
```

ブラウザーを開き、 _http://localhost:3000_ に移動します。 次のように表示されます。

![サンプル アプリ - Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>イマーシブ リーダーの起動

[イマーシブ リーダー] ボタンをクリックすると、イマーシブ リーダーが起動し、ページのコンテンツが表示されます。

![Immersive Reader - Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [リソースの作成と AAD の構成](../../how-to-create-immersive-reader.md)