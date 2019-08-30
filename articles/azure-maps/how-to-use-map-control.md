---
title: Azure Maps の Web マップ コントロールの概要 | Microsoft Docs
description: Azure Maps のマップ コントロール クライアント側 JavaScript ライブラリを使用する方法について説明します。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: fa6a3af7893830eba2f4a5c43184991bff68d8a8
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898203"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Maps のマップ コントロールを使用する

マップ コントロール クライアント側 JavaScript ライブラリを使用すると、マップと埋め込まれた Azure Maps の機能を、Web アプリケーションまたはモバイル アプリケーションにレンダリングできます。

## <a name="create-a-new-map-in-a-web-page"></a>Web ページに新しいマップを作成する

マップ コントロール クライアント側 JavaScript ライブラリを使って、Web ページにマップを埋め込むことができます。

1. 新しい HTML ファイルを作成します。

2. Azure Maps Web SDK に読み込みます。 これは、次に示す 2 つのオプションのどちらかを使用して行うことができます。

    a. ファイルの `<head>` 要素内のスタイル シートとスクリプトの参照に URL エンドポイントを追加することで、グローバルにホストされる CDN バージョンの Azure Maps Web SDK を使用します。

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. または、[azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM パッケージを使用して、Azure Maps Web SDK のソース コードをローカルに読み込み、アプリを使用してホストします。 このパッケージには TypeScript 定義も含まれています。

    > npm install azure-maps-control

    次に、Azure Maps のスタイル シートとスクリプト ソースの参照を、ファイルの `<head>` 要素に追加します。

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. ページの本文全体を埋めるようにマップをレンダリングするには、次の `<style>` 要素を `<head>` 要素に追加します。

    ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. ページの本文で、`<div>` 要素を追加して **myMap** の `id` を指定します。

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. マップ コントロールを初期化するには、html 本文に新しいセクションを定義し、スクリプトを作成します。 `Map` クラスのインスタンスを作成するときに、最初のパラメーターとしてマップ `<div>` の `id` または `HTMLElement` (`document.getElementById('myMap')` など) を渡します。 [認証オプション](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)を使用してマップを認証するには、固有の Azure Maps アカウント キーまたは Azure Active Directory (AAD) 資格情報を使用します。 アカウントを作成するか、キーを確認する必要がある場合は、「[Azure Maps のアカウントとキーを管理する方法](how-to-manage-account-keys.md)」を参照してください。 **language** オプションには、マップ ラベルとコントロールに使用される言語を指定します。 サポートされている言語の詳細については、[サポートされている言語](supported-languages.md)に関するページを参照してください。 サブスクリプション キーを使用して認証する場合は、次のようになります。

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Azure Active Directory (AAD) を使用して認証する場合は、次のようになります。

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
    ```

    詳細については、「[Azure Maps による認証](azure-maps-authentication.md)」ドキュメントを参照してください。

6. 必要に応じて、ページの見出しに次のメタ タグ要素を追加すると役立つ場合があります。

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. すべてをまとめると、HTML ファイルは次のようなコードになります。

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Web ブラウザーでファイルを開き、レンダリングされたマップを表示します。 これは次のようなコードになります。

    <iframe height="700" style="width: 100%;" scrolling="no" title="マップ コントロールの使用方法" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true"><a href='https://codepen.io'>CodePen</a> の Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) による Pen 「<a href='https://codepen.io/azuremaps/pen/yZpEYL/'>How to use the map control (マップ コントロールの使用方法)</a>」を参照してください。
    </iframe>

## <a name="localizing-the-map"></a>マップのローカライズ

Azure Maps には、マップの言語と地域ビューを設定するための 2 つの異なる方法が用意されています。 最初のオプションでは、この情報をグローバルな `atlas` 名前空間に追加します。これにより、アプリ内のすべてのマップ コントロール インスタンスにより、これらの設定が既定で使用されます。 以下では、言語をフランス語 ("fr-FR") に、地域ビューを "auto" に設定します。

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

2 つ目のオプションでは、次のようにマップを読み込むときに、この情報をマップ オプションに渡します。

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> Web SDK を使用すると、言語と地域の設定が異なる複数のマップ インスタンスを同じページに読み込むことができます。 また、これらの設定は、マップの `setStyle` 関数を使用してマップが読み込まれた後に更新することができます。 

言語が "fr-FR" に設定され、地域ビューが "auto" に設定されている Azure Maps の例を次に示します。

![ラベルをフランス語で表示するマップ イメージ](./media/how-to-use-map-control/websdk-localization.png)

サポートされている言語と地域ビューの完全な一覧については、[こちら](supported-languages.md)をご覧ください。

## <a name="next-steps"></a>次の手順

マップを作成して操作する方法について説明します。

> [!div class="nextstepaction"]
> [マップを作成する](map-create.md)

マップにスタイルを設定する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [マップ スタイルを選択する](choose-map-style.md)

マップにさらにデータを追加するには:

> [!div class="nextstepaction"]
> [マップを作成する](map-create.md)

> [!div class="nextstepaction"]
> [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-maps)