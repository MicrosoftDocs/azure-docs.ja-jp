---
title: .NET V4 SDK を使用して Azure Cosmos DB SQL API リソースを管理する
description: Azure Cosmos DB SQL API アカウント リソースを管理するための .NET V4 SDK を使用したコンソール アプリを構築するクイック スタート。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/22/2020
ms.custom: devx-track-dotnet
ms.openlocfilehash: 224a1b67ff0282c216763229593fcfed81d7567b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090105"
---
# <a name="quickstart-build-a-console-app-using-the-net-v4-sdk-to-manage-azure-cosmos-db-sql-api-account-resources"></a>クイック スタート:Azure Cosmos DB SQL API アカウント リソースを管理するための .NET V4 SDK を使用したコンソール アプリを構築する。
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

.NET 用の Azure Cosmos DB SQL API クライアント ライブラリの概要について説明します。 このドキュメントの手順に従って .NET V4 (Azure.Cosmos) パッケージをインストールし、アプリをビルドして、Azure Cosmos DB に格納されているデータに対する基本的な CRUD 操作のサンプル コードを試してみてください。 

Azure Cosmos DB は、あらゆる規模に対応する、オープン API を備えた Microsoft の高速 NoSQL データベースです。 Azure Cosmos DB を使用すると、キー/値データベース、ドキュメント データベース、およびグラフ データベースをすばやく作成し、クエリを実行できます。 .NET 用の Azure Cosmos DB SQL API クライアント ライブラリを使用して、次のことを行います。

* Azure Cosmos データベースとコンテナーを作成する
* コンテナーにサンプル データを追加する
* データにクエリを実行する 
* データベースを削除する

[ライブラリのソース コード](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/v4) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Cosmos)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料で作成する](https://azure.microsoft.com/free/)か、Azure サブスクリプションを使用せず、料金やコミットメントなしで、[Azure Cosmos DB を無料で試用](https://azure.microsoft.com/try/cosmosdb/)できます。 
* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core)。 `dotnet --version` を実行すると、お使いの環境で使用可能なバージョンを確認できます。

## <a name="setting-up"></a>設定

このセクションでは、Azure Cosmos アカウントを作成し、.NET 用 Azure Cosmos DB SQL API クライアント ライブラリを使用してリソースを管理するプロジェクトを設定する手順について説明します。 この記事で説明するコード例では `FamilyDatabase` データベースと、そのデータベース内にファミリ メンバー (各ファミリ メンバーが項目) を作成します。 各ファミリ メンバーには `Id, FamilyName, FirstName, LastName, Parents, Children, Address,` などのプロパティがあります。 `LastName` プロパティは、コンテナーのパーティション キーとして使用されます。 

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos アカウントを作成する

