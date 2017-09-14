---
title: "Table API を使用した Azure Cosmos DB .NET アプリケーションの構築 | Microsoft Docs"
description: ".NET を使用した Azure Cosmos DB の Table API の概要"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/22/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 29e7eebda5177d6e852ef04ad82d9d38a8d30ed8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017

---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Table API を使用した .NET アプリケーションの構築

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、キー/値、およびグラフ データベースをすばやく作成およびクエリできます。 

このクイック スタートでは、Azure Cosmos DB アカウントを作成する方法と、Azure Portal を使用してそのアカウント内にテーブルを作成する方法を説明します。 次に、エンティティを挿入、更新、削除するコードを記述し、NuGet の新しい [Windows Azure Storage Premium Table](https://aka.ms/premiumtablenuget) (プレビュー) パッケージを使用してクエリを実行します。 このライブラリには、公開されている [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage) と同じクラスおよびメソッド シグネチャが含まれます。ただし、[Table API](table-introduction.md) (プレビュー) を使用して Azure Cosmos DB アカウントに接続する機能もあります。 

## <a name="prerequisites"></a>前提条件

まだ Visual Studio 2017 をインストールしていない場合は、**無料**の [Visual Studio 2017 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>テーブルの追加

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>サンプル データの追加

これで、データ エクスプローラー (プレビュー) を使用して、新しいテーブルにデータを追加できます。

1. データ エクスプローラーで、**sample-table** を展開し、**[エンティティ]**、**[エンティティの追加]** の順にクリックします。

   ![Azure Portal のデータ エクスプローラーで新しいエンティティを作成する](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. PartitionKey 値ボックスと RowKey 値ボックスにデータを追加し、**[Add Entity] \(エンティティの追加)** をクリックします。

   ![新しいエンティティのパーティション キーと行キーを設定する](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    これで、テーブルにさらにエンティティを追加したり、エンティティを編集したり、データ エクスプローラーでデータをクエリしたりできます。 データ エクスプローラーでは、スループットを拡張し、ストアド プロシージャ、ユーザー定義関数、およびトリガーをテーブルに追加することもできます。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

github で Table アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

2. 次のコマンドを実行して、サンプル レポジトリを複製します。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. 次に、Visual Studio でソリューション ファイルを開きます。 

## <a name="review-the-code"></a>コードの確認

アプリで何が行われているかを簡単に確認してみましょう。 Program.cs ファイルを開くと、以下のコード行によって、Azure Cosmos DB リソースが作成されていることがわかります。 

* CloudTableClient が初期化されます。

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* テーブルが存在しない場合は新しいテーブルが作成されます。

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* 新しいテーブル コンテナーが作成されます。 このコードは通常の Azure Table Storage SDK によく似ています。 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>接続文字列を更新する

今度は、アプリから Azure Cosmos DB に接続できるように接続文字列情報を更新します。 

1. Visual Studio で app.config ファイルを開きます。 

2. [Azure Portal](http://portal.azure.com/) で左側の Azure Cosmos DB ナビゲーション メニューにある **[接続文字列]** をクリックします。 次に、新しいウィンドウで接続文字列のコピー ボタンをクリックします。 

    ![[接続文字列] ウィンドウの [エンドポイント] と [アカウント キー] を確認してコピーします。](./media/create-table-dotnet/keys.png)

3. これを app.config ファイルに PremiumStorageConnectionString の値として貼り付けます。 

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`    

    StandardStorageConnectionString はそのまま使用できます。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="run-the-web-app"></a>Web アプリの実行

1. Visual Studio の**ソリューション エクスプローラー**で **[PremiumTableGetStarted]** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

2. NuGet の **[参照]** ボックスに「*WindowsAzure.Storage-PremiumTable*」と入力します。

3. **[リリース前のパッケージを含める]** をオンにします。 

4. 結果から、**WindowsAzure.Storage-PremiumTable** ライブラリをインストールします。 これにより、プレビュー Azure Cosmos DB Table API パッケージとすべての依存関係がインストールされます。 これは、Azure Table Storage で使用される Windows Azure Storage パッケージとは異なる NuGet パッケージであることに注意してください。 

5. Ctrl + F5 キーを押してアプリケーションを実行します。

    テーブルに追加されているデータ、テーブルから取得されているデータ、照会されているデータ、置換されているデータ、テーブルから削除されているデータがコンソール ウィンドウに表示されます。 スクリプトが完了したら、任意のキーを押してコンソール ウィンドウを閉じます。 
    
    ![クイック スタートのコンソール出力](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-console-output.png)

6. データ エクスプローラーに新しいエンティティを表示したい場合は、program.cs の 188 ～ 208 行目を (削除せずに) コメントアウトしてから、もう一度サンプルを実行してください。 

    その後、データ エクスプローラーに戻って、**[最新の情報に更新]** をクリックします。**[people]** テーブルを展開し、**[エンティティ]** をクリックすると、この新しいデータを操作できます。 

    ![データ エクスプローラー内の新しいエンティティ](./media/create-table-dotnet/azure-cosmosdb-table-quickstart-data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリの使用を続けない場合は、以下の手順に従い、Azure Portal でこのクイック スタートで作成したすべてのリソースを削除してください。 

1. Azure Portal の左側のメニューで、**[リソース グループ]** をクリックし、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、アプリを実行する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [Table API を使用したクエリ](tutorial-query-table.md)


