---
title: Azure モバイル アプリ (Xamarin.Forms) に対するオフライン同期の有効化 | Microsoft Docs
description: App Service Mobile Apps を使用して、Xamarin.Forms アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 53f339d5450965c992f6528ff294e0d37ec2f7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446291"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Xamarin.Forms モバイル アプリのオフライン同期の有効化
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-offline-data) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このチュートリアルでは、Xamarin.Forms 向けの Azure Mobile Apps のオフライン同期機能について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。 変更は、ローカル データベースに格納されます。 デバイスが再びオンラインになると、これらの変更がリモート サービスと同期されます。

このチュートリアルは、チュートリアル「Xamarin.iOS アプリを作成する」を完了した際に作成される Mobile Apps 用の Xamarin.Forms クイック スタート ソリューションに基づいています。 この Xamarin.Forms のクイック スタート ソリューションには、オフライン同期をサポートするためのコードが含まれています。これを有効にする必要があります。 このチュートリアルでは、クイック スタート ソリューションを更新して、Azure Mobile Apps のオフライン機能をオンにします。 また、アプリのオフライン固有のコードについても取り上げます。 ダウンロードしたクイック スタート ソリューションを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)][1]」を参照してください。

オフラインの同期機能の詳細については、トピック「 [Azure Mobile Apps でのオフライン データ同期][2]」をご覧ください。

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>クイック スタート ソリューションでのオフライン同期機能の有効化
オフライン同期コードは、C# プリプロセッサ ディレクティブを使用してプロジェクトにインクルードされます。 **OFFLINE\_SYNC\_ENABLED** シンボルを定義すると、これらのコード パスがビルドにインクルードされます。 Windows アプリの場合、SQLite プラットフォームもインストールする必要があります。

1. Visual Studio で、ソリューション、 **[ソリューションの NuGet パッケージの管理...]** の順に右クリックし、ソリューション内のすべてのプロジェクトの **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを探してインストールします。
2. ソリューション エクスプローラーで、ポータブル クラス ライブラリ プロジェクトである **Portable** というプロジェクトから TodoItemManager.cs ファイルを開き、次のプリプロセッサ ディレクティブをコメント解除します。

        #define OFFLINE_SYNC_ENABLED
3. (省略可能) Windows デバイスをサポートする場合、次の SQLite ランタイム パッケージのいずれかをインストールします。

   * **Windows 8.1 ランタイム:** [SQLite for Windows 8.1][3] をインストールします。
   * **Windows Phone 8.1:** [SQLite for Windows Phone 8.1][4] をインストールします。
   * **ユニバーサル Windows プラットフォーム** : [ユニバーサル Windows プラットフォーム用 SQLite][5]をインストール。

     クイック スタートには、ユニバーサル Windows プロジェクトは含まれていませんが、ユニバーサル Windows プラットフォームは Xamarin フォームでサポートされます。
4. (省略可能) それぞれの Windows アプリ プロジェクトで、 **[参照]**  >  **[参照の追加]** の順に右クリックして、**Windows** フォルダー、 **[拡張機能]** の順に展開します。
    適切な **SQLite for Windows** SDK と **Visual C++ 2013 Runtime for Windows** SDK を有効にします。
    Windows プラットフォームによって SQLite SDK の名前はわずかに異なります。

## <a name="review-the-client-sync-code"></a>クライアント同期コードの確認
ここでは、 `#if OFFLINE_SYNC_ENABLED` ディレクティブ内のチュートリアル コードに既に含まれているものの概要を説明します。 オフライン同期機能は、ポータブル クラス ライブラリ プロジェクト内の TodoItemManager.cs プロジェクト ファイル内にあります。 機能の概念的な概要については、「 [Azure Mobile Apps でのオフライン データ同期][2]」をご覧ください。

* テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。 ローカル ストア データベースは、次のコードを使用して **TodoItemManager** クラス コンストラクターで初期化されます。

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    これにより、 **MobileServiceSQLiteStore** クラスを使用して新しいローカルの SQLite データベースが作成されます。

    **DefineTable** メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。  この型に、リモート データベース内のすべての列を含める必要はありません。 列のサブセットを格納できます。
* **TodoItemManager** の **todoTable** フィールドは、**IMobileServiceTable** 型ではなく **IMobileServiceSyncTable** 型です。 このクラスでは、作成、読み取り、更新、削除 (CRUD) のすべてのテーブル操作にローカル データベースを使用します。 これらの変更がいつモバイル アプリ バックエンドにプッシュされるかを決定するには、**IMobileServiceSyncContext** で **PushAsync** を呼び出します。 この同期コンテキストは、**PushAsync** が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。

    次の **SyncAsync** メソッドは、モバイル アプリ バックエンドと同期するために呼び出されます。

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    このサンプルでは、既定の同期ハンドラーを使った単純なエラー処理を使用しています。 実際のアプリケーションでは、ネットワーク状態やサーバーの競合などの各種エラーが、カスタム **IMobileServiceSyncHandler** の実装を使用して処理されます。

