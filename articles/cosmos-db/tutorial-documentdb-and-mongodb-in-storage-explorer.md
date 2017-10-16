---
title: "Azure Cosmos DB を Azure Storage Explorer で管理する"
description: "Azure Cosmos DB を Azure Storage Explorer で管理する方法を説明します。"
Keywords: Azure Cosmos DB, Azure Storage Explorer, DocumentDB, MongoDB, DocumentDB
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.openlocfilehash: 2cd3656156b77c71be85a1a18567232f4466fc68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Azure Cosmos DB を Azure Storage Explorer で管理する (プレビュー)

Azure Cosmos DB を Azure Storage Explorer で使用することで、ストレージ BLOB やキューなどの他の Azure エンティティと一緒に Azure Cosmos DB エンティティの管理、データの操作、ストアド プロシージャとトリガーの更新を実行できます。 さまざまな Azure エンティティを、同じツールを使用して 1 か所で管理できるようになりました。 現時点では、Azure Storage Explorer は、SQL (DocumentDB) と MongoDB のアカウントをサポートします。

この記事では、Storage Explorer を使用して Azure Cosmos DB を管理する方法を説明します。


## <a name="prerequisites"></a>前提条件

- SQL (DocumentDB) または MongoDB データベース用の Azure Cosmos DB アカウント。 アカウントを持っていない場合は、Azure ポータルで作成できます。「[Azure Cosmos DB: .NET と Azure Portal による DocumentDB API Web アプリの構築](create-documentdb-dotnet.md)」を参照してください。
- 最新の Azure Storage Explorer をインストールします。 次のリンクからインストールできます。[Linux](https://go.microsoft.com/fwlink/?linkid=858559)[Mac](https://go.microsoft.com/fwlink/?linkid=858561)[Windows](https://go.microsoft.com/fwlink/?linkid=858562)。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

1. **Azure Storage Explorer** をインストールしたら、次の図の左側にある **[プラグイン]** アイコンをクリックします。
       
   ![[プラグイン] アイコン](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** をクリックします。

   ![Azure サブスクリプションに接続する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. **[Azure サインイン]** ダイアログ ボックスで、**[サインイン]** を選択し、Azure 資格情報を入力します。

    ![[サインイン]](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. 一覧からサブスクリプションを選択し、**[適用]** をクリックします。

    ![適用](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    エクスプローラー ウィンドウが更新され、選択したサブスクリプション内のアカウントが表示されます。

    ![アカウントの一覧](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    **Azure Cosmos DB アカウント**から Azure サブスクリプションに正常に接続されています。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>接続文字列を使用して Azure Cosmos DB に接続する

Azure Cosmos DB に接続する別の方法では、接続文字列を使用します。 接続文字列を使用して接続するには、次の手順を使用します。

1. 左側のツリーで **[Local and Attached]\(ローカルで接続済み\)** を見つけ、**[Azure Cosmos DB アカウント]** を右クリックし、**[Azure Cosmos DB に接続...]** を選択します。

    ![接続文字列によって Azure Cosmos DB に接続する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. アカウントの種類に適した **[Default Experience] \(既定のエクスペリエンス\)** (**DocumentDB** または **MongoDB**) を選択し、**[接続文字列]** に接続文字列を貼り付け、**[OK]** をクリックして Azure Cosmos DB アカウントに接続します。 接続文字列の取得については、「[接続文字列を取得する](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string)」を参照してください。

    ![接続文字列](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB のリソース管理

次の操作を実行することで、Azure Cosmos DB アカウントを管理できます。
* Azure ポータルでアカウントを開く
* クイック アクセスの一覧にリソースを追加する
* リソースを検索および更新する
* データベースを作成および削除する
* コレクションを作成および削除する
* ドキュメントを作成、削除、およびフィルター処理する
* ストアド プロシージャ、トリガー、およびユーザー定義関数を管理する

### <a name="quick-access-tasks"></a>クイック アクセス タスク

エクスプローラー ウィンドウでサブスクリプションを右クリックすることで、多数のクイック アクション タスクを実行できます。

* Azure Cosmos DB アカウントまたはデータベースを右クリックし、**[ポータルで開く]** を選択して、Azure ポータルのブラウザーでリソースを管理できます。

     ![ポータルで開く](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* Azure Cosmos DB アカウント、データベース、コレクションを**[クイック アクセス]** に追加することもできます。
* **[Search from Here]\(ここから検索\)** は、選択したパスのキーワード検索を有効にします。

    ![ここから検索](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>データベースとコレクションの管理
#### <a name="create-a-database"></a>データベースの作成 
Azure Cosmos DB アカウントを右クリックし、**[データベースの作成]** を選択し、データベース名を入力し、**Enter** キーを押します。

![データベースを作成する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>データベースの削除
データベースを右クリックし、**[データベースの削除]** をクリックし、ポップアップ ウィンドウで **[はい]** をクリックします。 データベース ノードが削除され、Azure Cosmos DB アカウントが自動的に更新されます。

![database1 を削除する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![database2 を削除する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>コレクションの作成
データベースを右クリックし、**[コレクションの作成]** をクリックし、**[コレクション ID]** や **[ストレージ容量]** などの情報を指定します。**[OK]** をクリックして終了します。 パーティション キーの設定については、「[パーティション分割のための設計](partition-data.md#designing-for-partitioning)」を参照してください。

コレクションの作成時にパーティション キーを使用している場合は、作成が完了した後でコレクションのパーティション キー値を変更することはできません。

![collection1 を作成する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![collection2 を作成する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>コレクションの削除
コレクションを右クリックし、**[コレクションの削除]** をクリックし、ポップアップ ウィンドウで **[はい]** をクリックします。 

コレクション ノードが削除され、データベースが自動的に更新されます。  

![コレクションを削除する](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>ドキュメントの管理

#### <a name="create-and-modify-documents"></a>ドキュメントの作成と変更
新しいドキュメントを作成するには、左側のウィンドウで **[ドキュメント]** を開き、右側のウィンドウで **[新しいドキュメント]** をクリックし、内容を編集し、**[保存]** をクリックします。 既存のドキュメントを更新して **[保存]** をクリックすることもできます。 **[破棄]** をクリックすることで、変更を破棄できます。

![ドキュメント](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>ドキュメントの削除
選択したドキュメントを削除するには、**[削除]** ボタンをクリックします。
#### <a name="query-for-documents"></a>ドキュメントのクエリ
[SQL クエリ](documentdb-sql-query.md)を入力してドキュメントをフィルターし、**[適用]** をクリックします。

![filter](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>ストアド プロシージャ、トリガー、および UDF の管理
* ストアド プロシージャを作成するには、左側のツリーで **[ストアド プロシージャ]** を右クリックし、**[ストアド プロシージャ]** を選択し、左側のウィンドウで名前を入力し、右側のウィンドウでストアド プロシージャのスクリプトを入力し、**[作成]** をクリックします。 
* 既存のストアド プロシージャをダブルクリックし、更新を行った後、**[更新]** をクリックして保存するか、**[破棄]** をクリックして変更を取り消すこともできます。

![ストアド プロシージャ](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* **トリガー**と **UDF** に対する操作は、**ストアド プロシージャ**に対する操作に似ています。

## <a name="demo"></a>デモ
* Azure Cosmos DB を Azure Storage Explorer で使用する方法については、次のビデオをご覧ください。[Use Azure Cosmos DB in Azure Storage Explorer](https://go.microsoft.com/fwlink/?linkid=858710) (Azure Cosmos DB を Azure Storage Explorer で使用する)

## <a name="next-steps"></a>次のステップ

これで、Azure Storage Explorer が Azure Cosmos DB アカウントに接続されました。Storage Explorer と他のサービスへの接続の詳細については、「[ストレージ エクスプローラー (プレビュー) の概要](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer)」を参照してください。

