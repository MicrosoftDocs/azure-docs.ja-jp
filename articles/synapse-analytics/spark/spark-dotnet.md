---
title: .NET for Apache Spark を使用する
description: バッチ処理、リアルタイム ストリーミング、機械学習を行うため、および Azure Synapse Analytics ノートブック内でアドホック クエリを作成するために、.NET および Apache Spark の使用について説明します。
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 895d766cc1e70e6a001904770d6f3ef12b6945cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018663"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Azure Synapse Analytics で .NET for Apache Spark を使用する

[.NET for Apache Spark](https://dot.net/spark) では、無料で[オープンソース](https://github.com/dotnet/spark)かつクロスプラットフォームの、Spark 向け .NET サポートを提供しています。 

これには、C# および F# を介して Spark API にアクセスできるようにする Spark 用 .NET バインディングが用意されています。 .NET for Apache Spark では、.NET で記述された Spark 用のユーザー定義関数を作成して実行することもできます。 Spark 用の .NET API を使用すると、Spark SQL、Delta Lake、構造化ストリーミングなど、ご利用のデータの分析に役立つ Spark DataFrames のあらゆる側面にアクセスすることができます。

.NET for Apache Spark を、Spark バッチ ジョブ定義を介して使用するか、またはインタラクティブな Azure Synapse Analytics ノートブックで使用することで、データを分析することができます。 この記事では、両方の手法を使用して、Azure Synapse で .NET for Apache Spark を使用する方法について説明します。

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Spark ジョブ定義を使用してバッチ ジョブを送信する

Azure Synapse Analytics を使用して [Synapse Spark プールの Apache Spark ジョブ定義を作成する](apache-spark-job-definitions.md)方法を学習するチュートリアルにアクセスしてください。 Azure Synapse に送信するアプリをパッケージ化していない場合は、次の手順のようにします。

1. 次のコマンドを実行してアプリを発行します。 *mySparkApp* を必ずそのアプリのパスに置き換えてください。

   **Windows の場合:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **Linux の場合:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. たとえば、ステップ 1 の結果として作成された publish フォルダー `publish.zip` の内容を圧縮します。 すべてのアセンブリは ZIP ファイルの最初のレイヤーにある必要があり、中間フォルダーのレイヤーに存在してはいけません。 つまり、`publish.zip` を解凍すると、すべてのアセンブリが現在の作業ディレクトリに抽出されます。

    **Windows の場合:**

    [7-Zip](https://www.7-zip.org/) や [WinZip](https://www.winzip.com/) のような抽出プログラムを使用して、発行されたすべてのバイナリを含むファイルを bin ディレクトリに抽出します。

    **Linux の場合:**

    Bash シェルを開き、発行されたすべてのバイナリを含む bin ディレクトリに移動し、次のコマンドを実行します。

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics ノートブックでの .NET for Apache Spark 

ノートブックは、.NET for Apache Spark パイプラインおよびシナリオのプロトタイプを作成する場合に便利なオプションです。 ご利用のデータの操作、理解、フィルター処理、表示、視覚化を迅速かつ効率的に開始できます。 

データ エンジニア、データ サイエンティスト、ビジネス アナリスト、機械学習エンジニアは皆、共有されたインタラクティブなドキュメントを通じて共同作業を行うことができます。 データ探索の結果はすぐに表示され、同じノートブック内でデータを視覚化することができます。

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>.NET for Apache Spark ノートブックを使用する方法

新しいノートブックを作成する場合は、使用するビジネス ロジックを表現する言語カーネルを選択します。 C# など、いくつかの言語ではカーネルのサポートを利用できます。

ご利用の Azure Synapse Analytics ノートブック内で .NET for Apache Spark を使用するには、カーネルとして **.NET Spark (C#)** を選択し、ノートブックを既存のサーバーレス Apache Spark プールにアタッチします。

.Net Spark ノートブックは、[.NET インタラクティブ](https://github.com/dotnet/interactive) エクスペリエンスに基づいており、Spark セッション変数 `spark` があらかじめ定義された状態ですぐに利用できる .NET for Spark を使用する機能を備えたインタラクティブ C# エクスペリエンスを提供します。

### <a name="install-nuget-packages-in-notebooks"></a>ノートブックに NuGet パッケージをインストールする

NuGet パッケージの名前の前に `#r nuget` マジック コマンドを使用することで、任意の NuGet パッケージをノートブックにインストールできます。 次の図は例を示しています。

![Spark .NET ノートブック NuGet パッケージをインストールするために #r を使用していることを示すスクリーンショット](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

ノートブックで NuGet パッケージを使用する方法の詳細については、[.NET のインタラクティブ ドキュメント](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md)を参照してください。

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET for Apache Spark C# カーネル機能

Azure Synapse Analytics ノートブック内で .NET for Apache Spark を使用する場合は、次の機能を使用できます。

* 宣言型 HTML: ヘッダー、箇条書きリスト、画像表示など、HTML 構文を使用してセルから出力を生成します。
* シンプルな C# ステートメント (割り当て、コンソールへの出力、例外のスローなど)。
* 複数行の C# コード ブロック (if ステートメント、foreach ループ、クラス定義など)。
* 標準 C# ライブラリ (システム、LINQ、列挙体など) へのアクセス。
* C# 8.0 言語機能のサポート。
* `spark` は、事前定義された変数であり、これにより、使用する Apache Spark セッションへのアクセス権が与えられます。
* [Apache Spark 内で実行できる .NET ユーザー定義関数](/dotnet/spark/how-to-guides/udf-guide)を定義するためのサポート。 .NET for Apache Spark Interactive エクスペリエンスで UDF を使用する方法を確認する場合は、[.NET for Apache Spark Interactive 環境に UDF を書き込んで呼び出す](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)ことをお勧めします。
* Spark ジョブからの出力を、`XPlot.Plotly` ライブラリを使用してさまざまなグラフ (折れ線、横棒、ヒストグラムなど) やレイアウト (シングル、オーバーレイなど) で視覚化するためのサポート。
* ご利用の C# ノートブックに NuGet パッケージを含める機能。

## <a name="next-steps"></a>次のステップ

* [.NET for Apache Spark ドキュメント](/dotnet/spark/)
* [.NET for Apache Spark Interactive ガイド](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET インタラクティブ](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
