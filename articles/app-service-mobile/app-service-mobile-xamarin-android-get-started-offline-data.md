---
title: Azure モバイル アプリ (Xamarin Android) に対するオフライン同期の有効化
description: App Service モバイル アプリを使用して、Xamarin Android アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9409260af0fd9739ae68545eca102ee420e9b2e8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440138"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Xamarin Android モバイル アプリのオフライン同期を有効にする

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーとかかわることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started-offline-data) を今すぐチェックしてください。
>

## <a name="overview"></a>概要

このチュートリアルでは、Xamarin.Android 向けのAzure Mobile Apps のオフライン同期機能について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。 変更は、ローカル データベースに格納されます。
デバイスが再びオンラインになると、これらの変更がリモート サービスと同期されます。

このチュートリアルでは、「[Create a Xamarin Android app (Xamarin Android アプリの作成)]」チュートリアルからクライアント プロジェクトを更新し、Azure Mobile Apps のオフライン機能をサポートできるようにします。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「 [Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## <a name="update-the-client-app-to-support-offline-features"></a>オフライン機能をサポートするようにクライアント アプリを更新する

Azure モバイル アプリのオフライン機能を使用すると、オフラインになっている状況でも、ローカル データベースとやり取りすることができます。 アプリケーションでこれらの機能を使用するには、[SyncContext] をローカル ストアに初期化します。 次に、[IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) インターフェイスを使用してテーブルを参照します。 SQLite は、デバイス上のローカル ストアとして使用されます。

1. Visual Studio で、「[Create a Xamarin Android app (Xamarin Android アプリの作成)]」チュートリアルで完了したプロジェクトの NuGet パッケージ マネージャーを開きます。  **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを検索してインストールします。
2. ToDoActivity.cs ファイルを開き、`#define OFFLINE_SYNC_ENABLED` の定義をコメント解除します。
3. Visual Studio で、 **F5** キーを押してクライアント アプリをリビルドして実行します。 アプリは、オフライン同期を有効にする前と同じように動作します。ただし今度は、オフライン シナリオで使用できるデータがローカル データベースに格納されます。

## <a name="update-sync"></a>アプリを更新してバックエンドから切断する

ここでは、オフライン状況をシミュレートするために、モバイル アプリ バックエンドへの接続を解除します。 データ項目を追加すると、例外ハンドラーによって、アプリがオフライン モードであることが通知されます。 この状態では、新しい項目はローカル ストアに追加され、プッシュが接続状態で実行されたときに、モバイル アプリ バックエンドに同期されます。

1. 共有プロジェクトの ToDoActivity.cs を編集します。 **applicationURL** を、無効な URL を指すように変更します。

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    また、デバイス上で Wi-Fi および移動体通信ネットワークを無効にするか、機内モードを使用して、オフライン動作をデモンストレーションすることもできます。
2. **F5** キーを押し、アプリケーションをビルドして実行します。 アプリを起動した際の更新時には同期が失敗することに注意してください。
3. 新しい項目を入力し、 **[保存]** をクリックするたびに [CancelledByNetworkError] ステータスでプッシュが失敗することを確認します。 ただし、新しい todo 項目は、モバイル アプリ バックエンドにプッシュされるまでは、ローカル ストア内に存在します。  運用アプリでは、これらの例外を抑制した場合、クライアント アプリはモバイル アプリ バックエンドにまだ接続されているかのように動作します。
4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。
5. (省略可能) Visual Studio で、 **サーバー エクスプローラー**を開きます。 **[Azure]** -> **[SQL Databases]** を選択して、データベースに移動します。 データベースを右クリックし、 **[SQL Server オブジェクト エクスプローラーで開く]** を選択します。 これで SQL データベースのテーブルとその内容を参照できます。 バックエンド データベース内のデータが変更されていないことを確認します。
6. (省略可能) Fiddler や Postman などの REST ツールを使用して、モバイルのバックエンドをクエリします。その際、`https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem` の形式で、GET クエリを使用します。

## <a name="update-online-app"></a>モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する

ここでは、アプリをモバイル アプリ バックエンドに再接続します。 初めてアプリケーションを実行すると、`OnCreate` イベント ハンドラーが `OnRefreshItemsSelected` を呼び出します。 このメソッドが `SyncAsync` を呼び出し、ローカル ストアとバックエンドのデータベースを同期します。

1. 共有プロジェクトの ToDoActivity.cs を開き、**applicationURL** プロパティの変更を元に戻します。
2. **F5** キーを押して、アプリケーションをリビルドして実行します。 アプリは、`OnRefreshItemsSelected` メソッドが実行されると、プッシュとプルの操作によって、ローカルでの変更を Azure Mobile Apps バックエンドに同期します。
3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。 データが同期されるのは、Azure モバイル アプリのバックエンドのデータベースとローカル ストアの間であることに注意してください。
4. アプリケーションで、ローカル ストアで完了させる項目の横にあるチェック ボックスをクリックします。

   `CheckItem` は `SyncAsync` を呼び出し、完了した各項目をモバイル アプリ バックエンドと同期します。 `SyncAsync` はプッシュとプルの両方を呼び出します。 **クライアントが変更したテーブルに対してプルを実行するたびに、プッシュが常に自動的に実行されます**。 これは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。 この動作によって、予期しないプッシュが行われることがあります。 この動作については、「 [Azure Mobile Apps でのオフライン データ同期]」を参照してください。

## <a name="review-the-client-sync-code"></a>クライアント同期コードの確認

チュートリアル「 [Create a Xamarin Android app (Xamarin Android アプリの作成)] 」を完了した際にダウンロードした Xamarin クライアント プロジェクトには、ローカルの SQLite データベースを使用したオフライン同期をサポートするコードが既に含まれてます。 チュートリアルのコードにすでに含まれているものの概要を示します。 機能の概念的な概要については、「 [Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

* テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。 `ToDoActivity.OnCreate()` が `ToDoActivity.InitLocalStoreAsync()` を実行すると、ローカル ストアのデータベースが初期化されます。 このメソッドにより、Azure Mobile Apps クライアント SDK で提供される `MobileServiceSQLiteStore` クラスを使用して、ローカルの SQLite データベースが作成されます。

    `DefineTable` メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、`ToDoItem` になります。 この型に、リモート データベース内のすべての列を含める必要はありません。 列のサブセットのみ格納できます。

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* `ToDoActivity` の `toDoTable` メンバーは、`IMobileServiceTable` ではなく、`IMobileServiceSyncTable` 型です。 IMobileServiceSyncTable は、テーブルの作成、読み取り、更新、削除 (CRUD) などのすべての操作がローカル ストア データベースに対して行われるようにします。

    `IMobileServiceSyncContext.PushAsync()`を呼び出すことによって、変更がいつ Azure モバイル アプリ バックエンドにプッシュされるかを決定します。 同期コンテキストは、 `PushAsync` が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。

    todoitem リストの更新、または todoitem の追加や完了があれば、提供されているコードは `ToDoActivity.SyncAsync()` を呼び出して同期します。 コードは、ローカルの変更があるたびに同期されます。

    提供されたコードでは、リモートの `TodoItem` テーブルのすべてのレコードはクエリされますが、クエリ ID やクエリを `PushAsync` に渡すことでレコードをフィルター処理することも可能です。 詳細については、「 *Azure Mobile Apps でのオフライン データ同期* 」の「 [Azure Mobile Apps でのオフライン データ同期]」セクションを参照してください。

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>その他のリソース

* [Azure Mobile Apps でのオフライン データ同期]
* [Azure Mobile Apps .NET SDK の使用方法][8]

<!-- URLs. -->
[Create a Xamarin Android app (Xamarin Android アプリの作成)]: ./app-service-mobile-xamarin-android-get-started.md
[Azure Mobile Apps でのオフライン データ同期]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Create a Xamarin Android app (Xamarin Android アプリの作成)]: app-service-mobile-xamarin-android-get-started.md
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