## <a name="offline-sync-considerations"></a>オフライン同期に関する考慮事項
このサンプルでは、**SyncAsync** メソッドは起動時と同期が明示的に要求された場合にのみ呼び出されます。  Android アプリまたは iOS アプリで同期を開始するには、項目一覧でプルダウンします。Windows アプリでは、 **[同期]** ボタンを使用します。 実際のアプリケーションでは、ネットワークの状態が変更されたときに同期がトリガーされるように設定することもできます。

コンテキストによって追跡された保留中のローカル更新のあるテーブルに対してプルが実行される場合、そのプル操作は前のコンテキストのプッシュを自動的にトリガーします。 このサンプルの項目を更新、追加、完了するとき、明示的な **PushAsync** の呼び出しを省略できます。

提供されたコードでは、リモートの TodoItem テーブルのすべてのレコードが照会されますが、クエリ ID とクエリを **PushAsync**に渡すことでレコードをフィルター処理することも可能です。 詳細については、「 *Azure Mobile Apps でのオフライン データ同期* 」の「 [増分同期][2]」セクションを参照してください。

## <a name="run-the-client-app"></a>クライアント アプリの実行
オフライン同期が有効になったので、各プラットフォームで少なくとも 1 回クライアント アプリケーションを実行して、ローカル ストア データベースにデータを設定します。 この後、オフラインのシナリオをシミュレートし、アプリがオフラインの間にローカル ストアのデータを変更します。

## <a name="update-the-sync-behavior-of-the-client-app"></a>クライアント アプリの同期の動作を更新する
このセクションでは、バックエンドに無効なアプリケーション URL を使用することで、クライアント プロジェクトを変更して、オフラインのシナリオをシミュレートします。 デバイスを "機内モード" にすることで、ネットワーク接続をオフにする方法もあります。  データ項目を追加または変更すると、これらの変更はローカル ストアに保持されますが、接続が再確立されるまでは、バックエンドのデータ ストアには同期されません。

1. ソリューション エクスプローラーで、**Portable** プロジェクトから Constants.cs プロジェクト ファイルを開き、`ApplicationURL` の値を次のような無効な URL を指すように変更します。

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. **Portable** プロジェクトから TodoItemManager.cs ファイルを開き、ベース **Exception** クラスの **catch** を、**SyncAsync** 内の **try...catch** ブロックに追加します。 この **catch** ブロックによってコンソールに例外メッセージが書き込まれます。これは次のようになります。

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. クライアント アプリの構築と実行  新しい項目をいくつか追加します。 バックエンドとの同期を試みるたびに、コンソールに例外がログ記録されることに注意してください。 これらの新しい項目は、モバイル バックエンドにプッシュされるまでは、ローカル ストア内にのみ存在します。 クライアント アプリケーションは、バックエンドに接続されているかのように動作し、作成、読み取り、更新、削除 (CRUD) 操作のすべてをサポートします。
4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。
5. (省略可能) Visual Studio を使用して、Azure SQL Database テーブルを表示し、バックエンドのデータベースのデータが変更されていないことを確認します。

    Visual Studio で、 **サーバー エクスプローラー**を開きます。 **[Azure]** -> **[SQL Databases]** を選択して、データベースに移動します。 データベースを右クリックし、 **[SQL Server オブジェクト エクスプローラーで開く]** を選択します。 これで SQL データベースのテーブルとその内容を参照できます。

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>クライアント アプリを更新し、モバイルのバックエンドを再接続します。
このセクションでは、アプリケーションをモバイル バックエンドに再接続して、アプリケーションがオンライン状態に戻ったときの動作をシミュレーションします。 更新ジェスチャを実行すると、データがモバイル バックエンドに同期されます。

1. Constants.cs をもう一度開きます。 `applicationURL` を修正して、正しい URL に設定します。
2. クライアント アプリの再構築と実行 起動後、アプリはモバイル アプリ バックエンドとの同期を試みます。 デバッグ コンソールに例外がログ記録されていないことを確認してください。
3. (省略可能) SQL Server オブジェクト エクスプローラー、または Fiddler や [Postman][6] などの REST ツールを使用して、更新データを表示します。 バックエンド データベースとローカル ストアの間でデータが同期されていることを確認します。

    データがデータベースとローカル ストアの間で同期されており、アプリが接続されていない状況で追加した項目が含まれていることを確認してください。

## <a name="additional-resources"></a>その他のリソース
* [Azure Mobile Apps でのオフライン データ同期][2]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
