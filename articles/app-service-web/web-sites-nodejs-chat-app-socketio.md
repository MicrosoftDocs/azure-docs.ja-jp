---
title: "Azure App Service での Socket.IO を使用する Node.js チャット アプリケーションの構築"
description: "Azure でホストされる node.js Web アプリで socket.io を使用する方法を示すチュートリアル。"
services: app-service\web
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: c4c4af36-3ecf-4619-b586-ca90d53ce35b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 4f5c04525437aefeccbe58e06b084433d0413dc1
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Azure App Service での Socket.IO を使用する Node.js チャット アプリケーションの構築
Socket.IO は、WebSocket を使用して node.js サーバーとクライアントの間のリアルタイム通信を提供します。 また、古いブラウザーで動作する他のトランスポート (ロング ポーリングなど) へのフォールバックもサポートします。 このチュートリアルでは、Azure Web アプリとして Socket.IO ベースのチャット アプリケーションをホストする手順と、 [Azure Redis Cache]を使用してアプリケーションをスケーリングする方法を説明します。 Socket.IO の詳細については、<http://socket.io/> を参照してください。

> [!NOTE]
> このタスクの手順は、[App Service Web Apps] に適用されます。Cloud Services については、「[Azure Cloud Services で Socket.IO を使用する Node.js チャット アプリケーションを構築する]」をご覧ください。
> 
> 

## <a name="download-the-chat-example"></a>チャットのサンプルのダウンロード
このプロジェクトでは、 [Socket.IO GitHub]リポジトリにあるチャットのサンプルを使用します。 次の手順を実行して、サンプルをダウンロードし、先ほど作成したプロジェクトに追加します。

1. Socket.IO プロジェクトの [ZIP または GZ アーカイブ リリース] をダウンロードします (このドキュメントではバージョン 1.3.5 を使用しました)。
2. アーカイブを展開し、**examples\\chat** ディレクトリを新しい場所にコピーします  (例: **\\node\\chat**)。

## <a name="modify-appjs-and-install-modules"></a>App.js の変更とモジュールのインストール
1. **index.js** ファイルの名前を **app.js** に変更します。 これにより、Azure はこれが Node.js アプリケーションであることを検出できます。
2. テキスト エディターで **app.js** ファイルを開きます。 `var io = require('../..')(server);` を含む行を以下に示すように変更します。
   
       var express = require('express');
       var app = express();
       var server = require('http').createServer(app);
       // var io = require('../..')(server);
       // New:
       var io = require('socket.io')(server);
       var port = process.env.PORT || 3000;
3. **package.json** ファイルを開き、次のように、socket.io への参照を `dependencies` の下に追加します。
   
        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }
4. コマンド ラインで **\\node\\chat** ディレクトリに移動し、このアプリケーションで必要なモジュールを npm を使用してインストールします。
   
        npm install
   
    これにより、モジュールが **node_modules** という名前のサブフォルダーにインストールされます。

## <a name="create-an-azure-web-app"></a>Azure Web アプリの作成
Azure Web アプリを作成し、Git 発行を有効にして、Web アプリの WebSocket サポートを有効にするには、次の手順に従います。

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure の無料試用版サイト</a>」を参照してください。
> 
> 

1. Azure コマンド ライン インターフェイス (Azure CLI) をインストールし、Azure サブスクリプションに接続します。 「 [Azure CLI のインストールと構成](../cli-install-nodejs.md)」をご覧ください。
2. Azure で初めてリポジトリを設定する場合は、ログイン資格情報を作成する必要があります。 Azure CLI で、次のコマンドを入力します。
   
        azure site deployment user set [username] [password]
3. **\\node\chat** ディレクトリに移動し、次のコマンドを使用して新しい Azure Web アプリとローカル Git リポジトリを作成します。 このコマンドにより、「azure」という名前の Git リモートも作成されます。
   
        azure site create mysitename --git
   
    "mysitename" を一意の Web アプリ名に置き換える必要があります。
4. 次のコマンドを使用して、既存のファイルをローカル リポジトリにコミットします。
   
        git add .
        git commit -m "Initial commit"
5. 次のコマンドで、ファイルを Azure Web Apps リポジトリにプッシュします。
   
        git push azure master
   
    メッセージが表示されたら、手順 2. の資格情報を入力します。 モジュールがサーバーにインポートされると、ステータス メッセージを受信します。 この処理が完了すると、アプリケーションが Azure Web アプリでホストされるようになります。
   
   > [!NOTE]
   > モジュールのインストール中に、"インポートされたプロジェクト ... が見つかりませんでした" というエラーが表示される場合があります。 このエラーは無視してかまいません。
   > 
   > 
