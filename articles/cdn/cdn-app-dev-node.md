---
title: Azure CDN SDK for Node.js の概要 | Microsoft Docs
description: Azure CDN のプロファイルとエンドポイントの作成と管理を自動化する方法を示すシンプルな Node.js コンソール アプリケーションを作成する方法について説明します。
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: f5d5c7a6e1f6993b19f38db2ae846b213a1d553e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993385"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN 開発の概要
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

CDN プロファイルとエンドポイントの作成と管理は、 [Azure CDN SDK for Node.js](https://www.npmjs.com/package/azure-arm-cdn) を使用して自動化できます。  このチュートリアルでは、単純な Node.js コンソール アプリケーションを作成しながら、使用可能な操作のいくつかを紹介します。  このチュートリアルは、Azure CDN SDK for Node.js のすべての側面を詳細に説明することを目的としていません。

このチュートリアルに取り組む前に、[Node.js](https://www.nodejs.org) **4.x.x** 以降をインストールし、構成を済ませておく必要があります。  Node.js アプリケーションの作成には、好きなテキスト エディターを使用してかまいません。  このチュートリアルは [Visual Studio Code](https://code.visualstudio.com)を使って執筆されています。  

> [!TIP]
> [このチュートリアルに沿って作成されたプロジェクト](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) は MSDN からダウンロードできます。
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>プロジェクトの作成と NPM の依存関係の追加
これまでの手順で、CDN プロファイルのリソース グループを作成し、そのグループ内の CDN プロファイルとエンドポイントを管理するための Azure AD アプリケーション アクセス許可を割り当てました。この段階で、アプリケーションの作成を開始できます。

アプリケーションの保存先となるフォルダーを作成します。  Node.js のツールが置かれているパスのコンソールから、この新しいフォルダーに移動して次のコマンドを実行し、プロジェクトを初期化します。

```console
npm init
```

プロジェクトを初期化するための一連の指定項目が表示されます。  このチュートリアルでは、 **entry point** に *app.js* を使用します。  その他の項目については、次のスクリーンショットを参照してください。

![NPM init output](./media/cdn-app-dev-node/cdn-npm-init.png)

プロジェクトが *packages.json* ファイルで初期化されました。  このプロジェクトでは、NPM パッケージに含まれているいくつかの Azure ライブラリを使用します。  使用するのは、Azure Client Runtime for Node.js (ms-rest-azure) と Azure CDN Client Library for Node.js (azure-arm-cd) です。  それらを依存関係としてプロジェクトに追加しましょう。

```console
npm install --save ms-rest-azure
npm install --save azure-arm-cdn
```

パッケージのインストールが完了した後の *package.json* ファイルは次のようになります (バージョン番号は異なる場合があります)。

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

最後にテキスト エディターを使用して空のテキスト ファイルを作成し、プロジェクト フォルダーのルートに *app.js* という名前で保存します。  コードを記述する準備ができました。

## <a name="requires-constants-authentication-and-structure"></a>ライブラリの読み込み、定数、認証、構造
テキスト エディターで *app.js* を開き、プログラムの基本構造を記述していきましょう。

1. まず NPM パッケージの "require" を次のように追加します。
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. 次に、メソッドで使用するいくつかの定数を定義します。  以降の内容を追加してください。  **&lt;山かっこ&gt;** などのプレースホルダーは、必要に応じて自分の環境に合わせて置き換えます。
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. 次に、CDN 管理クライアントをインスタンス化し、必要な資格情報を割り当てます。
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    個別ユーザー認証を使用する場合は、この 2 行のコードが多少異なります。
   
   > [!IMPORTANT]
   > このコード サンプルは、サービス プリンシパルの代わりに個別ユーザー認証を行う場合にのみ使用してください。  個々のユーザーの資格情報は気を付けて保護し、第三者に知られないようにしてください。
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    **&lt;山かっこ&gt;** 内の項目は、正しい情報に置き換えてください。  `<redirect URI>`には、アプリケーションを Azure AD に登録するときに入力したリダイレクト URI を使用します。
4. Node.js コンソール アプリケーションには、いくつかのコマンド ライン パラメーターがあります。  少なくとも 1 つのパラメーターが渡されたことを検証しましょう。
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. ここからがプログラムの核となる部分です。渡されたパラメーターに応じて、他の関数に分岐します。
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. プログラム内のいくつかの場所で、適切な数のパラメーターが渡されたことを確認しなければなりません。正しくないようであれば何らかのヒントを表示する必要があります。  そのための関数を作成しましょう。
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. CDN 管理クライアントで使用する関数は非同期的に実行されるので、その完了時にコールバックするメソッドが必要となります。  CDN 管理クライアントからの出力 (ある場合) を表示し、プログラムを適切に終了できるようにしましょう。
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

これで、プログラムの基本的な構造が作成されました。次に、パラメーターに基づいて呼び出される関数を作成する必要があります。

## <a name="list-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントのリストの取得
最初に、既存のプロファイルとエンドポイントをリストするコードを作成します。  コード コメントは、各パラメーターの流れを把握しやすいように正しい構文を示しています。

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの作成
次に、プロファイルとエンドポイントを作成するための関数を作成します。

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>エンドポイントの消去
エンドポイントが作成されたと仮定して、プログラムで実行する一般的なタスクの 1 つに、エンドポイントの内容を消去することがあります。

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN プロファイルとエンドポイントの削除
最後に、エンドポイントとプロファイルを削除する関数を追加します。

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>プログラムの実行
これで、使い慣れたデバッガーやコンソールを使用して Node.js プログラムを実行できる状態になりました。

> [!TIP]
> Visual Studio Code をデバッガーとして使用する場合、コマンド ライン パラメーターを渡すための設定を環境に対して行う必要があります。  Visual Studio Code では、この設定を **launch.json** ファイルで行います。  **args** という名前のプロパティを探して、パラメーターの一連の文字列値を追加します (例: `"args": ["list", "profiles"]`)。
> 
> 

まず、プロファイルを一覧表示してみましょう。

![List profiles](./media/cdn-app-dev-node/cdn-list-profiles.png)

空の配列が返されました。  リソース グループにはプロファイルがまったく存在しないので、これは正しい動作です。  早速プロファイルを作成しましょう。

![Create profile](./media/cdn-app-dev-node/cdn-create-profile.png)

次にエンドポイントを追加します。

![エンドポイントを作成する](./media/cdn-app-dev-node/cdn-create-endpoint.png)

最後に、プロファイルを削除します。

![Delete profile](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>次の手順
このチュートリアルの完成したプロジェクトを確認するには、 [サンプルをダウンロード](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)してください。

Azure CDN SDK for Node.js のリファレンスは、 [こちら](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/)でご覧いただけます。

Azure SDK for Node.js に関するその他のドキュメントについては、 [詳しいリファレンス](https://azure.github.io/azure-sdk-for-node/)を参照してください。

[PowerShell](cdn-manage-powershell.md) で CDN リソースを管理します。

