---
title: "Node.js のバージョンの指定"
description: "Azure Websites と Cloud Services で使用される Node.js のバージョンを指定する方法を説明します。"
services: 
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d0e15278-2ab4-4ec8-8256-913839c6d5ef
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 754e07e1942a809e415a1da6d1eab6307c65a47a
ms.lasthandoff: 03/21/2017


---
# <a name="specifying-a-nodejs-version-in-an-azure-application"></a>Azure アプリケーションでの Node.js のバージョンの指定
Node.js アプリケーションをホストするときに、アプリケーションで特定のバージョンの Node.js を使用することが必要になる場合があります。 Azure でホストされるアプリケーションについてこれを行うには、いくつかの方法があります。

## <a name="default-versions"></a>既定のバージョン
Azure が提供する Node.js のバージョンは常に更新されます。 特に指定しない限り、 `WEBSITE_NODE_DEFAULT_VERSION` 環境変数で指定された既定のバージョンが使用されます。 この既定値を上書きするには、以降この記事に書かれているセクションの手順に従ってください。

> [!NOTE]
> Azure クラウド サービス (Web ロールまたは worker ロール) でアプリケーションをホストしており、初めてアプリケーションをデプロイした場合、開発環境にインストールされていた Node.js が Azure で使用できる既定のバージョンのいずれかと一致するときには、同じバージョンを使用することが試行されます。
>
>

## <a name="versioning-with-packagejson"></a>package.json でバージョンを指定する
次の内容を **package.json** ファイルに追加することによって、使用する Node.js のバージョンを指定できます。

    "engines":{"node":version}

ここで、 *version* は使用する特定のバージョン番号です。 次のように、より複雑なバージョンの条件を指定できます。

    "engines":{"node": "0.6.22 || 0.8.x"}

0.6.22 はホスティング環境で利用可能なバージョンには含まれていないため、0.8 シリーズで利用可能な最も高いバージョンである 0.8.4 が代わりに使用されます。

## <a name="versioning-websites-with-app-settings"></a>アプリケーションの設定で Websites のバージョンを指定する
Web サイトでアプリケーションをホストしている場合は、環境変数 **WEBSITE_NODE_DEFAULT_VERSION** を目的のバージョンに設定できます。

## <a name="versioning-cloud-services-with-powershell"></a>PowerShell で Cloud Services のバージョンを指定する
クラウド サービスでアプリケーションをホストしており、Microsoft Azure PowerShell を使用してアプリケーションをデプロイしている場合は、PowerShell の **Set-AzureServiceProjectRole** コマンドレットを使用して既定の Node.js のバージョンをオーバーライドできます。 次に例を示します。

    Set-AzureServiceProjectRole WebRole1 Node 0.8.4

上記のステートメント内のパラメーターは大文字と小文字が区別されます。  Node.js の正しいバージョンが選択されていることを検証できます。このためには、ロールの **package.json** の **engines** プロパティを確認します。

また、 **Get-AzureServiceProjectRoleRuntime** を使用して、クラウド サービスとしてホストされるアプリケーションで利用可能な Node.js のバージョンの一覧を取得できます。  プロジェクトが依存している Node.js のバージョンは、この一覧でいつでも確認できます。

## <a name="using-a-custom-version-with-azure-websites"></a>Azure Websites でカスタム バージョンを使用する
Azure には Node.js の既定のバージョンが複数用意されていますが、既定以外のバージョンを使用することもできます。 アプリケーションが Azure Website としてホストされている場合、 **iisnode.yml** ファイルを使用してこれを実現できます。 以下の手順では、Azure Website で Node.Js のカスタム バージョンを使用するプロセスを説明します。

1. 新しいディレクトリを作成し、そのディレクトリ内に **server.js** ファイルを作成します。 **server.js** ファイルの内容は次のようになっている必要があります。

        var http = require('http');
        http.createServer(function(req,res) {
          res.writeHead(200, {'Content-Type': 'text/html'});
          res.end('Hello from Azure running node version: ' + process.version + '</br>');
        }).listen(process.env.PORT || 3000);

    これにより、Web サイトを閲覧するときに、使用されている Node.js のバージョンが表示されます。
2. 新しい Web サイトを作成し、サイトの名前をメモしておきます。 たとえば、次の例では [Azure コマンド ライン ツール] を使用して、 **mywebsite**という名前の新しい Azure Website を作成し、この Web サイトの Git リポジトリを有効にします。

        azure site create mywebsite --git
3. **server.js** ファイルが格納されているディレクトリの子として **bin** という名前の新しいディレクトリを作成します。
4. アプリケーションで使用する特定のバージョンの **node.exe** (Windows 版) をダウンロードします。 たとえば、次の例では **curl** を使用してバージョン 0.8.1 をダウンロードします。

        curl -O http://nodejs.org/dist/v0.8.1/node.exe

    **node.exe** ファイルを前の手順で作成した **bin** フォルダーに保存します。
5. **iisnode.yml** ファイルを **server.js** ファイルと同じディレクトリに作成し、次の内容を **iisnode.yml** ファイルに追加します。

        nodeProcessCommandLine: "D:\home\site\wwwroot\bin\node.exe"

    このパスは、アプリケーションを Azure Website に発行した際に、プロジェクト内の **node.exe** ファイルが配置される場所です。
6. アプリケーションを発行します。 たとえば、先ほど --git パラメーターを指定して新しい Web サイトを作成したので、次のコマンドによって、自分のローカル Git リポジトリにアプリケーション ファイルが追加された後、Web サイトのリポジトリにプッシュされます。

        git add .
        git commit -m "testing node v0.8.1"
        git push azure master

    アプリケーションが発行された後、ブラウザーで Web サイトを開きます。 "Hello from Azure running node version: v0.8.1" というメッセージが表示されます。

## <a name="next-steps"></a>次のステップ
アプリケーションで使用される Node.js のバージョンを指定する方法が理解できたら、[モジュールの使用方法]、[Node.js Web サイトを構築、デプロイする方法](app-service-web/web-sites-nodejs-develop-deploy-mac.md)、[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]に関する各トピックを参照してください。

詳細については、 [Node.js デベロッパー センター](https://azure.microsoft.com/develop/nodejs/)を参照してください。

[Mac および Linux 用 Azure コマンド ライン ツールの使用方法]:cli-install-nodejs.md
[Azure コマンド ライン ツール]:cli-install-nodejs.md
[モジュールの使用方法]: nodejs-use-node-modules-azure-apps.md
[build and deploy a Node.js Web Site]: web-sites-nodejs-develop-deploy-mac.md

