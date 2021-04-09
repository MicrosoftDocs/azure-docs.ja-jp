---
title: 複数のイマーシブ リーダー リソースを統合する
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、複数のイマーシブ リーダー リソースを使用して、イマーシブ リーダーを起動する Node.js アプリケーションを作成します。
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 01/14/2020
ms.author: skamal
ms.custom: devx-track-js
ms.openlocfilehash: dc6c7bd9e38aa21ab10f55fca2500b0c12194d43
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620292"
---
# <a name="integrate-multiple-immersive-reader-resources"></a>複数のイマーシブ リーダー リソースを統合する

[概要](./overview.md)に関するページでは、イマーシブ リーダーの機能とそのしくみ (どのようにして言語学習者、新しい読者、および学習方法の異なる学生が読解力向上のために実証済みの手法を実装するか) について説明しました。 [クイックスタート](./quickstarts/client-libraries.md)では、単一のリソースでイマーシブ リーダーを使用する方法について説明しました。 このチュートリアルでは、複数のイマーシブ リーダー リソースを同じアプリケーションに統合する方法について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 既存のリソース グループの下に複数のイマーシブ リーダー リソースを作成する
> * 複数のリソースを使用してイマーシブ リーダーを起動する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

* [クイックスタート](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)に従い、NodeJS を使用してイマーシブ リーダーを起動する Web アプリを作成します。 クイックスタートでは、1 つのイマーシブ リーダー リソースを構成します。 このチュートリアルでは、それをベースにして構築を行います。

## <a name="create-the-immersive-reader-resources"></a>イマーシブ リーダー リソースを作成する

[こちらの手順](./how-to-create-immersive-reader.md)に従って、各イマーシブ リーダー リソースを作成します。 **Create-ImmersiveReaderResource** スクリプトには、`ResourceName`、`ResourceSubdomain`、および `ResourceLocation` がパラメーターとして含まれています。 これらは、作成するリソースごとに一意である必要があります。 残りのパラメーターは、最初のイマーシブ リーダー リソースを設定したときに使用したものと同じにする必要があります。 これにより、各リソースを同じ Azure リソース グループおよび Azure AD アプリケーションにリンクすることができます。

次の例は、2 つのリソースを作成する方法を示しています。1 つは WestUS に、もう 1 つは EastUS に作成されます。 `ResourceName`、`ResourceSubdomain`、および `ResourceLocation` には一意の値を指定する点に注意してください。

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>環境構成にリソースを追加する

クイックスタートでは、`TenantId`、`ClientId`、`ClientSecret`、および `Subdomain` のパラメーターを含む環境構成ファイルを作成しました。 すべてのリソースで同じ Azure AD アプリケーションが使用されるため、`TenantId`、`ClientId`、および `ClientSecret` には同じ値を使用できます。 リソースごとに各サブドメインを指定する点のみが、唯一必要な変更箇所です。

新しい __.env__ ファイルは次のようになります。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

このファイルは、公開するべきでない機密情報を含んでいるため、ソース管理にはコミットしないでください。

次に、複数のリソースをサポートできるように、作成済みの _routes\index.js_ ファイルを変更します。 その内容を次のコードで置き換えます。

以前のとおり、このコードでは、サービス プリンシパルのパスワードを使用して Azure AD 認証トークンを取得する API エンドポイントが作成されます。 今回は、ユーザーがリソースの場所を指定し、それをクエリ パラメーターとして渡すことができるようにします。 次に、トークンおよび対応するサブドメインを含むオブジェクトが返されます。

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

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

**getimmersivereaderlaunchparams** API エンドポイントはなんらかの形式の認証 ([OAuth](https://oauth.net/2/) など) の背後で保護して、未承認のユーザーがトークンを取得し、ご使用のイマーシブ リーダー サービスと請求に対して使用できないようにする必要があります。この作業は、このチュートリアルの範囲を超えています。

## <a name="launch-the-immersive-reader-with-sample-content"></a>イマーシブ リーダーを起動してサンプル コンテンツを表示する

1. _views\index.pug_ を開いて、その内容を次のコードに置き換えます。 このコードによって、ページにサンプル コンテンツが表示され、イマーシブ リーダーを起動する 2 つのボタンが追加されます。 1 つは EastUS リソースのイマーシブ リーダーを起動するためのもので、もう 1 つは WestUS リソース用です。

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
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

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
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
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

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
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

3. これで Web アプリの準備が整いました。 次を実行してアプリを起動します。

    ```bash
    npm start
    ```

4. ブラウザーを開き、`http://localhost:3000` に移動します。 ページに上記のコンテンツが表示されます。 **[EastUS Immersive Reader]\(EastUS イマーシブ リーダー\)** ボタンまたは **[WestUS Immersive Reader]\(WestUS イマーシブ リーダー\)** ボタンをクリックし、それぞれのリソースを使用してイマーシ ブリーダーを起動します。

## <a name="next-steps"></a>次のステップ

* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp) でコード サンプルを見る