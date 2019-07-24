---
title: Mobile Apps でユニバーサル Windows プラットフォーム (UWP) アプリのオフライン同期を有効にする | Microsoft Docs
description: Azure Mobile App を使用して、ユニバーサル Windows プラットフォーム (UWP) アプリでオフライン データをキャッシュおよび同期する方法について説明します。
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994920"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Windows アプリのオフライン同期を有効にする
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Mobile App バックエンドを使用して、ユニバーサル Windows プラットフォーム (UWP) アプリにオフライン サポートを追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。 変更は、ローカル データベースに格納されます。 デバイスが再びオンラインになると、これらの変更がリモート バックエンドと同期されます。

このチュートリアルでは、「[Create a Windows app (Windows アプリの作成)]」チュートリアルからの UWP アプリ プロジェクトを更新し、Azure Mobile Apps のオフライン機能をサポートできるようにします。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「 [Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## <a name="requirements"></a>必要条件  
このチュートリアルの前提条件は次のとおりです。

* Windows 8.1 以降で実行されている Visual Studio 2013。
* 「 [Create a Windows app (Windows アプリの作成)][Create a Windows app (Windows アプリの作成)]」を参照してください。
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [ユニバーサル Windows プラットフォーム開発用 SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>オフライン機能をサポートするようにクライアント アプリを更新する
Azure モバイル アプリのオフライン機能を使用すると、オフラインになっている状況でも、ローカル データベースとやり取りすることができます。 アプリケーションでこれらの機能を使用するには、[SyncContext][synccontext] をローカル ストアに初期化します。 その後、 [IMobileServiceSyncTable][IMobileServiceSyncTable] インターフェイスを使用してテーブルを参照します。 SQLite は、デバイス上のローカル ストアとして使用されます。

1. [ユニバーサル Windows プラットフォーム用の SQLite ランタイム](https://sqlite.org/2016/sqlite-uwp-3120200.vsix)をインストールします。
2. Visual Studio で、「[Create a Windows app (Windows アプリの作成)]」チュートリアルで完了した UWP アプリ プロジェクトの NuGet パッケージ マネージャーを開きます。
    **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを検索してインストールします。
3. ソリューション エクスプ ローラーで、**[参照]** > **[参照の追加]**> **[ユニバーサル Windows]** > **[拡張機能]** の順に右クリックおよびクリックして、**[ユニバーサル Windows プラットフォーム用 SQLite]** と **[Visual C++ 2015 Runtime for Universal Windows Platform apps]\(ユニバーサルWindows プラットフォーム アプリ用 Visual C++ 2015 ランタイム\)** の両方を有効にします。

    ![SQLite UWP リファレンスを追加する][1]
4. MainPage.xaml.cs ファイルを開き、`#define OFFLINE_SYNC_ENABLED`の定義をコメント解除します。
5. Visual Studio で、 **F5** キーを押してクライアント アプリをリビルドして実行します。 アプリは、オフライン同期を有効にする前と同じように動作します。ただし今度は、オフライン シナリオで使用できるデータがローカル データベースに格納されます。

## <a name="update-sync"></a>アプリを更新してバックエンドから切断する
ここでは、オフライン状況をシミュレートするために、モバイル アプリ バックエンドへの接続を解除します。 データ項目を追加すると、例外ハンドラーによって、アプリがオフライン モードであることが通知されます。 この状態では、新しい項目はローカル ストアに追加され、プッシュが次に接続状態で実行したときに、モバイル アプリ バックエンドに同期されます。

1. 共有プロジェクトで App.xaml.cs を編集します。 **MobileServiceClient** の初期化をコメント アウトし、無効なモバイル アプリ URL を使用する次の行を追加します。

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    また、デバイス上で Wi-Fi および移動体通信ネットワークを無効にしてオフライン動作をデモンストレーションすることも、機内モードを使用することもできます。
2. **F5** キーを押し、アプリケーションをビルドして実行します。 アプリを起動した際の更新時には同期が失敗することに注意してください。
3. 新しい項目を入力し、 [MobileServicePushFailedException] をクリックするたびに **CancelledByNetworkError**ステータスでプッシュが失敗することを確認します。 ただし、新しい todo 項目は、モバイル アプリ バックエンドにプッシュされるまでは、ローカル ストア内に存在します。  運用アプリでは、これらの例外を抑制した場合、クライアント アプリはモバイル アプリ バックエンドにまだ接続されているかのように動作します。
4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。
5. (省略可能) Visual Studio で、 **サーバー エクスプローラー**を開きます。 **[Azure]**->**[SQL Databases]** を選択して、データベースに移動します。 データベースを右クリックし、 **[SQL Server オブジェクト エクスプローラーで開く]** を選択します。 これで SQL データベースのテーブルとその内容を参照できます。 バックエンド データベース内のデータが変更されていないことを確認します。
6. (省略可能) Fiddler や Postman などの REST ツールを使用して、モバイルのバックエンドをクエリします。その際、`https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem` の形式で、GET クエリを使用します。

## <a name="update-online-app"></a>モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する
ここでは、アプリをモバイル アプリ バックエンドに再接続します。 これらの変更は、アプリでのネットワークの再接続をシミュレートしています。

初めてアプリケーションを実行すると、`OnNavigatedTo` イベント ハンドラーが `InitLocalStoreAsync` を呼び出します。 このメソッドが次に `SyncAsync` を呼び出し、ローカル ストアとバックエンドのデータベースを同期します。 アプリは起動時に同期しようとします。

1. 共有プロジェクトで App.xaml.cs を開き、正しいモバイル アプリ URL を使用するために以前の `MobileServiceClient` の初期化をコメント解除します。
2. **F5** キーを押して、アプリケーションをリビルドして実行します。 アプリは、`OnNavigatedTo` イベント ハンドラーが実行されると、プッシュとプルの操作によって、ローカルでの変更を Azure Mobile Apps バックエンドに同期します。
3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。 データが同期されるのは、Azure モバイル アプリのバックエンドのデータベースとローカル ストアの間であることに注意してください。
4. アプリケーションで、ローカル ストアで完了させる項目の横にあるチェック ボックスをクリックします。

   `UpdateCheckedTodoItem` は `SyncAsync` を呼び出し、完了した各項目をモバイル アプリ バックエンドと同期します。 `SyncAsync` はプッシュとプルの両方を呼び出します。 ただし、**クライアントが変更したテーブルに対してプルを実行するたびに、プッシュが常に自動的に実行されます**。 この動作は、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。 この動作によって、予期しないプッシュが行われることがあります。  この動作については、「 [Azure Mobile Apps でのオフライン データ同期]」を参照してください。

## <a name="api-summary"></a>API の概要
モバイル サービスのオフライン機能をサポートするために、[IMobileServiceSyncTable] インターフェイスを使用して、ローカル SQLite データベースで [MobileServiceClient.SyncContext][synccontext] を初期化しました。 オフラインのときは、モバイル アプリに対する通常の CRUD 操作は、アプリケーションはまだ接続されているが、操作はローカル ストアに対して発生したかのように動作します。 ローカル ストアをサーバーと同期するには、次のメソッドを使用します。

* **[PushAsync]** このメソッドは [IMobileServicesSyncContext] のメンバーなので、すべてのテーブルに対する変更はバックエンドにプッシュされます。 ローカルに変更されたレコードのみが、サーバーに送信されます。
* **[PullAsync]** プルは [IMobileServiceSyncTable] から開始されます。 テーブルに追跡されている変更がある場合は、ローカル ストア内のすべてのテーブルとリレーションシップの一貫性が保持されるように、暗黙のプッシュが実行されます。 *pushOtherTables* パラメーターは、コンテキスト内の他のテーブルが暗黙のプッシュでプッシュされるかどうかを制御します。 *query* パラメーターは、[IMobileServiceTableQuery<T>][IMobileServiceTableQuery] または OData クエリ文字列を受け取り、返されたデータをフィルター処理します。 *queryId* パラメーターは、増分同期の定義に使用されます。詳細については、「[Azure Mobile Apps でのオフライン データ同期](app-service-mobile-offline-data-sync.md#how-sync-works)」を参照してください。
* **[PurgeAsync]** アプリはこのメソッドを定期的に呼び出して、ローカル ストアから古いデータを消去する必要があります。 まだ同期されていないすべての変更を消去する必要がある場合は、 *force* パラメーターを使用します。

これらの概念の詳細については、「 [Azure Mobile Apps でのオフライン データ同期](app-service-mobile-offline-data-sync.md#how-sync-works)」を参照してください。

## <a name="more-info"></a>詳細情報
Mobile Apps のオフライン同期機能の詳しい背景情報については、以下のトピックを参照してください。

* [Azure Mobile Apps でのオフライン データ同期]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[Create a Windows app (Windows アプリの作成)]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