6. Socket.IO では、WebSocket を使用します。WebSocket は、Azure では既定で有効になりません。 Web Socket を有効にするには、次のコマンドを使用します。
   
        azure site set -w
   
    Web アプリの名前の入力を求められた場合は、入力します。
   
   > [!NOTE]
   > "azure site set -w" コマンドは、Version 0.7.4 以降の Azure  コマンド ライン インターフェイスでのみ機能します。 また、 [Azure ポータル](https://portal.azure.com)を使用して、WebSocket のサポートを有効にすることもできます。
   > 
   > Azure Portal を使用して Websocket を有効にするには、[Web Apps] ブレードから Web アプリをクリックし、**[すべての設定]** > **[アプリケーションの設定]** をクリックします。 **[Web Sockets]** で、**[オン]** をクリックします。 その後、 **[保存]**をクリックします。
   > 
   > 
7. Azure の Web アプリを表示するには、次のコマンドを使用して Web ブラウザーを起動し、ホストされている Web アプリに移動します。
   
        azure site browse

これで、アプリケーションは Azure で実行されるようになり、Socket.IO を使用する複数のクライアント間でチャット メッセージを中継できます。

## <a name="scale-out"></a>スケールアウト
**アダプター**を使用してメッセージとイベントを複数のアプリケーション インスタンスに分配することにより、Socket.IO アプリケーションをスケールアウトできます。 複数のアダプターを使用できますが、Azure Redis Cache 機能では [socket.io-redis] アダプターを簡単に使用できます。

> [!NOTE]
> Socket.IO ソリューションのスケールアウトに対する追加要件は、スティッキ セッションのサポートです。 Azure Web Apps では Azure Request Routing によるスティッキ セッションが既定で有効になっています。 詳細については、 [Azure Web サイトでのインスタンスのアフィニティ]に関するブログ記事をご覧ください。
> 
> 

### <a name="create-a-redis-cache"></a>Redis Cache の作成
「 [Azure Redis Cache でのキャッシュの作成] 」の手順を実行して、新しいキャッシュを作成します。

> [!NOTE]
> キャッシュの**ホスト名**と**プライマリ キー**を保存します。これらは次の手順で必要になります。
> 
> 

### <a name="add-the-redis-and-socketio-redis-modules"></a>Redis および socket.io-redis モジュールの追加
1. コマンド ラインから、**\\node\\chat** ディレクトリに移動し、次のコマンドを実行します。
   
        npm install socket.io-redis@0.1.4 redis@0.12.1 --save
   
   > [!NOTE]
   > このコマンドで指定されているバージョンは、この記事をテストするときに使用したバージョンです。
   > 
   > 
2. **app.js** ファイルを変更し、次の行を `var io = require('socket.io')(server);` の直後に追加します。
   
        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
   
        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));
   
    **redishostname** および **rediskey** は、実際の Redis Cache のホスト名とキーに置き換えます。
   
    これにより、前の手順で作成した Redis Cache に対する発行とサブスクライブ クライアントが作成されます。 その後、クライアントは、Redis Cache を使用してアプリケーション インスタンス間のメッセージおよびイベントの受け渡しを行うように Socket.IO を構成するために、アダプターで使用されます。
   
   > [!NOTE]
   > **socket.io-redis** アダプターは Redis と直接通信できますが、現在のバージョンは Azure Redis Cache で必要な認証をサポートしていません。 したがって、**redis** モジュールを使用して最初の接続が作成された後、クライアントが **socket.io-redis** アダプターに渡されます。
   > 
   > Azure Redis Cache はポート 6380 を使用するセキュリティで保護された接続をサポートしますが、この例で使用されているモジュールは、2014/7/14 の時点では、セキュリティで保護された接続をサポートしていません。 前記のコードは、セキュリティで保護されていない既定のポート 6379 を使用しています。
   > 
   > 
3. 変更した **app.js** を保存します。

### <a name="commit-changes-and-redeploy"></a>変更のコミットと再デプロイ
**\\node\\chat** ディレクトリのコマンド ラインから、次のコマンドを使用して変更をコミットし、アプリケーションを再デプロイします。

    git add .
    git commit -m "implementing scale out"
    git push azure master

変更をサーバーにプッシュした後は、次のコマンドを使用してサイトを複数のインスタンスに拡大できます。

    azure site scale instances --instances #

**#** は、作成するインスタンスの数です。

複数のブラウザーまたはコンピューターから Web アプリに接続して、メッセージがすべてのクライアントに正しく送信されることを確認できます。

## <a name="troubleshooting"></a>トラブルシューティング
### <a name="connection-limits"></a>接続制限
Azure Web Apps は複数の SKU で使用でき、SKU によってサイトで使用できるリソースが決まります。 これには、許可される WebSocket 接続の数が含まれます。 詳細については、 [Web Apps の料金のページ]を参照してください。

### <a name="messages-arent-being-sent-using-websockets"></a>WebSocket を使用してメッセージを送信できない
クライアントのブラウザーがロング ポーリングへのフォールバックを続け、WebSocket を使用しない場合、次のいずれかによって解決する可能性があります。

