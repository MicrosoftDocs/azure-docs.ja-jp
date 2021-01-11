---
title: .NET for Apache Spark を使用する
description: バッチ処理、リアルタイム ストリーミング、機械学習を行うため、および Azure Synapse Analytics ノートブック内でアドホック クエリを作成するために、.NET および Apache Spark の使用について説明します。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 677b54a09a446ef8af0b410faea9f94b71634712
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999039"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Azure Synapse Analytics で .NET for Apache Spark を使用する

[.NET for Apache Spark](https://dot.net/spark) によって、無料のオープンソースであり、クロスプラットフォームの Spark 用 .NET サポートが提供されます。 

これには、C# および F# を介して Spark API にアクセスできるようにする Spark 用 .NET バインディングが用意されています。 .NET for Apache Spark には、.NET で記述されたにSpark 用のユーザー定義関数を実行する機能もあります。 Spark 用の .NET API を使用すると、Spark SQL、Delta Lake、構造化ストリーミングなど、ご利用のデータの分析に役立つ Spark DataFrames のあらゆる側面にアクセスすることができます。

.NET for Apache Spark を、Spark バッチ ジョブ定義を介して使用するか、またはインタラクティブな Azure Synapse Analytics ノートブックで使用することで、データを分析することができます。 この記事では、両方の手法を使用して、Azure Synapse で .NET for Apache Spark を使用する方法について説明します。

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Spark ジョブ定義を使用してバッチ ジョブを送信する

Azure Synapse Analytics を使用して [Synapse Spark プールの Apache Spark ジョブ定義を作成する](apache-spark-job-definitions.md)方法を学習するチュートリアルにアクセスしてください。 Azure Synapse に送信するアプリをパッケージ化していない場合は、次の手順を行ってください。

1. 次のコマンドを実行してアプリを発行します。 *mySparkApp* を必ずそのアプリのパスに置き換えてください。

   **Windows の場合:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

   **Linux の場合:**

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics ノートブックでの .NET for Apache Spark 

ノートブックは、.NET for Apache Spark パイプラインおよびシナリオのプロトタイプを作成する場合に便利なオプションです。 ご利用のデータの操作、理解、フィルター処理、表示、視覚化を迅速かつ効率的に開始できます。 データ エンジニア、データ サイエンティスト、ビジネス アナリスト、機械学習エンジニアは皆、共有されたインタラクティブなドキュメントを通じて共同作業を行うことができます。 データ探索の結果はすぐに表示され、同じノートブック内でデータを視覚化することができます。

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>.NET for Apache Spark ノートブックを使用する方法

新しいノートブックを作成する場合は、使用するビジネス ロジックを表現する言語カーネルを選択します。 C# など、いくつかの言語のカーネル サポートがあります。

ご利用の Azure Synapse Analytics ノートブック内で .NET for Apache Spark を使用するには、カーネルとして **.NET Spark (C#)** を選択し、ノートブックを既存の Spark プールにアタッチします。

.Net Spark ノートブックは .NET インタラクティブ エクスペリエンスに基づいており、Spark セッション変数 `spark` があらかじめ定義された状態で、すぐに利用できる .NET for Spark を使用する機能を備えたインタラクティブ C# エクスペリエンスを備えています。

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET for Apache Spark C# カーネル機能

Azure Synapse Analytics ノートブック内で .NET for Apache Spark を使用する場合は、次の機能を使用できます。

* 宣言型 HTML: ヘッダー、箇条書きリスト、画像表示など、HTML 構文を使用してセルから出力を生成します。
* シンプルな C# ステートメント (割り当て、コンソールへの出力、例外のスローなど)。
* 複数行の C# コード ブロック (if ステートメント、foreach ループ、クラス定義など)。
* 標準 C# ライブラリ (システム、LINQ、列挙体など) へのアクセス。
* [C# 8.0 言語機能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)のサポート。
* 'spark'。事前定義された変数であり、使用する Apache Spark セッションへのアクセス権がこれにより与えられます。
* [Apache Spark 内で実行できる .NET ユーザー定義関数](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)を定義するためのサポート。
* Spark ジョブからの出力を、`XPlot.Plotly` ライブラリを使用してさまざまなグラフ (折れ線、横棒、ヒストグラムなど) やレイアウト (シングル、オーバーレイなど) で視覚化するためのサポート。
* ご利用の C# ノートブックに NuGet パッケージを含める機能。

## <a name="next-steps"></a>次のステップ

* [.NET for Apache Spark ドキュメント](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET インタラクティブ](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
