---
title: .Net SDK を使用して Azure Cosmos DB SQL API アカウントにデータを一括インポートする
description: データをインポートするために必要なプロビジョニング スループット (RU/秒) を最適化する .NET コンソール アプリケーションを作成して、Azure Cosmos DB にデータをインポートしたり取り込んだりする方法を説明します。
author: ealsur
ms.author: maquaran
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 79771e082a4a6ffae15f33f636b0300e93bcdaba
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896276"
---
# <a name="bulk-import-data-to-azure-cosmos-db-sql-api-account-by-using-the-net-sdk"></a>.NET SDK を使用して Azure Cosmos DB SQL API アカウントにデータを一括インポートする

このチュートリアルでは、Azure Cosmos DB にデータをインポートするために必要なプロビジョニング スループット (RU/秒) を最適化する .NET コンソール アプリケーションを構築する方法について説明します。 この記事では、サンプル データ ソースからデータを読み取り、Azure Cosmos コンテナーにインポートします。
このチュートリアルでは、.NET Framework または .NET Core をターゲットとすることができる、Azure Cosmos DB .NET SDK の[バージョン 3.0 以降](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)を使用します。

このチュートリアルの内容:

> [!div class="checklist"]
> * Azure Cosmos アカウントを作成する
> * プロジェクトを構成する
> * 一括サポートが有効になっている Azure Cosmos アカウントに接続する
> * 同時作成操作によるデータ インポートを実行する

## <a name="prerequisites"></a>前提条件

