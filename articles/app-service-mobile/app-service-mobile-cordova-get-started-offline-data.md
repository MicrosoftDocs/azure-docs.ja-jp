---
title: Azure Mobile Apps (Cordova) に対するオフライン同期の有効化 | Microsoft Docs
description: App Service Mobile App を使用して、Cordova アプリケーション内のオフライン データをキャッシュおよび同期する方法を説明します。
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 04c8e7b2b60a60f17c49862d5c17793c16456032
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443517"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Cordova モバイル アプリのオフライン同期を有効にする
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-offline-data) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このチュートリアルでは、Cordova 向けの Azure Mobile Apps のオフライン同期機能について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、&mdash;データの表示、追加、変更など、&mdash;モバイル アプリとやり取りできます。 変更は、ローカル データベースに格納されます。  デバイスが再びオンラインになると、これらの変更がリモート サービスと同期されます。

このチュートリアルは、チュートリアル「 [Apache Cordova クイック スタート]」を完了した際に作成される Mobile Apps 用の Cordova クイック スタート ソリューションに基づいています。 このチュートリアルでは、クイックスタート ソリューションを更新して、Azure Mobile Apps のオフライン機能を追加します。  また、アプリのオフライン固有のコードについても取り上げます。

オフラインの同期機能の詳細については、トピック「 [Azure Mobile Apps でのオフライン データ同期]」をご覧ください。 API 使用量の詳細については、[API ドキュメント](https://azure.github.io/azure-mobile-apps-js-client)をご覧ください。

## <a name="add-offline-sync-to-the-quickstart-solution"></a>クイック スタート ソリューションへのオフライン同期の追加
オフライン同期コードは、アプリに追加する必要があります。 オフライン同期には、cordova-sqlite-storage プラグインが必要です。このプラグインは、Azure Mobile Apps プラグインがプロジェクトに含まれる場合に、自動的にアプリに追加されます。 クイック スタート プロジェクトには、これらの両方のプラグインが含まれています。

1. Visual Studio のソリューション エクスプローラーで index.js を開き、次のコードを

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    次のコードに置き換えます。

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. 次に、次のコードを

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    次のコードに置き換えます。

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    上記のコードの追加によって、ローカル ストアが初期化され、Azure バックエンドで使用される列の値が一致するローカル テーブルが定義されます (このコードにすべての列の値を含める必要はありません)。`version` フィールドは、モバイル バックエンドが管理し、競合の解決に使用します。

    同期コンテキストへの参照を取得するには、 **getSyncContext**を呼び出します。 同期コンテキストは、 `.push()` が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。

3. アプリケーションの URL を Mobile App アプリケーションの URL に更新します。

4. 次に、次のコードを

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    次のコードに置き換えます。

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    上記のコードは、同期コンテキストを初期化してから、getSyncTable を (getTable の代わりに) 呼び出して、ローカル テーブルへの参照を取得します。

    このコードでは、作成、読み取り、更新、削除 (CRUD) のすべてのテーブル操作にローカル データベースを使用します。

    このサンプルでは、同期の競合に対して、単純なエラー処理を実行します。 実際のアプリケーションでは、ネットワーク状態、サーバーの競合などの各種エラーが処理されます。 コード例については、 [オフライン同期サンプル]を参照してください。

5. 次に、実際の同期を実行する関数を追加します。

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    変更をいつ Mobile App バックエンドにプッシュするかは、**syncContext.push()** を呼び出すことによって決定します。 たとえば、同期ボタンに関連付けられているボタンのイベント ハンドラーで **syncBackend** を呼び出すことが可能です。

## <a name="offline-sync-considerations"></a>オフライン同期に関する考慮事項

サンプルでは、**syncContext** の **push** メソッドが、アプリの起動時だけにログインのコールバック関数で呼び出されます。  実際のアプリケーションでは、手動で、またはネットワークの状態が変更されたときに、この同期機能がトリガーされるように設定することもできます。

コンテキストによって追跡された保留中のローカル更新のあるテーブルに対してプルが実行された場合、そのプル操作は自動的にプッシュをトリガーします。 このサンプルの項目を更新、追加、完了するとき、明示的な **push** の呼び出しは省略できます。冗長であるためです。

提供されたコードでは、リモートの todoItem テーブルのすべてのレコードが照会されますが、クエリ ID とクエリを **push**に渡すことでレコードをフィルター処理することも可能です。 詳細については、「 *Azure Mobile Apps でのオフライン データ同期* 」の「 [Azure Mobile Apps でのオフライン データ同期]」セクションを参照してください。

## <a name="optional-disable-authentication"></a>(省略可能) 認証の無効化

オフライン同期のテスト前に認証をセットアップしない場合は、ログインのコールバック関数をコメント アウトします。ただし、コールバック関数内のコードはコメント化しないままにしておきます。  ログイン行をコメント アウトすると、コードは次のようになります。

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

これで、アプリの実行時にアプリが Azure バックエンドと同期するようになります。

## <a name="run-the-client-app"></a>クライアント アプリの実行
オフライン同期が有効になったので、各プラットフォームで少なくとも 1 回クライアント アプリケーションを実行すると、ローカル ストア データベースにデータが設定されるようになりました。 この後、オフラインのシナリオをシミュレートし、アプリがオフラインの間にローカル ストアのデータを変更します。

## <a name="optional-test-the-sync-behavior"></a>(省略可能) 同期の動作のテスト
このセクションでは、バックエンドに無効なアプリケーション URL を使用することで、クライアント プロジェクトを変更して、オフラインのシナリオをシミュレートします。 データ項目を追加または変更すると、これらの変更はローカル ストアに保持されますが、接続が再確立されるまでは、バックエンドのデータ ストアには同期されません。

1. ソリューション エクスプローラーで index.js プロジェクト ファイルを開き、次のコードのように、アプリケーション URL が無効な URL を指すように変更します。

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. index.html で、CSP `<meta>` 要素を同じ無効な URL に更新します。

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. クライアント アプリをビルドして実行し、アプリがログイン後にバックエンドと同期を試みるたびに、コンソールに例外がログ記録されることを確認してください。 追加した新しい項目は、モバイルのバックエンドにプッシュされるまでローカル ストアにのみ存在します。 クライアント アプリは、バックエンドに接続されているかのように動作します。

4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。

5. (省略可能) Visual Studio を使用して、Azure SQL Database テーブルを表示し、バックエンドのデータベースのデータが変更されていないことを確認します。

    Visual Studio で、 **サーバー エクスプローラー**を開きます。 **[Azure]** -> **[SQL Databases]** を選択して、データベースに移動します。 データベースを右クリックし、 **[SQL Server オブジェクト エクスプローラーで開く]** を選択します。 これで SQL データベースのテーブルとその内容を参照できます。

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(省略可能) モバイル バックエンドへの再接続のテスト

このセクションでは、アプリケーションをモバイル バックエンドに再接続して、アプリケーションがオンライン状態に戻ったときの動作をシミュレーションします。 ログインすると、データがモバイル バックエンドに同期されます。

1. index.js をもう一度開き、アプリケーション URL を復元します。
2. index.html をもう一度開き、CSP `<meta>` 要素のアプリケーション URL を修正します。
3. クライアント アプリの再構築と実行 ログイン後、アプリはモバイル アプリ バックエンドとの同期を試みます。 デバッグ コンソールに例外がログ記録されていないことを確認してください。
4. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。 バックエンド データベースとローカル ストアの間でデータが同期されていることを確認します。

    データがデータベースとローカル ストアの間で同期されており、アプリが接続されていない状況で追加した項目が含まれていることを確認してください。

## <a name="additional-resources"></a>その他のリソース
* [Azure Mobile Apps でのオフライン データ同期]
* [Apache Cordova の Visual Studio ツール]

## <a name="next-steps"></a>次の手順
* [オフライン同期サンプル]で、競合解決など、より高度なオフライン同期機能について学びます。
* [API ドキュメント](https://azure.github.io/azure-mobile-apps-js-client)で、オフライン同期 API リファレンスを確認します。

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova クイック スタート]: app-service-mobile-cordova-get-started.md
[オフライン同期サンプル]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Apache Cordova の Visual Studio ツール]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
