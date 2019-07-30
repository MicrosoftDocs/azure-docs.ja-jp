---
title: Node.js と VS Code を使用した複数の依存サービスの実行
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/21/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.openlocfilehash: 136d48f1c420ac71896eaafa0daab476c7fba6fa
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503076"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したマルチサービスの開発

このチュートリアルでは、Azure Dev Spaces を使用してマルチサービス アプリケーションを開発する方法と、Dev Spaces が提供するいくつかの追加の利点について学習します。

## <a name="call-a-service-running-in-a-separate-container"></a>別のコンテナーで実行されているサービスを呼び出す

このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードを開く
`samples` という名前のフォルダーに、このガイドの `mywebapi` のサンプル コードが既にあります (ない場合は、 https://github.com/Azure/dev-spaces に移動し、 **[Clone or download]** をクリックして GitHub リポジトリをダウンロードしてください)。このセクションのコードは、`samples/nodejs/getting-started/mywebapi` にあります。

### <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の VS Code ウィンドウ*" で、`mywebapi` フォルダーを開きます。
1. ( **[表示 | コマンド パレット]** メニューを使用して) **コマンド パレット**を開き、オート コンプリートを使用して次のコマンドを入力および選択します: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 このコマンドと、デプロイ用にプロジェクトを構成する `azds prep` コマンドを混同しないでください。
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、"*Listening on port 80 (ポート 80 をリッスンしています)* " というメッセージがデバッグ コンソールに表示されます。
1. エンドポイント URL を書き留めます。これは、`http://localhost:<portnumber>` のように表示されます。 **ヒント: VS Code のステータス バーがオレンジ色に変わり、クリック可能な URL が表示されます。** コンテナーはローカルで実行されているように見えるかもしれませんが、実際には Azure の開発環境で実行されています。 localhost アドレスである理由は、`mywebapi` でパブリック エンドポイントが定義されておらず、Kubernetes インスタンス内からしかアクセスできないためです。 利便性を考慮し、また、ローカル コンピューターからのプライベート サービスとの対話を容易にするために、Azure Dev Spaces では、Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されます。
1. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開きます。 `mywebapi` サービスからの応答 ("Hello from mywebapi") が表示されます。


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。
1. `webfrontend` の VS Code ウィンドウに切り替えます。
1. `server.js` の先頭に次のコード行を追加します。
    ```javascript
    var request = require('request');
    ```

3. `/api` GET ハンドラーのコードを*置き換えます*。 要求を処理すると、`mywebapi` が呼び出され、両方のサービスからの結果が返されます。

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. `server.js` の最後の行 `server.close()` の*削除*

前述のコード例は、`azds-route-as` ヘッダーを受信要求から送信要求に転送します。 これがチームによる共同開発にどのように役立つかについては、後ほど説明します。

### <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. 既定の GET `/` ハンドラー内にブレークポイントを設定します ([`server.js` の 8 行目](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8))。
1. `webfrontend` プロジェクトで、GET 要求を `http://mywebapi` に送信する直前にブレークポイントを設定します。
1. `webfrontend` プロジェクトで F5 キーを押します。
1. Web アプリを開き、両方のサービスでコードをステップ実行します。 Web アプリには、2 つのサービスによって連結されたメッセージ "Hello from webfrontend and Hello from mywebapi" が表示されます。

### <a name="well-done"></a>お疲れさまでした。
これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Dev Spaces でのチーム開発について学ぶ](team-development-nodejs.md)
