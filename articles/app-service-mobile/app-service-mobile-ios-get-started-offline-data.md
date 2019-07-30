---
title: iOS モバイル アプリでオフライン同期を有効にする | Microsoft Docs
description: Azure App Service Mobile Apps を使用して、iOS アプリケーション内のオフライン データをキャッシュし、同期する方法について説明します。
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f7ae3e7a33ae7df70214ed171b00cc2accbaccb5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446372"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>iOS モバイル アプリでオフライン同期を有効にする
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-offline-data) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このチュートリアルでは、iOS 向け Azure App Service Mobile Apps 機能を使用したオフライン同期について説明します。 オフライン同期により、エンド ユーザーはネットワークに接続していなくても、モバイル アプリを操作してデータを表示、追加、または変更できます。 変更は、ローカル データベースに格納されます。 デバイスが再びオンラインになると、変更がリモート バックエンドと同期されます。

Mobile Apps を初めて使用する場合は、最初に「[iOS アプリの作成]」チュートリアルを完了してください。 ダウンロードしたクイックスタート サーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフライン同期機能の詳細については、[Mobile Apps でのオフライン データ同期]に関する記事をご覧ください。

## <a name="review-sync"></a>クライアント同期コードの確認
「[iOS アプリの作成]」チュートリアルでダウンロードしたクライアント プロジェクトには、ローカルの Core Data に基づいたデータベースを使用したオフライン同期をサポートするコードが既に含まれています。 このセクションでは、チュートリアルのコードに既に含まれているものについて簡単に説明します。 機能の概念的な概要については、[Mobile Apps でのオフライン データ同期]に関する記事をご覧ください。

Mobile Apps のオフライン データ同期機能を使用すると、エンド ユーザーは、ネットワークにアクセスできない場合でもローカル データベースを操作できます。 アプリケーションでこれらの機能を使用するには、 `MSClient` の同期コンテキストを初期化して、ローカル ストアを参照します。 次に、**MSSyncTable** インターフェイスを使用してテーブルを参照します。

**QSTodoService.m** (Objective-C) または **ToDoTableViewController.swift** (Swift) では、メンバー **syncTable** の型は **MSSyncTable** です。 オフライン同期では、**MSTable** の代わりにこの同期テーブル インターフェイスを使用します。 同期テーブルを使用すると、すべての操作はローカル ストアを参照し、明示的なプッシュ操作とプル操作を使用してリモート バックエンドとのみ同期されます。

 同期テーブルへの参照を取得するには、`MSClient` で **syncTableWithName** メソッドを使用します。 オフライン同期機能を削除するには、**tableWithName** を使用します。

テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。 関連するコードを次に示します。

* **Objective-C**: **QSTodoService.init** メソッドのコードを次に示します。

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**: **ToDoTableViewController.viewDidLoad** メソッドのコードを次に示します。

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   このメソッドでは、Mobile Apps SDK で提供される `MSCoreDataStore` インターフェイスを使用してローカル ストアを作成します。 `MSSyncContextDataSource` プロトコルを実装することで、別のローカル ストアを提供することもできます。 また、**MSSyncContext** の最初のパラメーターは、競合ハンドラーを指定するために使用します。 ここでは `nil` を渡したため、既定の競合ハンドラーが取得されます。この競合ハンドラーは、競合が発生すると失敗します。

では、実際の同期操作を実行し、リモート バックエンドからデータを取得してみましょう。

* **Objective-C**: `syncData` で新しい変更をプッシュしてから **pullData** を呼び出すことで、リモート バックエンドからデータを取得します。 次に、**pullData** メソッドがクエリに一致する新しいデータを取得します。

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Objective C バージョンでは、`syncData` で同期コンテキストの最初に **pushWithCompletion** を呼び出しています。 このメソッドは、すべてのテーブルに対して変更をプッシュするため、(同期テーブル自体ではなく) `MSSyncContext` のメンバーです。 (CUD 操作によって) 何らかの方法でローカルで変更されたレコードだけがサーバーに送信されます。 次に、ヘルパー **pullData** が呼び出されます。このヘルパーが **MSSyncTable.pullWithQuery** を呼び出してリモート データを取得し、ローカル データベースに格納します。

