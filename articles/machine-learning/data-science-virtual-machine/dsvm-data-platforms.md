---
title: サポートされているデータ プラットフォーム
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine のサポートされているデータ プラットフォームおよびツールについて説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 5dbaf969420f066698a07b8d137d2ba44fc99080
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208123"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine でサポートされているデータ プラットフォーム

Data Science Virtual Machine (DSVM) を使用すると、さまざまなデータ プラットフォームに対して分析を作成することができます。 リモート データ プラットフォームへのインターフェイスに加えて、DSVM は、迅速な開発およびプロトタイプ作成のためのローカル インスタンスを提供します。

DSVM でサポートされているデータ プラットフォーム ツールは次のとおりです。

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer エディション

| | |
| ------------- | ------------- |
| 紹介   | ローカルのリレーショナル データベース インスタンス      |
| サポートされている DSVM エディション      | Windows      |
| 標準的な使用      | 比較的小さなデータセットを使用したローカルでの迅速開発 <br/> In-Database R の実行   |
| サンプルへのリンク      |    New York City データセットの小さなサンプルが、次の SQL データベースに読み込まれます。<br/>  `nyctaxi` <br/> Microsoft Machine Learning Server およびデータベース内の分析を示す Jupyter サンプルは次の場所にあります。<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM 上の関連ツール       | SQL Server Management Studio <br/> ODBC および JDBC ドライバー<br/> pyodbc、RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 Developer エディションは、開発およびテスト目的でのみ使用できます。 実稼働環境で実行するには、ライセンスまたはいずれかの SQL Server VM が必要です。


### <a name="setup"></a>セットアップ

データベース サーバーは既に事前構成されており、SQL Server に関連する Windows サービス (`SQL Server (MSSQLSERVER)` など) は自動的に実行されるように設定されています。 手動で行う唯一の手順は、Microsoft Machine Learning Server を使用してデータベース内分析を有効にすることです。 これを行うには、SQL Server Management Studio (SSMS) で 1 回限りの操作として次のコマンドを実行します。 マシン管理者としてログインし、SSMS で新しいクエリを開き、選択したデータベースが `master` であることを確認した後、このコマンドを実行します。

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
SQL Server Management Studio を実行するには、プログラムの一覧から "SQL Server Management Studio" を探すか、または Windows Search を使用してこれを探して実行します。 資格情報の入力を求められたら、 **[Windows 認証]** を選択し、 **[SQL Server 名]** フィールドにはマシン名または ```localhost``` を使用します。

### <a name="how-to-use-and-run-it"></a>使用と実行方法

既定では、既定のデータベース インスタンスがあるデータベース サーバーは自動的に実行されます。 VM 上の SQL Server Management Studio などのツールを使用して、SQL Server データベースにローカルでアクセスできます。 ローカル管理者アカウントには、データベースへの管理者アクセス権があります。

また DSVM には、Python、Machine Learning Server を含む複数の言語で記述されたアプリケーションから SQL Server、Azure SQL データベース、および Azure SQL Data Warehouse と通信するための ODBC ドライバーおよび JDBC ドライバーが付属しています。

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM での構成とインストール方法 

 SQL Server は、標準の方法でインストールされます。 その場所は `C:\Program Files\Microsoft SQL Server` です。 データベース内 Machine Learning Server インスタンスは、`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` にあります。 DSVM には、`C:\Program Files\Microsoft\R Server\R_SERVER` にインストールされる別のスタンドアロン Machine Learning Server インスタンスもあります。 これらの 2 つの Machine Learning Server インスタンスは、ライブラリを共有しません。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (スタンドアロン)

| | |
| ------------- | ------------- |
| 紹介   | 広く普及した Apache Spark プラットフォームのスタンドアロン (シングル ノード インプロセス) インスタンス、高速で大規模なデータ処理および機械学習のためのシステム     |
| サポートされている DSVM エディション      | Linux <br /> Windows (試験段階)      |
| 標準的な使用      | * 小さなデータセットを使用してローカルで Spark/PySpark アプリケーションを迅速に開発し、後で Azure HDInsight などの大規模 Spark クラスターにデプロイする<br/> * Microsoft Machine Learning Server Spark コンテキストをテストする <br />* SparkML または Microsoft のオープン ソース [MMLSpark](https://github.com/Azure/mmlspark) ライブラリを使用して ML アプリケーションを構築する |
| サンプルへのリンク      |    Jupyter サンプル: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (Spark コンテキスト): /dsvm/samples/MRS/MRSSparkContextSample.R |
| DSVM 上の関連ツール       | PySpark、Scala<br/>Jupyter (Spark/PySpark カーネル)<br/>Microsoft Machine Learning Server、SparkR、Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>使用方法
コマンド ラインで Spark ジョブを送信するには、 `spark-submit` または `pyspark` コマンドを実行します。 Spark カーネルで新しいノートブックを作成することによって、Jupyter ノートブックを作成することもできます。

DSVM で使用可能な SparkR、Sparklyr、Microsoft Machine Learning Server などのライブラリを使用して、R から Spark を使用できます。 前出の表のサンプルへのリンクを参照してください。

> [!NOTE]
> DSVM の Spark コンテキストでの Microsoft Machine Learning Server の実行は、Ubuntu Linux DSVM エディションのみでサポートされています。



### <a name="setup"></a>セットアップ
Ubuntu Linux DSVM エディション上の Microsoft Machine Learning Server で Spark コンテキストで実行する前に、1 回限りのセットアップ手順を実行して、単一ノードのローカル Hadoop (HDFS と Yarn) インスタンスを有効にする必要があります。 Hadoop サービスはインストールされていますが、既定では DSVM で無効になっています。 これらを有効にするには、最初に次のコマンドを root 権限で実行します。

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Hadoop 関連サービスが不要になった場合は、```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` を実行してそれらを停止することができます。

MRS をリモート Spark コンテキスト (つまり DSVM 上のスタンドアロン Spark インスタンス) で開発およびテストする方法を示したサンプルは、`/dsvm/samples/MRS` ディレクトリで入手して使用することができます。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM での構成とインストール方法 
|プラットフォーム|インストール場所 ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Azure Blob Storage または Azure Data Lake Storage から、Microsoft MMLSpark 機械学習ライブラリを使用してデータにアクセスするためのライブラリは、$SPARK_HOME/jars にプレインストールされています。 これらの JAR は Spark の起動時に自動的に読み込まれます。 既定では、Spark はローカル ディスク上のデータを使用します。 

DSVM 上の Spark インスタンスから Blob ストレージまたは Azure Data Lake Storage に格納されているデータにアクセスするには、$SPARK_HOME/conf/core-site.xml.template にあるテンプレートに基づいて `core-site.xml` ファイルを作成して構成する必要があります。 Blob ストレージと Azure Data Lake Storage にアクセスするための適切な資格情報も必要です。 (テンプレート ファイルでは、Blob ストレージおよび Azure Data Lake Storage の構成にプレースホルダーが使用されることに注意してください。)

Azure Data Lake Storage サービス資格情報の作成方法の詳細情報については、[Azure Data Lake Storage Gen1 に対する認証](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)に関する記事を参照してください。 Blob ストレージまたは Azure Data Lake Storage の資格情報が core-site.xml ファイルに入力されると、URI プレフィックス wasb:// または adl:// を使用して、それらのソースに格納されたデータを参照できます。