この記事の手順を実行する前に、以下のリソースがあることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [NET Core 3 SDK](https://dotnet.microsoft.com/download/dotnet-core)。 `dotnet --version` を実行すると、お使いの環境で使用可能なバージョンを確認できます。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>手順 1:Azure Cosmos DB アカウントを作成する

Azure portal から [Azure Cosmos DB SQL API アカウントを作成](create-cosmosdb-resources-portal.md)します。または、[Azure Cosmos DB Emulator](local-emulator.md) を使用してアカウントを作成することもできます。

## <a name="step-2-set-up-your-net-project"></a>手順 2:.NET プロジェクトを設定する

ローカル コンピューターから Windows コマンド プロンプトまたはターミナル ウィンドウを開きます。 次のセクションではコマンド プロンプトまたはターミナルからすべてのコマンドを実行します。 次の dotnet new コマンドを実行して、*bulk-import-demo* という名前の新しいアプリを作成します。 `--langVersion` パラメーターによって、作成されたプロジェクト ファイル内に *LangVersion* プロパティが設定されます。

   ```bash
   dotnet new console –langVersion:8 -n bulk-import-demo
   ```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

   ```bash
   cd bulk-import-demo
   dotnet build
   ```

ビルドからの予想される出力は、次のようになります。

   ```bash
   Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo\bulk-import-demo.csproj.
     bulk -> C:\Users\user1\Downloads\CosmosDB_Samples\bulk-import-demo \bin\Debug\netcoreapp2.2\bulk-import-demo.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:34.17
   ```

## <a name="step-3-add-the-azure-cosmos-db-package"></a>手順 3:Azure Cosmos DB パッケージを追加する

アプリケーション ディレクトリ内で、dotnet add package コマンドを使用して .NET Core 用の Azure Cosmos DB クライアント ライブラリをインストールします。

   ```bash
   dotnet add package Microsoft.Azure.Cosmos
   ```

## <a name="step-4-get-your-azure-cosmos-account-credentials"></a>手順 4:Azure Cosmos アカウントの資格情報を取得する

サンプル アプリケーションは、Azure Cosmos アカウントに対する認証を行う必要があります。 認証するには、Azure Cosmos アカウントの資格情報をアプリケーションに渡す必要があります。 次の手順に従って、Azure Cosmos アカウントの資格情報を取得します。

1.  [Azure Portal](https://portal.azure.com/) にサインインします。
1.  Azure Cosmos アカウントに移動します。
1.  **[キー]** ウィンドウを開き、アカウントの **[URI]** と **[プライマリ キー]** をコピーします。

Azure Cosmos DB Emulator を使用している場合は、[こちらの記事からエミュレーターの資格情報](local-emulator.md#authenticating-requests)を取得してください。

## <a name="step-5-initialize-the-cosmosclient-object-with-bulk-execution-support"></a>手順 5:一括実行のサポートを適用して CosmosClient オブジェクトを初期化する

生成された `Program.cs` ファイルをコード エディターで開きます。 一括実行を有効にした CosmosClient の新しいインスタンスを作成し、それを使用して Azure Cosmos DB に対して操作を実行します。 

まず、既定の `Main` メソッドを上書きし、グローバル変数を定義します。 これらのグローバル変数には、エンドポイントと承認キー、データベースの名前、作成するコンテナー、一括で挿入する項目の数が含まれます。 環境に応じて、endpointURL と承認キーの値を必ず置き換えてください。 


   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Diagnostics;
   using System.IO;
   using System.Text.Json;
   using System.Threading.Tasks;
   using Microsoft.Azure.Cosmos;

   public class Program
   {
        private const string EndpointUrl = "https://<your-account>.documents.azure.com:443/";
        private const string AuthorizationKey = "<your-account-key>";
        private const string DatabaseName = "bulk-tutorial";
        private const string ContainerName = "items";
        private const int ItemsToInsert = 300000;

        static async Task Main(string[] args)
        {

        }
   }
   ```

`Main` メソッド内に、CosmosClient オブジェクトを初期化する次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=CreateClient)]

一括実行を有効にすると、CosmosClient によって同時実行操作が単一のサービス呼び出しに内部的にグループ化されます。 これにより、サービス呼び出しを複数のパーティションに分散し、最終的に個々の結果を元の呼び出し元に割り当てることで、スループット利用率が最適化されます。

その後、すべての項目を格納するコンテナーを作成できます。  パーティション キーとして `/pk`、プロビジョニング スループットとして 50000 RU/秒、すべてのフィールドを除外して書き込みスループットを最適化するカスタム インデックス作成ポリシーを定義します。 CosmosClient 初期化ステートメントの後に次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Initialize)]

## <a name="step-6-populate-a-list-of-concurrent-tasks"></a>手順 6:同時実行タスクのリストを設定する

一括実行のサポートを利用するには、データのソースと実行する操作に基づいて非同期タスクのリストを作成し、`Task.WhenAll` を使用してそれらを同時に実行します。
まず、"Bogus" データを使用して、データ モデルから項目のリストを生成します。 実際のアプリケーションでは、項目は目的のデータ ソースから取得されます。

まず、dotnet add package コマンドを使用して、Bogus パッケージをソリューションに追加します。

   ```bash
   dotnet add package Bogus
   ```

保存する項目を定義します。 `Program.cs` ファイル内に `Item` クラスを定義する必要があります。

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Model)]

次に、`Program` クラス内にヘルパー関数を作成します。 このヘルパー関数は、挿入対象として定義された項目の数を取得し、ランダム データを生成します。

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Bogus)]

項目を読み取り、`System.Text.Json` クラスを使用してそれらをストリーム インスタンスにシリアル化します。 自動生成されたデータの性質により、データをストリームとしてシリアル化します。 項目インスタンスを直接使用することもできますが、それらをストリームに変換することによって、CosmosClient でストリーム API のパフォーマンスを活用できます。 通常、パーティション キーがわかっていれば、データを直接使用できます。 


データをストリーム インスタンスに変換するには、`Main` メソッド内で、コンテナーを作成した直後に次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=Operations)]

次に、データ ストリームを使用して同時実行タスクを作成し、タスク リストを設定して項目をコンテナーに挿入します。 この操作を実行するには、`Program` クラスに次のコードを追加します。

[!code-csharp[Main](~/cosmos-dotnet-bulk-import/src/Program.cs?name=ConcurrentTasks)]

概要セクションで説明したように、これらの同時ポイント操作はすべてまとめて (つまり一括で) 実行されます。

## <a name="step-7-run-the-sample"></a>手順 7:サンプルを実行する

サンプルは、`dotnet` コマンドで簡単に実行できます。

   ```bash
   dotnet run
   ```

## <a name="get-the-complete-sample"></a>完全なサンプルを入手する

このチュートリアルの手順を実行する時間がない場合や、コード サンプルをダウンロードするだけの場合は、[GitHub](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer) から入手できます。

プロジェクトを複製したら、[Program.cs](https://github.com/Azure-Samples/cosmos-dotnet-bulk-import-throughput-optimizer/blob/master/src/Program.cs#L25) 内の必要な資格情報を必ず更新してください。

サンプルを実行するには、リポジトリ ディレクトリに変更し、`dotnet` を使用します。

   ```bash
   cd cosmos-dotnet-bulk-import-throughput-optimizer
   dotnet run
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を実行しました。

> [!div class="checklist"]
> * Azure Cosmos アカウントを作成する
> * プロジェクトを構成する
> * 一括サポートが有効になっている Azure Cosmos アカウントに接続する
> * 同時作成操作によるデータ インポートを実行する

これで、次のチュートリアルに進むことができます。

> [!div class="nextstepaction"]
>[SQL API を使って Azure Cosmos DB に対するクエリを実行する](tutorial-query-sql-api.md)