Swift バージョンでは、プッシュ操作は必ずしも必要ではなかったため、**pushWithCompletion** の呼び出しはありません。 プッシュ操作を行っているテーブルの同期コンテキストで保留中の変更がある場合は、プルは必ず最初にプッシュを発行します。 ただし、複数の同期テーブルを使用している場合は、関連するテーブル全体で整合性を確保するために、プッシュを明示的に呼び出すことをお勧めします。

Objective-C と Swift のどちらのバージョンでも、**pullWithQuery** メソッドを使用して、取得するレコードをフィルター処理するクエリを指定できます。 この例のクエリでは、リモートの `TodoItem` テーブルのすべてのレコードを取得します。

**pullWithQuery** の 2 番目のパラメーターは、*増分同期*に使用するクエリ ID です。増分同期では、レコードの `UpdatedAt` タイムスタンプ (ローカル ストアでは `updatedAt` と呼ばれます) を使用して、前回の同期以降に変更されたレコードだけを取得します。クエリ ID は、アプリ内の各論理クエリに対して一意の、わかりやすい文字列にする必要があります。 増分同期を解除するには、クエリ ID として `nil` を渡します。 この方法では、プル操作のたびにすべてのレコードを取得するため、非効率的になる可能性があります。

Objective-C のアプリは、データを変更または追加したとき、ユーザーが更新操作を実行したとき、および起動時に同期します。

Swift のアプリは、ユーザーが更新操作を実行したときと起動時に同期します。

アプリはデータが変更されたとき (Objective-C) またはアプリの起動時 (Objective-C と Swift) に同期するため、ユーザーがオンラインであることを前提としています。 後のセクションで、ユーザーがオフラインのときでも編集できるようにアプリを更新します。

## <a name="review-core-data"></a>Core Data モデルの確認
Core Data オフライン ストアを使用するときは、データ モデルで特定のテーブルとフィールドを定義する必要があります。 サンプル アプリケーションには、あらかじめ適切な形式でデータ モデルが含まれています。 このセクションでは、これらのテーブルとその使用方法について説明します。

**QSDataModel.xcdatamodeld** を開きます。 4 つのテーブルが定義されています。3 つは SDK で使用され、1 つは To Do 項目自体に使用されます。
  * MS_TableOperations:サーバーと同期する必要がある項目を追跡します。
  * MS_TableOperationErrors:オフライン同期中に発生したエラーを追跡します。
  * MS_TableConfig:すべてのプル操作に対する最後の同期操作の最終更新時刻を追跡します。
  * TodoItem:To Do アイテムを格納します。 システム列 **createdAt**、**updatedAt**、および **version** は省略可能なシステム プロパティです。

> [!NOTE]
> Mobile Apps SDK では、" **``** " で始まる列名を予約しています。 このプレフィックスは、システム列以外のものに使用しないでください。 そうしないと、リモート バックエンドの使用時に列名が変更されます。
>
>

オフライン同期機能を使用するときは、3 つのシステム テーブルと、データ テーブルを定義します。

### <a name="system-tables"></a>システム テーブル

**MS_TableOperations**  

![MS_TableOperations テーブルの属性][defining-core-data-tableoperations-entity]

| Attribute | Type |
| --- | --- |
| id | Integer 64 |
| itemId | string |
| properties | Binary Data |
| table | string |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors テーブルの属性][defining-core-data-tableoperationerrors-entity]

| Attribute | Type |
| --- | --- |
| id |string |
| operationId |Integer 64 |
| properties |Binary Data |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribute | Type |
| --- | --- |
| id |string |
| key |string |
| keyType |Integer 64 |
| table |string |
| value |string |

### <a name="data-table"></a>データ テーブル

**TodoItem**

| Attribute | Type | Note |
| --- | --- | --- |
| id | String、必須のマーク |リモート ストア内のプライマリ キー |
| complete | Boolean | To Do 項目フィールド |
| text |string |To Do 項目フィールド |
| createdAt | Date | (省略可能) **createdAt** システム プロパティにマップします。 |
| updatedAt | Date | (省略可能) **updatedAt** システム プロパティにマップします。 |
| version | string | (省略可能) 競合の検出に使用され、バージョンにマップします。 |