* **トランスポートを WebSocket だけに限定してみる**
  
    Socket.IO がメッセージング トランスポートとして WebSocket を使用するには、サーバーとクライアントの両方が WebSocket をサポートしている必要があります。 どちらかがサポートしていない場合、Socket.IO はロング ポーリングなどの別のトランスポートをネゴシエートします。 Socket.IO によって使用されるトランスポートの既定のリストは、 ` websocket, htmlfile, xhr-polling, jsonp-polling`です。 **app.js** ファイルの `, nicknames = {};` を含む行の後に次のコードを追加することによって、WebSocket だけを使用するように強制できます。
  
        io.configure(function() {
          io.set('transports', ['websocket']);
        });
  
  > [!NOTE]
  > 上記のコードは通信を WebSocket だけに制限するので、WebSocket をサポートしない古いブラウザーは、上記のコードが有効になっている間はサイトに接続できないことに注意してください。
  > 
  > 
* **SSL を使用する**
  
    WebSocket は **Upgrade** ヘッダーなどの使用頻度が低い一部の HTTP ヘッダーに依存します。 Web プロキシなど、中間ネットワーク デバイスの中にはこれらのヘッダーを削除するものがあります。 この問題を避けるには、WebSocket 接続を SSL 経由で確立できます。
  
    これを実現する簡単な方法は、Socket.IO を `match origin protocol`に構成することです。 この指定は、Socket.IO に、Web ページに対する HTTP/HTTPS 要求を送信するときと同じように WebSocket 通信をセキュリティで保護するように指示します。 ブラウザーが HTTPS URL を使用して Web サイトにアクセスすると、それ以降の Socket.IO を使用した WebSocket 通信は SSL によってセキュリティで保護されます。
  
    この構成が有効になるようにこの例を変更するには、**app.js** ファイルの `, nicknames = {};` を含む行の後に次のコードを追加します。
  
        io.configure(function() {
          io.set('match origin protocol', true);
        });
* **web.config の設定を確認する**
  
    Node.js アプリケーションをホストする Azure Web アプリは、 **web.config** ファイルを使用して、受け取った要求を Node.js アプリケーションにルーティングします。 Node.js アプリケーションで WebSocket が正常に機能するためには、 **web.config** に次のエントリが含まれる必要があります。
  
        <webSocket enabled="false"/>
  
    これにより IIS WebSocket モジュールが無効になりますが、それには WebSocket の独自の実装が含まれ、それは Socket.IO などの Node.js 固有の WebSocket モジュールと競合します。 この行が存在しない場合、または `true`に設定されている場合は、WebSocket トランスポートがアプリケーションで動作しない原因になる可能性があります。
  
    通常、Node.js アプリケーションには **web.config** ファイルが含まれないので、Azure Websites はデプロイされるときに Node.js アプリケーション用にこのファイルを自動的に生成します。 このファイルはサーバー上に自動的に生成されるので、Web サイトでこのファイルを参照するには FTP または FTPS URL を使用する必要があります。 クラシック ポータルでサイトの FTP および FTPS URL を検索するには、Web サイトを選択して、 **[ダッシュボード]** リンクをクリックします。 URL は **[概要]** セクションに表示されます。
  
  > [!NOTE]
  > **web.config** ファイルが Azure Websites によって生成されるのは、アプリケーションで提供されていない場合だけです。 アプリケーション プロジェクトのルートで **web.config** ファイルを提供した場合は、それが Azure Web Apps によって使用されます。
  > 
  > 
  
    エントリが存在しない場合、または値 `true` に設定されている場合は、Node.js アプリケーションのルートに **web.config** を作成し、値 `false` を指定する必要があります。  参考として、エントリ ポイントとして **app.js** を使用するアプリケーションの既定の **web.config** を次に示します。
  
        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:
  
             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->
  
        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>
  
                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>
  
                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled
  
              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>
  
    アプリケーションで **app.js** 以外のエントリ ポイントを使用している場合は、すべての **app.js** を正しいエントリ ポイントに置き換える必要があります。 たとえば、**app.js** を **server.js** に置き換えます。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成]」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Web アプリでホストされるチャット アプリケーションを作成する方法を説明しました。 このアプリケーションは、Azure クラウド サービスとしてホストすることもできます。 そのための作業手順については、「 [Azure Cloud Services で Socket.IO を使用する Node.js チャット アプリケーションを構築する]」を参照してください。

詳細については、 [Node.js デベロッパー センター]を参照してください。

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス]」をご覧ください。

<!-- URL List -->

[Azure Redis Cache]: /documentation/services/redis-cache/
[App Service Web Apps]: http://go.microsoft.com/fwlink/?LinkId=529714
[Web Apps の料金のページ]: http://go.microsoft.com/fwlink/?LinkId=511643
[Azure Cloud Services で Socket.IO を使用する Node.js チャット アプリケーションを構築する]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../cli-install-nodejs.md
[Azure App Service と既存の Azure サービス]: http://go.microsoft.com/fwlink/?LinkId=529714
[Node.js デベロッパー センター]: /develop/nodejs/
[Azure App Service アプリケーションの作成]: https://azure.microsoft.com/try/app-service/
[Azure Web サイトでのインスタンスのアフィニティ]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Azure Redis Cache でのキャッシュの作成]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.io-redis]: https://github.com/socketio/socket.io-redis
[Socket.IO GitHub]: https://github.com/socketio/socket.io
[ZIP または GZ アーカイブ リリース]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png