[[Azure Cosmos DB を無料で試す]](https://azure.microsoft.com/try/cosmosdb/) オプションを使用して Azure Cosmos アカウントを作成する場合は、種類が **SQL API** の Azure Cosmos DB アカウントを作成する必要があります。 Azure Cosmos DB テスト アカウントは既に作成されています。 アカウントを明示的に作成する必要はないため、このセクションをスキップして次のセクションに進むことができます。

独自の Azure サブスクリプションを所有しているか、または無料のサブスクリプションを作成した場合は、Azure Cosmos アカウントを明示的に作成する必要があります。 次のコードでは、セッションの整合性を持つ Azure Cosmos アカウントを作成します。 アカウントは `South Central US` と `North Central US` でレプリケートされます。  

Azure Cloud Shell を使用して、Azure Cosmos アカウントを作成できます。 Azure Cloud Shell は、Azure リソースを管理するための、ブラウザーでアクセスできる対話形式の認証されたシェルです。 Bash または PowerShell どちらかのシェル エクスペリエンスを作業方法に合わせて柔軟に選択できます。 このクイックスタートでは、 **[Bash]** モードを選択します。 Azure Cloud Shell ではストレージ アカウントも必要です。これはプロンプトが表示されたら作成できます。

次のコードの横にある **[使ってみる]** ボタンを選択し、 **[Bash]** モードを選択します。 **[ストレージ アカウントを作成する]** を選択して Cloud Shell にログインします。 次に、次のコードをコピーして Azure Cloud Shell に貼り付けて実行します。 Azure Cosmos アカウント名はグローバルに一意である必要があります。必ず `mysqlapicosmosdb` 値を更新してからコマンドを実行してください。

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'

# The Azure Cosmos account name must be globally unique, make sure to update the `mysqlapicosmosdb` value before you run the command
accountName='mysqlapicosmosdb'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-region writes enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

Azure Cosmos アカウントの作成にはしばらく時間がかかります。操作が正常に終了したら、確認出力が表示されます。 コマンドが正常に終了したら、[Azure portal](https://portal.azure.com/) にサインインし、指定した名前の Azure Cosmos アカウントが存在することを確認します。 リソースの作成後に、[Azure Cloud Shell] ウィンドウを閉じることができます。 

### <a name="create-a-new-net-app"></a><a id="create-dotnet-core-app"></a>新しい .NET アプリを作成する

好みのエディターまたは IDE で、新しい .NET アプリケーションを作成します。 ローカル コンピューターから Windows コマンド プロンプトまたはターミナル ウィンドウを開きます。 次のセクションではコマンド プロンプトまたはターミナルからすべてのコマンドを実行します。  次の dotnet new コマンドを実行して、`todo` という名前の新しいアプリを作成します。 作成したプロジェクト ファイル内には、--langVersion パラメーターによって、LangVersion プロパティが設定されます。

   ```bash
   dotnet new console –langVersion:8 -n todo
   ```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

   ```bash
   cd todo
   dotnet build
   ```

ビルドからの予想される出力は、次のようになります。

```bash
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp3.0\todo.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a name="install-the-azure-cosmos-db-package"></a><a id="install-package"></a>Azure Cosmos DB パッケージをインストールする

アプリケーション ディレクトリ内で、dotnet add package コマンドを使用して .NET Core 用の Azure Cosmos DB クライアント ライブラリをインストールします。

   ```bash
   dotnet add package Azure.Cosmos --version 4.0.0-preview3
   ```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Azure portal から Azure Cosmos アカウントの資格情報をコピーする

サンプル アプリケーションは、Azure Cosmos アカウントに対する認証を行う必要があります。 認証するには、Azure Cosmos アカウントの資格情報をアプリケーションに渡す必要があります。 次の手順に従って、Azure Cosmos アカウントの資格情報を取得します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos アカウントに移動します。

1. **[キー]** ウィンドウを開き、アカウントの **[URI]** と **[プライマリ キー]** をコピーします。 次の手順では、URI とキーの値を環境変数に追加します。

## <a name="object-model"></a><a id="object-model"></a>オブジェクト モデル

アプリケーションのビルドを開始する前に、Azure Cosmos DB のリソースの階層と、これらのリソースの作成とアクセスに使用されるオブジェクト モデルについて説明します。 Azure Cosmos DB によって、次の順序でリソースが作成されます。

* Azure Cosmos アカウント 
* データベース 
* Containers 
* アイテム

さまざまなエンティティの階層の詳細については、[Azure Cosmos DB のデータベース、コンテナー、および項目の操作](account-databases-containers-items.md)に関する記事を参照してください。 これらのリソースとやり取りするには、以下の .NET クラスを使用します。

* CosmosClient - このクラスは、Azure Cosmos DB サービスのクライアント側の論理表現を提供します。 このクライアント オブジェクトは、サービスに対する要求の構成と実行に使用されます。
* CreateDatabaseIfNotExistsAsync - このメソッドによって、非同期操作としてデータベース リソースが (存在しない場合は) 作成されるか、(既に存在する場合は) 取得されます。 
* CreateContainerIfNotExistsAsync - このメソッドによって、非同期操作としてコンテナーが (存在しない場合は) 作成されるか、(既に存在する場合は) 取得されます。 応答から状態コードを確認して、コンテナーが新しく作成された (201) か、既存のコンテナーが返された (200) かを判断できます。 
* CreateItemAsync - このメソッドによって、コンテナー内に項目が作成されます。
* UpsertItemAsync - このメソッドでは、アイテムがまだ存在しない場合、コンテナー内にアイテムが作成され、既に存在する場合、アイテムが置換されます。 
* GetItemQueryIterator - このメソッドにより、SQL ステートメントとパラメーター化された値を利用して Azure Cosmos データベースのコンテナーの下でアイテムに対するクエリが作成されます。 
* DeleteAsync - 指定されたデータベースを Azure Cosmos アカウントから削除します。 `DeleteAsync` メソッドでは、データベースのみが削除されます。

 ## <a name="code-examples"></a><a id="code-examples"></a>コード例

この記事で説明されているサンプル コードでは、Azure Cosmos DB でファミリ データベースを作成します。 ファミリ データベースには、名前、住所、場所、関連付けられている親、子供、ペットなどのファミリの詳細が含まれています。 データを Azure Cosmos アカウントに設定する前に、ファミリ項目のプロパティを定義します。 サンプル アプリケーションのルート レベルに `Family.cs` という名前の新しいクラスを作成し、次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Family.cs)]

### <a name="add-the-using-directives--define-the-client-object"></a>using ディレクティブの追加とクライアント オブジェクトの定義と

プロジェクト ディレクトリからエディターで `Program.cs` ファイルを開き、アプリケーションの先頭に次の using ディレクティブを追加します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Usings)]


`Program` クラスに、次のグローバル変数を追加します。 これには、エンドポイント キーと承認キー、データベースの名前、および作成するコンテナーが含まれます。 環境に応じて、エンドポイント キーと承認キーの値を必ず置き換えてください。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Constants)]

最後に、`Main` メソッドを置き換えます。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=Main)]

### <a name="create-a-database"></a>データベースを作成する 

`program.cs` クラス内に `CreateDatabaseAsync` メソッドを定義します。 このメソッドによって、`FamilyDatabase` が作成されます (まだ存在しない場合)。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateDatabaseAsync)]

### <a name="create-a-container"></a>コンテナーを作成する

`Program` クラス内に `CreateContainerAsync` メソッドを定義します。 このメソッドによって、`FamilyContainer` が作成されます (まだ存在しない場合)。 

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=CreateContainerAsync)]

### <a name="create-an-item"></a>項目を作成する

次のコードを使用して、`AddItemsToContainerAsync` メソッドを追加してファミリ項目を作成します。 `CreateItemAsync` または `UpsertItemAsync` のメソッドを使用し、アイテムを作成できます。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=AddItemsToContainerAsync)]

### <a name="query-the-items"></a>項目に対してクエリを実行する

項目を挿入した後、クエリを実行して "Andersen" ファミリの詳細を取得できます。 次のコードは、SQL クエリを直接使用してクエリを実行する方法を示しています。 "Anderson" ファミリの詳細を取得する SQL クエリは `SELECT * FROM c WHERE c.LastName = 'Andersen'` です。 `Program` クラス内で `QueryItemsAsync` メソッドを定義し、次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=QueryItemsAsync)]

### <a name="replace-an-item"></a>項目を置換する 

次のコードで `ReplaceFamilyItemAsync` メソッドを追加して、ファミリ項目を読み取り、それを更新します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=ReplaceFamilyItemAsync)]

### <a name="delete-an-item"></a>項目を削除する 

次のコードで `DeleteFamilyItemAsync` メソッドを追加して、ファミリ項目を削除します。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteFamilyItemAsync)]

### <a name="delete-the-database"></a>データベースを削除する 

最後に、次のコードを使用して `DeleteDatabaseAndCleanupAsync` メソッドを追加し、データベースを削除できます。

[!code-csharp[Main](~/cosmos-dotnet-v4-getting-started/src/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

すべての必要なメソッドを追加したら、`Program` ファイルを保存します。 

## <a name="run-the-code"></a>コードの実行

次に、アプリケーションをビルドして実行し、Azure Cosmos DB リソースを作成します。

   ```bash
   dotnet run
   ```

アプリケーションを実行すると、次の出力が生成されます。 また、Azure portal にサインインして、リソースが作成されたことを確認することもできます。

   ```bash
   Created Database: FamilyDatabase
   
   Created Container: FamilyContainer
   
   Created item in database with id: Andersen.1
   
   Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'
   
           Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null   "FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets": [{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}
   
   Updated Family [Wakefield,Wakefield.7].
           Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"}   {"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6   "Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1   "Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}
   
   Deleted Family [Wakefield,Wakefield.7]
   
   Deleted Database: FamilyDatabase
   
   End of demo, press any key to exit.
   ```

データが作成されたことを確認するには、Azure portal にサインインし、Azure Cosmos アカウントで必要な項目を確認します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、Azure CLI または Azure PowerShell を使用して、Azure Cosmos アカウントとそれに対応するリソース グループを削除できます。 次のコマンドは、Azure CLI を使用してリソース グループを削除する方法を示しています。

```azurecli
az group delete -g "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos アカウントを作成し、.NET Core アプリを使用してデータベースとコンテナーを作成する方法を説明しました。 これで、次の記事の指示に従って Azure Cosmos アカウントに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)