## <a name="setup-sync"></a>アプリケーションの同期動作を変更する
このセクションでは、アプリの起動時または項目を挿入および更新したときに同期しないように、アプリを変更します。 更新操作ボタンが実行されたときにのみ同期します。

**Objective-C**:

1. **QSTodoListViewController.m** で **viewDidLoad** メソッドを変更して、メソッドの最後にある `[self refresh]` への呼び出しを削除します。 これで、アプリの起動時にデータはサーバーと同期されなくなります。 代わりに、ローカル ストアの内容と同期されます。
2. **QSTodoService.m** で `addItem` の定義を変更して、項目の挿入後に同期しないようにします。 `self syncData` ブロックを削除し、代わりに次を配置します。

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. 前述のように、`completeItem` の定義を変更します。 `self syncData` のブロックを削除し、次のコードに置き換えます。
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

`viewDidLoad` の **ToDoTableViewController.swift** で、次に示す 2 行をコメントアウトしてアプリの起動時に同期しないようにします。 この記事の執筆時点では、Swift Todo アプリはユーザーが項目を追加または完了したときにはサービスを更新せず、 アプリの起動時にのみサービスを更新しています。

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>アプリケーションをテストする
ここでは、無効な URL に接続してオフライン シナリオをシミュレートします。 データ項目を追加すると、ローカル Core Data ストアに保持されますが、モバイル アプリ バックエンドとは同期されません。

1. **QSTodoService.m** のモバイル アプリ URL を無効な URL に変更し、アプリをもう一度実行します。

   **Objective-C**: QSTodoService.m のコードを次に示します。
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**: ToDoTableViewController.swift のコードを次に示します。
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. To Do 項目をいくつか追加します。 シミュレーターを終了し (またはアプリを強制的に閉じて)、再起動します。 変更内容が保持されていることを確認します。

3. リモートの **TodoItem** テーブルの内容を表示します。
   * Node.js バックエンドの場合は、[Azure Portal](https://portal.azure.com/) に移動し、モバイル アプリ バックエンドで **[簡易テーブル]**  >  **、[TodoItem]** をクリックします。  
   * .NET バックエンドの場合は、SQL Server Management Studio などの SQL ツール、または Fiddler や Postman などの REST クライアントを使用します。  

4. 新しい項目がサーバーと同期*されなかった*ことを確認します。

5. **QSTodoService.m** の URL を正しい URL に変更し、アプリを再実行します。

6. 項目の一覧をプルダウンして更新操作を実行します。  
進行状況を示すスピナーが表示されます。

7. **TodoItem** データをもう一度表示します。 今回は、新しい To Do 項目と変更された To Do 項目が表示されます。

## <a name="summary"></a>まとめ
オフライン同期機能をサポートするために、`MSSyncTable` インターフェイスを使用し、ローカル ストアで `MSClient.syncContext` を初期化しました。 この例では、ローカル ストアは Core Data に基づいたデータベースでした。

Core Data ローカル ストアを使用するときは、[正しいシステム プロパティ](#review-core-data)を使用して、複数のテーブルを定義する必要があります。

モバイル アプリでの通常の作成、読み取り、更新、削除 (CRUD) の各操作は、アプリがまだ接続されていても、すべての操作がローカル ストアに対して発生したかのように動作します。

ローカル ストアをサーバーと同期するときに、**MSSyncTable.pullWithQuery** メソッドを使用しました。

## <a name="additional-resources"></a>その他のリソース
* [Mobile Apps でのオフライン データ同期]
* [Cloud Cover:Azure Mobile Services でのオフライン同期] \(このビデオは Mobile Services に関するものですが、Mobile Apps のオフライン同期も同様に機能します。\)

<!-- URLs. -->


[iOS アプリの作成]: app-service-mobile-ios-get-started.md
[Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover:Azure Mobile Services でのオフライン同期]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
