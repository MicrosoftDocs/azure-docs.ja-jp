---
title: Azure Cosmos DB の C# ノートブックで組み込みのノートブック コマンドと機能を使用する (プレビュー)
description: Azure Cosmos DB の組み込み C# ノートブックを使用して、組み込みのコマンドと機能で一般的な操作を実行する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340481"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Azure Cosmos DB の C# ノートブックで組み込みのノートブック コマンドと機能を使用する (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB の組み込みの Jupyter ノートブックを使用すると、Azure portal のデータを分析して視覚化することができます。 この記事では、C# ノートブックで組み込みのノートブック コマンドと機能を使用して一般的な操作を行う方法について説明します。

## <a name="install-a-new-nuget-package"></a>新しい NuGet パッケージをインストールする
Azure Cosmos アカウントのノートブック サポートを有効にしたら、新しいノートブックを開き、パッケージをインストールできます。

新しいコード セルに次のコードを挿入して実行します。このとき、``PackageToBeInstalled`` を目的の NuGet パッケージに置き換え、必要に応じて ``optionalVersion`` をそのパッケージの特定のバージョンに置き換えます。 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

同じセルに複数の NuGet パッケージをインストールできます。 パッケージは、Azure Cosmos アカウント ワークスペースの任意のノートブックから使用できます。 

現在、C# ノートブック ワークスペースでは、NuGet パッケージの再帰的解決はサポートされていません。 ある NuGet パッケージが、現在はインストールされていない他の NuGet パッケージに依存している場合は、それらを親パッケージと一緒に明示的に参照する必要があります。

> [!TIP]
> お使いのノートブックにカスタム パッケージが必要な場合、そのパッケージをインストールするためにノートブックにセルを追加し、そのセルを最初のセルにすることをお勧めします。 このようにすることで、Azure Cosmos DB が既定で読み込む他のパッケージとの競合の可能性を抑えることができます。 また、[ワークスペースをリセットする](#reset-notebooks-workspace)とすべてのパッケージが削除されるので、パッケージを簡単に再インストールできます。 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>組み込みの Azure Cosmos DB .NET SDK を使用する
[Azure Cosmos DB .NET SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) のバージョン 3 は、Azure Cosmos アカウントのノートブック環境にインストールされて含まれています。

``CosmosClient`` のインスタンスを作成することで、任意の SDK 操作を実行できます。 

次に例を示します。

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
詳細については、[.NET V3 SDK のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage)を参照してください。 

> [!IMPORTANT]
> 組み込みの Azure Cosmos DB .NET SDK は、SQL (Core) API アカウントでのみサポートされています。 その他の API については、その API に対応する[関連 .NET ドライバーをインストールする](#install-a-new-nuget-package)必要があります。 

## <a name="set-custom-options-using-cosmosclientoptions"></a>``CosmosClientOptions`` を使用してカスタム オプションを設定する
柔軟性を高めるために、カスタムの ``CosmosClientOptions`` プロパティを設定し、``CosmosClient`` インスタンスに渡すことができます。 このプロパティを使用すると、次のことを行えます。

- ユーザー エージェント サフィックスにアプリケーション名を設定して、それがすべての要求に含まれるようにする。
- サービスに対して操作を実行するときに使用する優先リージョンを設定する。
- レート制限されている要求を処理するカスタム再試行ポリシーを設定する。

サポートされているすべての設定については、[CosmosClientOptions API のリファレンス](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions)の記事を参照してください。 `cosmosClientOptions` プロパティの設定方法の例を次に示します。

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>アカウント エンドポイントと主キーの変数にアクセスする
お使いのノートブックが存在する Azure Cosmos アカウントの組み込みのエンドポイントとキーにアクセスできます。

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key`` 変数と ``Cosmos.Endpoint`` 変数は、SQL API にのみ適用されます。 その他の API については、お使いの Azure Cosmos アカウントの **[接続文字列]** または **[キー]** ブレードでエンドポイントとキーを見つけてください。  

## <a name="print-console-output-in-c-code"></a>C# コードでコンソール出力を出力する
C# コードで、Display.AsMarkdown() 構文を[文字列補間](/dotnet/csharp/language-reference/tokens/interpolated)と共に使用すると、セルを実行したときに表示されるコンソール出力を出力できます。 

次に例を示します。 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Console.WriteLine() 構文は、現在は C# ノートブックではサポートされていません。 プログラムからコンソール出力を出力するには、Display.AsMarkdown を使用します。 

## <a name="use-built-in-nteract-data-explorer"></a>組み込みの nteract データ エクスプローラーを使用する
組み込みの [nteract データ エクスプローラー](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)を使用すると、項目のコレクションをフィルター処理したり視覚化したりできます。 セルで、視覚化する変数を最後の行に配置します。すると、セルを実行するときに、その変数が nteract に自動的に表示されます。

たとえば、*GetingStarted_Csharp.ipynb* の例では、変数 ``telemetryEvents`` を結果と共に出力できます。 サンプル全体については、[ノートブックの GettingStarted_Csharp.ipynb](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) を参照してください。 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Csharp のクエリ セル":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract データ エクスプローラー":::

## <a name="use-built-in-dictionary-viewer"></a>組み込みの辞書ビューアーを使用する
組み込みの辞書ビューアーを使用して、変数を表示できます。 セルで、視覚化する変数を最後の行に配置します。すると、セルを実行するときに、その変数が自動的に表示されます。

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="組み込みの辞書ビューアー":::

## <a name="upload-json-items-to-a-container"></a>JSON 項目をコンテナーにアップロードする
``%%upload`` マジックコマンドを使用して、JSON ファイルから指定された Azure Cosmos コンテナーにデータをアップロードできます。 次のコマンドを使用して項目をアップロードします。

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` と ``{container_id}`` をお使いの Azure Cosmos アカウントにあるデータベースとコンテナーの名前に変更します。 
- ``{url_location_of_file}`` を JSON ファイルの場所に置き換えます。 ファイルは有効な JSON オブジェクトの配列である必要があり、パブリック インターネット経由でアクセスできる必要があります。

次に例を示します。

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

出力統計を使用すると、項目のアップロードに使用される有効な RU/秒を計算できます。 たとえば、25000 の RU が 38 秒で使用された場合、有効な RU/秒は 25000ru/38 秒 = 658 RU/秒になります。

## <a name="run-another-notebook-in-current-notebook"></a>現在のノートブックで別のノートブックを実行する 
``%%run`` マジック コマンドを使用すると、現在のノートブックから、ワークスペース内の別のノートブックを実行できます。 次の構文を使用します。

```csharp
%%run ./path/to/{notebookName}.ipynb
```
``{notebookName}`` は、実行するノートブックの名前に置き換えてください。 そのノートブックは、現在の "マイ ノートブック" ワークスペースにある必要があります。 

## <a name="reset-notebooks-workspace"></a>ノートブック ワークスペースをリセットする
ノートブック ワークスペースをリセットし、既定の設定に戻すには、コマンド バーで **[Reset Workspace]\(ワークスペースのリセット\)** を選択します。 これでインストールされているカスタム パッケージがすべて削除され、Jupyter サーバーが再起動されます。 ノートブック、ファイル、Azure Cosmos リソースは影響を受けません。  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="ノートブック ワークスペースをリセットする":::

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB Jupyter ノートブック](cosmosdb-jupyter-notebooks.md)の長所について学習する
- [Azure Cosmos DB .NET SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) について学習する
