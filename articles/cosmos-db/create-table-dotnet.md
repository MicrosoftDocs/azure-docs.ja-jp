---
title: クイック スタート:.NET での Table API の使用 - Azure Cosmos DB
description: このクイックスタートでは、Azure Cosmos DB Table API を使用して Azure Portal と .NET でアプリケーションを作成する方法を示します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096939"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>クイック スタート:.NET SDK と Azure Cosmos DB で Table API アプリをビルドする 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

このクイックスタートでは、GitHub から例を複製することで、.NET と Azure Cosmos DB の [Table API](table-introduction.md) を使用してアプリを構築する方法を示します。 このクイックスタートでは、Web ベースの Azure Portal で Azure Cosmos DB アカウントを作成する方法、およびデータ エクスプローラーを使用してテーブルとエンティティを作成する方法も説明します。

## <a name="prerequisites"></a>前提条件

Visual Studio 2019 をまだインストールしていない場合は、**無料** の [Visual Studio 2019 Community エディション](https://www.visualstudio.com/downloads/)をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>データベース アカウントの作成

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>テーブルの追加

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub で Table アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. コマンド プロンプトを開いて git-samples という名前の新しいフォルダーを作成し、コマンド プロンプトを閉じます。

   ```bash
   md "C:\git-samples"
   ```

2. git bash などの git ターミナル ウィンドウを開いて、`cd` コマンドを使用して、サンプル アプリをインストールする新しいフォルダーに変更します。

   ```bash
   cd "C:\git-samples"
   ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> 類似のコードの詳細なチュートリアルについては、[Cosmos DB Table API サンプル](./tutorial-develop-table-dotnet.md)に関する記事を参照してください。

## <a name="open-the-sample-application-in-visual-studio"></a>Visual Studio でサンプル アプリケーションを開く

1. Visual Studio で、 **[ファイル]** メニューから **[開く]** を選択して、 **[プロジェクト/ソリューション]** を選択します。 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="ソリューションを開く"::: 

2. サンプル アプリケーションを複製したフォルダーに移動し、TableStorage.sln ファイルを開きます。

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、このドキュメントの「[接続文字列を更新する](#update-your-connection-string)」セクションに進んでください。

* 次のコードは、Azure Storage 内にテーブルを作成する方法を示しています。

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* 次のコードは、テーブルにデータを挿入する方法を示しています。

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* 次のコードは、テーブルにデータを照会する方法を示しています。

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* 次のコードは、テーブルのデータを削除する方法を示しています。

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 これでアプリが、ホストされているデータベースと通信できます。 

1. [Azure Portal](https://portal.azure.com/) で **[接続文字列]** をクリックします。 ウィンドウの右側にある [コピー] ボタンを使って **プライマリ接続文字列** をコピーします。

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="[接続文字列] ウィンドウでプライマリ接続文字列を確認してコピーする":::

2. Visual Studio で **Settings.json** ファイルを開きます。 

3. ポータルの **プライマリ接続文字列** を StorageConnectionString に貼り付けます。 引用符の内側に文字列を貼り付けます。

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. CTRL キーを押しながら S キーを押して **Settings.json** ファイルを保存します。

これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。 

## <a name="build-and-deploy-the-app"></a>アプリを構築してデプロイする

1. Visual Studio の **ソリューション エクスプローラー** で **CosmosTableSamples** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="NuGet パッケージの管理":::

2. NuGet の **[参照]** ボックスに「Microsoft.Azure.Cosmos.Table」と入力します。 これで、Cosmos DB テーブル API のクライアント ライブラリが見つかります。 このライブラリは、現在 .NET Framework と .NET Standard で使用できることにご留意ください。 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="NuGet の [参照] タブ":::

3. **[インストール]** をクリックして **Microsoft.Azure.Cosmos.Table** ライブラリをインストールします。 これにより、Azure Cosmos DB Table API パッケージとすべての依存関係がインストールされます。

4. アプリ全体を実行すると、サンプル データがテーブル エンティティに挿入され、終わりのところで削除されます。そのため、サンプル全体を実行した場合、データが挿入されても表示されません。 ただし、ブレークポイントを挿入することでデータを表示できます。 BasicSamples.cs ファイルを開き、52 行目を右クリックし、 **[ブレークポイント]** と **[ブレークポイントの挿入]** を順に選択します。 55 行目にもブレークポイントを挿入します。

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="ブレークポイントの追加"::: 

5. F5 キーを押してアプリケーションを実行します。 コンソール ウィンドウに、Azure Cosmos DB の新しいテーブル データベースの名前 (この場合は demoa13b1) が表示されます。 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="コンソール出力":::

   最初のブレークポイントにヒットしたら、Azure Portal のデータ エクスプローラーに戻ります。 **[最新の情報に更新]** ボタンをクリックし、demo* table を展開して **[エンティティ]** をクリックします。 右側の **[エンティティ]** タブに、追加された Walter Harp の新しいエンティティが表示されます。 新しいエンティティの電話番号が 425-555-0101 であることを確認します。

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="新しいエンティティ":::
    
   プロジェクトを実行するときに Settings.json ファイルが見つからないというエラーが発生した場合は、次の XML エントリをプロジェクトの設定に追加することで解決できます。 CosmosTableSamples を右クリックし、[CosmosTableSamples.csproj の編集] を選択して、次の itemGroup を追加します。 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. データ エクスプローラーの **[エンティティ]** タブを閉じます。
    
7. F5 キーを押して、次のブレークポイントまでアプリを実行します。 

    ブレークポイントにヒットしたら、Azure Portal に戻り、 **[エンティティ]** を再びクリックして **[エンティティ]** タブを開き、電話番号が 425-555-0105 に更新されていることを確認します。

8. F5 キーを押して、アプリを実行します。 
 
   アプリによって高度なサンプル アプリで使用するためのエンティティが追加されますが、これらはテーブル API では現在サポートされていません。 その後、アプリは、サンプル アプリによって作成されたテーブルを削除します。

9. コンソール ウィンドウで Enter キーを押してアプリの実行を終了します。 
  

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してテーブルを作成し、アプリを実行する方法を説明しました。  これで、Table API を使用して、データをクエリできます。  

> [!div class="nextstepaction"]
> [テーブル データを Table API にインポートする](table-import.md)