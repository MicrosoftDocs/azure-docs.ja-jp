---
title: "Sails.js Web アプリを Azure App Service にデプロイする | Microsoft Docs"
description: "Node.js アプリケーションを Azure App Service にデプロイする方法を学習します。 このチュートリアルは、Sails.js Web アプリをデプロイする方法を説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 8877ddc8-1476-45ae-9e7f-3c75917b4564
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: b3c8a2d54ea05462db8a873c852a70e64111bc2f
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Sails.js Web アプリを Azure App Service にデプロイする
このチュートリアルでは、Sails.js アプリを Azure App Service にデプロイする方法について説明します。 その過程で、App Service で実行する Node.js アプリを構成する方法の一般的な知識を得ることができます。

ここでは、次の便利なスキルを学習します。

* App Service での Sails.js アプリの実行を構成する。
* アプリをコマンドラインから App Service にデプロイする。
* デプロイに関するトラブルシューティングのために、stderr と stdout のログを読み取る。
* 環境変数をソース管理の外部に保存する。
* アプリから Azure 環境変数にアクセスする。
* データベース (MongoDB) に接続する。

Sails.js の実用的な知識が必要です。 このチュートリアルは、一般的な Sail.js の実行に関する問題解決を支援するものではありません。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン

次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](app-service-web-nodejs-sails-cli-nodejs.md) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0](app-service-web-nodejs-sails.md) - Resource Manager デプロイ モデル用の次世代 CLI

