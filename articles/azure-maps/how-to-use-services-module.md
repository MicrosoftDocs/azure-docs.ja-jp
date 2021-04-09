---
title: Azure Maps サービス モジュールの使用 | Microsoft Azure Maps
description: Azure Maps サービス モジュールについて学習します。 Web または Node.js アプリケーションで Azure Maps REST サービスにアクセスするには、このヘルパー ライブラリを読み込んで使用する方法を参照してください。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008601"
---
# <a name="use-the-azure-maps-services-module"></a>Azure Maps サービス モジュールの使用

Azure Maps の Web SDK は、*サービス モジュール* を提供します。 このモジュールは、Web または Node.js アプリケーションから JavaScript または TypeScript を使用して簡単に Azure Maps REST サービスを使用できるようにするヘルパー ライブラリです。

## <a name="use-the-services-module-in-a-webpage"></a>Web ページでサービス モジュールの使用

1. 新しい HTML ファイルを作成します。
1. Azure Maps サービス モジュールの読み込み 次の 2 つの方法のいずれかで読み込みます。
    - グローバルにホストされている Azure Maps サービス モジュールの Azure Content Delivery Network のバージョンを使用します。 次に、ファイルの `<head>` 要素にスクリプト参照を追加します。

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - または、[azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) NPM パッケージを使用して、Azure Maps Web SDK ソース コード用のサービス モジュールをローカルに読み込み、アプリを使用してそれをホストします。 このパッケージには TypeScript 定義も含まれています。 次のコマンドを実行します。
    
        > **npm install azure-maps-rest**
    
        次に、ファイルの `<head>` 要素にスクリプト参照を追加します。

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. 認証パイプラインを作成します。 サービス URL のクライアント エンドポイントを初期化するには、事前にパイプラインを作成しておく必要があります。 Azure Maps 検索サービス クライアントを認証するには、自身の Azure Maps アカウント キーまたは Azure Active Directory (Azure AD) 資格情報を使用します。 この例では、検索サービス URL クライアントが作成されます。 

    サブスクリプション キーを使用して認証する場合は、次のようになります。

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Azure AD を使用して認証する場合は、次のようになります。

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    詳細については、「[Azure Maps による認証](azure-maps-authentication.md)」を参照してください。

1. 次のコードでは、新しく作成された Azure Maps Search サービス URL クライアントを使用してアドレスをジオコードで表現します。"1 Microsoft Way, Redmond, WA" コードを使用して、`searchAddress`関数をページ本文内のテーブルとして結果を表示します。

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    完全に動作するコード サンプルを次に示します。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="サービス モジュールを使用する" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a> 上で、Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による<a href='https://codepen.io/azuremaps/pen/zbXGMR/'>サービス モジュールを使用する</a>ペンを表示します。
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure Government クラウドのサポート

Azure Maps Web SDK では、Azure Government クラウドがサポートされています。 Azure Maps Web SDK へのアクセスに使用する JavaScript および CSS の URL はすべて同じままです。ただし、Azure Maps プラットフォームの Azure Government クラウド バージョンに接続するには、次のタスクを実行する必要があります。

対話型マップ コントロールを使用する場合は、`Map` クラスのインスタンスを作成する前に、次のコード行を追加します。 

```javascript
atlas.setDomain('atlas.azure.us');
```

マップとサービスを認証するときは、Azure Government クラウド プラットフォームからの Azure Maps 認証の詳細を必ず使用してください。

サービス モジュールを使用する場合は、API URL エンドポイントのインスタンスを作成する際にサービス用のドメインを設定する必要があります。 たとえば、次のコードでは `SearchURL` クラスのインスタンスが作成され、ドメインが Azure Government クラウドを指すようにされます。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure Maps REST サービスに直接アクセスする場合は、URL ドメインを `atlas.azure.us` に変更します。 たとえば、検索 API サービスを使用する場合は、URL ドメインを `https://atlas.microsoft.com/search/` から `https://atlas.azure.us/search/` に変更します。

## <a name="next-steps"></a>次のステップ

この記事で使われているクラスとメソッドの詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

サービス モジュールを使用した他のコード サンプルについては、次の記事をご覧ください。

> [!div class="nextstepaction"]
> [マップに検索結果を表示する](./map-search-location.md)

> [!div class="nextstepaction"]
> [座標から情報を取得する](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A から B までのルートを表示する ](./map-route.md)