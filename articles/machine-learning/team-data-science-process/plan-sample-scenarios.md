---
title: Azure Machine Learning のシナリオを特定する - Team Data Science Process
description: Team Data Science Process での高度な予測分析の実行に適したシナリオを選択します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93319304"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Azure Machine Learning での高度な分析のシナリオ
この記事では、[Team Data Science Process (TDSP)](overview.md) で処理できる多様なデータ ソースとターゲット シナリオの例について概要を説明します。 TDSP は、チームが共同でインテリジェントなアプリケーションを構築するための体系的手法を提供します。 ここで示すシナリオでは、データ処理ワークフローで使用できるオプションを紹介します。このオプションは、データの特性、ソースの場所、および Azure でのターゲット リポジトリによって異なります。

最後のセクションに、データと目標に適したサンプル シナリオを選択するための **デシジョン ツリー** を示します。

以下の各セクションに、サンプル シナリオを示します。 シナリオごとに、考えられるデータ サイエンスまたは高度な分析のフローと、サポートする Azure リソースの説明があります。

> [!NOTE]
> **次のすべてのシナリオについて、次を実行する必要があります。**
> <br/>
> 
> * [ストレージ アカウントの作成](../../storage/common/storage-account-create.md)
>   <br/>
> * [Azure Machine Learning ワークスペースの作成](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>シナリオ \#1:ローカル ファイルの小規模から中規模の表形式のデータセット
![小規模から中規模のローカル ファイル][1]

#### <a name="additional-azure-resources-none"></a>その他の Azure リソース:なし
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. データセットをアップロードします。
1. アップロードされたデータセットで始まる Azure Machine Learning の実験フローを構築します。

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>シナリオ \#2:処理が必要なローカル ファイルの小規模から中規模のデータセット
![処理中の小規模から中規模のローカル ファイル][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>その他の Azure リソース:Azure 仮想マシン (IPython Notebook サーバー)
1. IPython Notebook を実行する Azure 仮想マシンを作成します。
1. Azure Storage コンテナーにデータをアップロードします。
1. IPython Notebook でデータの前処理とクリーニングを行い、Azure Storage コンテナーからデータにアクセスします。
1. クリーニングされたデータを表形式に変換します。
1. 変換されたデータを Azure BLOB に保存します。
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. [データのインポート][import-data] モジュールを使用して、Azure BLOB からデータを読み取ります。
1. 統合されたデータセットで始まる Azure Machine Learning の実験フローを構築します。

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>シナリオ \#3:ローカル ファイルの大規模データセット (Azure Blob を対象)
![大規模なローカル ファイル][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>その他の Azure リソース:Azure 仮想マシン (IPython Notebook サーバー)
1. IPython Notebook を実行する Azure 仮想マシンを作成します。
1. Azure Storage コンテナーにデータをアップロードします。
1. IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。
1. 必要に応じて、クリーニングされたデータを表形式に変換します。
1. データを探索し、必要に応じて機能を作成します。
1. 小規模から中規模のデータ サンプルを抽出します。
1. サンプリングされたデータを Azure BLOB に保存します。
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. [データのインポート][import-data] モジュールを使用して、Azure BLOB からデータを読み取ります。
1. データセットの取り込みから始まる Azure Machine Learning の実験フローを構築します。

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>シナリオ \#4:ローカル ファイルの小規模から中規模のデータセット (Azure 仮想マシンの SQL Server を対象)
![Azure の SQL DB への小規模から中規模のローカル ファイル][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>その他の Azure リソース:Azure 仮想マシン (SQL Server/IPython Notebook サーバー)
1. SQL Server と IPython Notebook を実行する Azure 仮想マシンを作成します。
1. Azure Storage コンテナーにデータをアップロードします。
1. IPython Notebook を使用して、Azure Storage コンテナーでデータの前処理とクリーニングを行います。
1. 必要に応じて、クリーニングされたデータを表形式に変換します。
1. データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。
1. Azure VM で実行している SQL Server データベースにデータを読み込みます。
   
   オプション \#1:SQL Server Management Studio を使用する。
   
   * SQL Server VM にログインします。
   * SQL Server Management Studio を実行します。
   * データベースとターゲット テーブルを作成します。
   * いずれかの一括インポート方法を使用して、ローカルの VM ファイルからデータを読み込みます。
   
   オプション \#2:IPython Notebook を使用する (中規模以上のデータセットには不向き)
   
   <!-- -->    
   * ODBC 接続文字列を使用して、VM 上の SQL Server にアクセスします。
   * データベースとターゲット テーブルを作成します。
   * いずれかの一括インポート方法を使用して、ローカルの VM ファイルからデータを読み込みます。
1. データを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。
1. 必要に応じて、データのサンプル サイズを決定します。
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. [データのインポート][import-data] モジュールを使用して、SQL Server から直接データを読み取ります。 フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[データのインポート][import-data] クエリに貼り付けます。
1. データセットの取り込みから始まる Azure Machine Learning の実験フローを構築します。

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>シナリオ \#5:ローカル ファイルの大規模データセット (Azure VM の SQL Server を対象)
![Azure の SQL DB への大規模なローカル ファイル][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>その他の Azure リソース:Azure 仮想マシン (SQL Server/IPython Notebook サーバー)
1. SQL Server と IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。
1. Azure Storage コンテナーにデータをアップロードします。
1. (省略可能) データの前処理とクリーニングを行います。
   
    a.  IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。
   
    b.  必要に応じて、クリーニングされたデータを表形式に変換します。
   
    c.  データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。
1. Azure VM で実行している SQL Server データベースにデータを読み込みます。
   
    a.  SQL Server VM にログインします。
   
    b.  データをまだ保存していない場合は、データ ファイルを Azure Storage コンテナーからローカルの VM フォルダーにダウンロードします。
   
    c.  SQL Server Management Studio を実行します。
   
    d.  データベースとターゲット テーブルを作成します。
   
    e.  いずれかの一括インポート方法を使用してデータを読み込みます。
   
    f.  テーブルの結合が必要な場合は、インデックスを作成して処理時間を短縮します。
   
   > [!NOTE]
   > 大きなサイズのデータの読み込みを高速化するために、パーティション分割されたテーブルを作成し、並列処理でデータを一括インポートすることをお勧めします。 詳細については、「 [Parallel Data Import to SQL Partitioned Tables (SQL パーティション テーブルへのデータの並列インポート](parallel-load-sql-partitioned-tables.md)」をご覧ください。
   > 
   > 
1. データを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。
1. 必要に応じて、データのサンプル サイズを決定します。
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. [データのインポート][import-data] モジュールを使用して、SQL Server から直接データを読み取ります。 フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[データのインポート][import-data] クエリに貼り付けます。
1. データセットのアップロードから始まる Azure Machine Learning の単純な実験フローを構築します。

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>シナリオ \#6:オンプレミスの SQL Server データベースの大規模データセット (Azure 仮想マシンの SQL Server を対象)
![Azure の SQL DB へのオンプレミスの大規模な SQL DB][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>その他の Azure リソース:Azure 仮想マシン (SQL Server/IPython Notebook サーバー)
1. SQL Server と IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。
1. いずれかのデータ エクスポート方法を使用して、SQL Server からダンプ ファイルにデータをエクスポートします。
   
   > [!NOTE]
   > オンプレミスのデータベースからすべてのデータを移動する場合、より速い方法は、完全なデータベースを Azure の SQL Server インスタンスに移動することです。 データのエクスポート、データベースの作成、ターゲット データベースへのデータの読み込み/インポートの手順を省略し、別の方法を続行します。
   > 
   > 
1. ダンプ ファイルを Azure Storage コンテナーにアップロードします。
1. Azure 仮想マシンで実行している SQL Server データベースにデータを読み込みます。
   
   a.  SQL Server VM にログインします。
   
   b.  データ ファイルを Azure Storage コンテナーからローカルの VM フォルダーにダウンロードします。
   
   c.  SQL Server Management Studio を実行します。
   
   d.  データベースとターゲット テーブルを作成します。
   
   e.  いずれかの一括インポート方法を使用してデータを読み込みます。
   
   f.  テーブルの結合が必要な場合は、インデックスを作成して処理時間を短縮します。
   
   > [!NOTE]
   > 大きなサイズのデータの読み込みを高速にするため、パーティション分割されたテーブルを作成し、並列処理でデータを一括インポートします。 詳細については、「 [Parallel Data Import to SQL Partitioned Tables (SQL パーティション テーブルへのデータの並列インポート](parallel-load-sql-partitioned-tables.md)」をご覧ください。
   > 
   > 
1. データを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。
1. 必要に応じて、データのサンプル サイズを決定します。
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. [データのインポート][import-data] モジュールを使用して、SQL Server から直接データを読み取ります。 フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[データのインポート][import-data] クエリに貼り付けます。
1. データセットのアップロードから始まる Azure Machine Learning の単純な実験フローを構築します。

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>オンプレミス SQL Server から Azure SQL Database に完全なデータベースをコピーする別の方法
![ローカル DB をデタッチし、Azure の SQL DB にアタッチする][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>その他の Azure リソース:Azure 仮想マシン (SQL Server/IPython Notebook サーバー)
SQL Server VM で SQL Server データベース全体をレプリケートするには、データベースを 1 つの場所/サーバーから別の場所にコピーする必要があり、データベースが一時的にオフラインになることを想定しています。 SQL Server Management Studio のオブジェクト エクスプローラーか、同等の Transact-SQL コマンドを使用できます。

1. ソースの場所にあるデータベースをデタッチします。 詳細については、「[データベースのデタッチ](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)」をご覧ください。
1. Windows エクスプローラーや Windows コマンド プロンプト ウィンドウで、デタッチされたデータベース ファイルとログ ファイルを Azure の SQL Server VM 上のターゲットの場所にコピーします。
1. コピーしたファイルを対象の SQL Server インスタンスにアタッチします。 詳細については、「 [Attach a Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)」を参照してください。

[デタッチとアタッチを使用してデータベースを移動する方法 (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>シナリオ \#7:ローカル ファイルのビッグ データ (Azure HDInsight Hadoop クラスターの Hive データベースを対象)
![ローカル ターゲット Hive のビッグ データ][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>その他の Azure リソース:Azure HDInsight Hadoop クラスターと Azure 仮想マシン (IPython Notebook サーバー)
1. IPython Notebook サーバーを実行する Azure 仮想マシンを作成します。
1. Azure HDInsight Hadoop クラスターを作成します。
1. (省略可能) データの前処理とクリーニングを行います。
   
   a.  IPython Notebook でデータの前処理とクリーニングを行い、Azure BLOB からデータにアクセスします。
   
   b.  必要に応じて、クリーニングされたデータを表形式に変換します。
   
   c.  データをローカルの VM ファイルに保存します (IPython Notebook は VM で実行し、ローカル ドライブは VM ドライブを参照します)。
1. 手順 2 で選択されている Hadoop クラスターの既定のコンテナーにデータをアップロードします。
1. Azure HDInsight Hadoop クラスターの Hive データベースにデータを読み込みます。
   
   a.  Hadoop クラスターのヘッド ノードにログインします。
   
   b.  Hadoop コマンド ラインを開きます。
   
   c.  Hadoop コマンド ラインで、コマンド `cd %hive_home%\bin` を使用して Hive ルート ディレクトリを入力します。
   
   d.  Hive クエリを実行してデータベースとテーブルを作成し、BLOB ストレージから Hive テーブルにデータを読み込みます。
   
   > [!NOTE]
   > データが大きい場合、ユーザーはパーティションを使用して Hive テーブルを作成できます。 次に、ヘッド ノードの Hadoop コマンド ラインで `for` ループを使用し、パーティション分割された Hive テーブルにデータを読み込むことができます。
   > 
   > 
1. Hadoop コマンド ラインでデータを探索し、必要に応じて機能を作成します。 機能をデータベース テーブルで具体化する必要はありません。 作成に必要なクエリに注意してください。
   
   a.  Hadoop クラスターのヘッド ノードにログインします。
   
   b.  Hadoop コマンド ラインを開きます。
   
   c.  Hadoop コマンド ラインで、コマンド `cd %hive_home%\bin` を使用して Hive ルート ディレクトリを入力します。
   
   d.  Hadoop クラスターのヘッド ノードの Hadoop コマンド ラインで Hive クエリを実行してデータを探索し、必要に応じて機能を作成します。
1. 必要に応じて、Azure Machine Learning Studio に適したデータをサンプリングします。
1. [Azure Machine Learning Studio](https://studio.azureml.net/)にサインインします。
1. [データのインポート][import-data] モジュールを使用して、`Hive Queries` から直接データを読み取ります。 フィールドの抽出、機能の作成、データのサンプリングを行うのに必要なクエリを、必要に応じて、直接[データのインポート][import-data] クエリに貼り付けます。
1. データセットのアップロードから始まる Azure Machine Learning の単純な実験フローを構築します。

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>シナリオを選択するためのデシジョン ツリー
---
次の図は、上記で説明したシナリオと、各シナリオを選択するための Advanced Analytics Process and Technology の選択肢をまとめたものです。 データ処理、探索、特徴エンジニアリング、およびサンプリングは、1 つ以上のメソッド/環境 (ソース、中間、またはターゲット環境) で発生する場合があり、必要に応じて繰り返し実行される可能性があります。 図は、考えられるフローの一部のみを示しており、すべてを網羅しているわけではありません。

![サンプル DS プロセスのチュートリアル シナリオ][8]

### <a name="advanced-analytics-in-action-examples"></a>高度な分析の活用の例
Advanced Analytics Process and Technology とパブリック データセットを使用する Azure Machine Learning の完全な チュートリアルについては、以下を参照してください。

* [Team Data Science Process の活用: SQL Sever の使用](sql-walkthrough.md)。
* [Team Data Science Process の活用: HDInsight Hadoop クラスターの使用](hive-walkthrough.md)。

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data