## <a name="prerequisites"></a>前提条件
* [Node.JS](https://nodejs.org/)
* [Sails.js](http://sailsjs.org/get-started)
* [Git](http://www.git-scm.com/downloads)
* [Azure CLI 2.0 プレビュー](/cli/azure/install-az-cli2)
* Microsoft Azure アカウント。 アカウントを持っていない場合は、[無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)するか [Visual Studio サブスクライバー特典を有効](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)にしてください。

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

## <a name="step-1-create-and-configure-a-sailsjs-app-locally"></a>手順 1: Sails.js アプリをローカルで作成および構成する
まず、次の手順に従って、開発環境で既定の Sails.js アプリをすばやく作成します。

1. 任意のコマンド ライン ターミナルと `CD` を作業ディレクトリに開きます。
2. Sails.js アプリを作成して実行します。

        sails new <app_name>
        cd <app_name>
        sails lift

    http://localhost:1377 で既定のホーム ページに移動できることを確認します。

1. 次に、Azure のログを有効にします。 ルート ディレクトリで、`iisnode.yml` というファイルを作成し、次の&2; 行を追加します。

        loggingEnabled: true
        logDirectory: iisnode

    これで、Azure App Service が Node.js アプリの実行のために使用する [iisnode](https://github.com/tjanczuk/iisnode) サーバーに対して、ログが有効になりました。 
    このしくみの詳細については、「 [iisnode から stdout ログと stderr ログを取得する](app-service-web-nodejs-get-started.md#iisnodelog)」を参照してください。

2. 次に、Azure の環境変数を使用するように Sails.js アプリを構成します。 config/env/production.js を開いて運用環境を構成し、`port` と `hookTimeout` を設定します。

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    これらの構成設定のドキュメントについては、 [Sails.js のドキュメント](http://sailsjs.org/documentation/reference/configuration/sails-config)のページをご覧ください。

4. 次に、使用する Node.js のバージョンをハードコードします。 package.json で、次の `engines` プロパティを追加して、Node.js バージョンを目的のバージョンに設定します。

        "engines": {
            "node": "6.9.1"
        },

5. 最後に、Git リポジトリを初期化し、ファイルをコミットします。 アプリケーション ルート (package.json がある場所) で、次の Git コマンドを実行します。

        git init
        git add .
        git commit -m "<your commit message>"

コードをデプロイする準備ができました。 

## <a name="step-2-create-an-azure-app-and-deploy-sailsjs"></a>手順 2: Azure アプリを作成し、Sails.js をデプロイする

次に、Azure で App Service リソースを作成し、Sails.js アプリをデプロイします。

1. 次のようにして、Azure にログインします。

        az login

    画面の指示に従い、Azure サブスクリプションのある Microsoft アカウントを使用してブラウザーでログイン操作を進めます。

3. App Service のデプロイ ユーザーを設定します。 後で、これらの資格情報を使用してコードをデプロイします。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 任意の名前で[リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 この PHP チュートリアルでは、実際にその内容を把握している必要はありません。

        az group create --location "<location>" --name my-sailsjs-app-group

    `<location>` に使用できる値を確認するには、CLI コマンド `az appservice list-locations` を使用してください。

3. 任意の名前で、"FREE" の [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を作成します。 この PHP チュートリアルでは、このプランの Web アプリに対しては課金されないことを把握しておくだけでかまいません。

        az appservice plan create --name my-sailsjs-appservice-plan --resource-group my-sailsjs-app-group --sku FREE

4. `<app_name>` に一意の名前を指定して新しい Web アプリを作成します。

        az appservice web create --name <app_name> --resource-group my-sailsjs-app-group --plan my-sailsjs-appservice-plan

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>手順 3: Sails.js アプリを構成およびデプロイする

1. 次のコマンドを使用して、新しい Web アプリのローカル Git デプロイを構成します。

        az appservice web source-control config-local-git --name <app_name> --resource-group my-sailsjs-app-group

    次のような JSON 出力が表示されます。これは、リモート Git リポジトリが構成されていることを意味します。

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. この URL を、ローカル リポジトリの Git リモートとして JSON に追加します (Git リモートは、わかりやすくするために `azure` という名前にしています)。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. サンプル コードを `azure` Git リモートにデプロイします。 入力を求められたら、前に構成したデプロイ資格情報を使用します。

        git push azure master

7. 最後に、ブラウザーでライブ Azure アプリを起動します。

        az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

    同じ Sails.js ホーム ページが表示されます。

    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>デプロイのトラブルシューティング
App Service で何らかの理由により Sails.js アプリケーションのエラーが発生した場合は、そのトラブルシューティングを行うのに役立つ stderr ログを探します。
詳細については、「 [iisnode から stdout ログと stderr ログを取得する](app-service-web-nodejs-get-started.md#get-stdout-and-stderr-logs-from-iisnode)」を参照してください。
アプリが正常に起動した場合は、stdout ログに次のような見慣れたメッセージが表示されます。

                   .-..-.
    
       Sails              <|    .-..-.
       v0.12.11            |\
                          /|.\
                         / || \
                       ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
       __---___--___---___--___---___--___
     ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

stdout ログの粒度は [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) ファイルで制御することができます。

## <a name="connect-to-a-database-in-azure"></a>Azure のデータベースへの接続
Azure のデータベースに接続するには、Azure SQL Database、MySQL、MongoDB、Azure (Redis) Cache など、Azure で任意のデータベースを作成し、対応する [データストア アダプター](https://github.com/balderdashy/sails#compatibility) を使用してそのデータベースに接続します。 このセクションの手順では、MongoDB のクライアント接続をサポートできる [Azure DocumentDB](../documentdb/documentdb-protocol-mongodb.md) データベースを使用して、MongoDB に接続する方法を説明します。

1. [MongoDB プロトコル対応の DocumentDB アカウントを作成します](../documentdb/documentdb-create-mongodb-account.md)。
2. [DocumentDB のコレクションとデータベースを作成します](../documentdb/documentdb-create-collection.md)。 コレクションの名前は重要ではありませんが、Sails.js からの接続時にデータベースの名前が必要になります。
3. [DocumentDB データベースの接続情報を取得します](../documentdb/documentdb-connect-mongodb-account.md#a-idgetcustomconnectiona-get-the-mongodb-connection-string-to-customize)。
2. コマンド ライン ターミナルから、MongoDB アダプターをインストールします。

        npm install sails-mongo --save

3. config/connections.js を開いて次の接続オブジェクトをリストに追加します。

        docDbMongo: {
            adapter: 'sails-mongo',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost,
            port: process.env.dbport,
            database: process.env.dbname,
            ssl: true
        },

    > [!NOTE] 
    > `ssl: true` オプションは、[Azure DocumentDB の必須要件](../documentdb/documentdb-connect-mongodb-account.md#connection-string-requirements)なので重要です。 
    >
    >

4. 環境変数 (`process.env.*`) ごとに、App Service で設定する必要があります。 そのためには、ターミナルから次のコマンドを実行します。 DocumentDB データベースの接続情報を使用します。

        az appservice web config appsettings update --settings dbuser="<database user>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbpassword="<database password>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbhost="<database hostname>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbport="<database port>" --name <app_name> --resource-group my-sailsjs-app-group
        az appservice web config appsettings update --settings dbname="<database name>" --name <app_name> --resource-group my-sailsjs-app-group

    必要な設定を Azure のアプリケーション設定に置くことで、ソース管理 (Git) の対象から機密データを除外することができます。 次に、同じ接続情報を使用するように開発環境を構成します。
5. config/local.js を開き、次の接続オブジェクトを追加します。

        connections: {
            docDbMongo: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>",
                database: "<database name>",
                ssl: true
            },
        },

    ローカル環境の config/connections.js ファイルに含まれている設定は、この構成によって上書きされます。 このファイルは、プロジェクトの既定の .gitignore によって除外され、Git には保管されません。 これで、Azure Web アプリとローカル開発環境のどちらからでも、DocumentDB (MongoDB) データベースに接続できるようになりました。
6. config/env/production.js を開いて運用環境を構成し、次の `models` オブジェクトを追加します。

        models: {
            connection: 'docDbMongo',
            migrate: 'safe'
        },
7. config/env/development.js を開いて開発環境を構成し、次の `models` オブジェクトを追加します。

        models: {
            connection: 'docDbMongo',
            migrate: 'alter'
        },

    `migrate: 'alter'` を指定すると、データベース移行機能を使用して、データベースのコレクションやテーブルを簡単に作成したり更新することができます。 ただし、Sails.js では運用環境で `migrate: 'alter'` を使用することができないため ( [Sails.js のドキュメント](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)を参照)、Azure (運用) 環境では `migrate: 'safe'` が使用されます。
8. ターミナルから、通常と同様に Sails.js の [blueprint API](http://sailsjs.org/documentation/concepts/blueprints) を[生成](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate)し、`sails lift` を実行して、データベースを作成すると共に Sails.js データベースを移行します。 次に例を示します。

         sails generate api mywidget
         sails lift

    このコマンドによって生成される `mywidget` モデルは空ですが、このモデルを使用することで、データベース接続の存在を示すことができます。
    `sails lift` を実行すると、アプリで使用されているモデルに足りないコレクションとテーブルが作成されます。
9. 今作成した blueprint API にブラウザーでアクセスします。 次に例を示します。

        http://localhost:1337/mywidget/create

    この API からは、作成されたエントリがブラウザー ウィンドウに返されます。つまり、これをもって、コレクションが正常に作成されたと考えることができます。

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}
10. 変更内容を Azure にプッシュし、アプリに移動して、アプリがまだ動作しているかどうかを確認します。

         git add .
         git commit -m "<your commit message>"
         git push azure master
         az appservice web browse --name <app_name> --resource-group my-sailsjs-app-group

11. Azure Web アプリの blueprint API にアクセスします。 For example:

         http://<appname>.azurewebsites.net/mywidget/create

     API から別の新しいエントリが返されれば、Azure Web アプリが DocumentDB (MongoDB) データベースと対話しているということです。

## <a name="more-resources"></a>その他のリソース
* [Get started with Node.js web apps in Azure App Service (Azure App Service で Node.js Web アプリの使用を開始する)](app-service-web-nodejs-get-started.md)
* [Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)

