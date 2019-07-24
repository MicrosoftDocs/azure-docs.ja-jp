---
title: データ サイエンス仮想マシン用データ プラットフォーム - Azure | Microsoft Docs
description: Data Science Virtual Machine でサポートされているデータ プラットフォームおよびツールについて説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 27e0deae9c35ad8fa00659e3e3e505cace6e9014
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733435"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine でサポートされているデータ プラットフォーム

データ サイエンス仮想マシン (DSVM) では、さまざまなデータ プラットフォームに対して分析を構築することができます。 リモート データ プラットフォームへのインターフェイスに加えて、DSVM は、迅速な開発およびプロトタイプ作成のためのローカル インスタンスを提供します。 

DSVM でサポートされているデータ プラットフォーム ツールを次に示します。 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer エディション

| | |
| ------------- | ------------- |
| 紹介   | ローカルのリレーショナル データベース インスタンス      |
| サポートされている DSVM エディション      |  Windows      |
| 一般的な用途      | 比較的小さなデータセットを使用したローカルでの迅速開発 <br/> In-Database R の実行   |
| サンプルへのリンク      |    New York City Dataset の小さなサンプルが、SQL データベース `nyctaxi` に読み込まれます。 <br/> Microsoft R および In-Database の分析を示す Jupyter サンプルは次の場所にあります。<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM 上の関連ツール       | SQL Server Management Studio <br/> ODBC/JDBC ドライバー<br/> pyodbc、RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 developer edition は、開発およびテスト目的でのみ使用できます。 実稼働環境で実行するには、ライセンスまたはいずれかの SQL Server VM が必要です。 


### <a name="setup"></a>セットアップ

データベース サーバーは既に事前構成されており、SQL Server に関連する Windows サービス (`SQL Server (MSSQLSERVER)` など) は自動的に実行されるように設定されています。 手動で実行する唯一の手順は、Microsoft R を使用した In-Database 分析を有効にすることです。これを行うには、コンピューターの管理者としてログインした後、SQL Server Management Studio (SSMS) での一時アクションとして次のコマンドを実行し、SSMS で [New Query] \(新しいクエリ) を開き、選択されているデータベースが `master` であることを確認してから、次を実行します。 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
SQL Server Management Studio を実行するには、プログラムの一覧から "SQL Server Management Studio" を探すか、または Windows 検索を使用してこのプログラムを探して実行します。 資格情報の入力を求められたら、[Windows Authentication] (Windows 認証) を選択し、[SQL Server Name] (SQL Server 名) にはコンピューター名または ```localhost``` を使用します。 

### <a name="how-to-use--run-it"></a>使用/実行方法  

既定のデータベース インスタンスがあるデータベース サーバーは、既定で自動的に実行されています。 VM 上の SQL Server Management Studio などのツールを使用して、SQL Server データベースにローカルでアクセスできます。 ローカル管理者アカウントには、データベースへの管理者アクセス権があります。 

また DSVM には、Python、R を含む複数の言語で記述されたアプリケーションから SQL Server、Azure SQL データベース、および Azure SQL Data Warehouse と通信するための ODBC ドライバーおよび JDBC ドライバーが付属します。 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>DSVM での構成/インストール方法 

SQL Server は、標準の方法でインストールされます。 その場所は `C:\Program Files\Microsoft SQL Server` です。 In-Database R インスタンスの場所は `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` です。 DSVM には、`C:\Program Files\Microsoft\R Server\R_SERVER` にインストールされる別のスタンドアロン Microsoft R Server インスタンスもあります。 これら 2 つの R インスタンスは、ライブラリを共有しません。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (スタンドアロン)

| | |
| ------------- | ------------- |
| 紹介   | 広く普及した Apache Spark プラットフォームのスタンドアロン (シングル ノード インプロセス) インスタンス、高速で大規模なデータ処理および機械学習のためのシステム     |
| サポートされている DSVM エディション      | Linux <br /> Windows (試験段階)      |
| 一般的な用途      | * 小さなデータセットを使用してローカルで Spark/PySpark アプリケーションを迅速開発した後、Azure HDInsight などの大規模 Spark クラスターにそのアプリケーションをデプロイする<br/> * Microsoft R Server Spark コンテキストをテストする <br />* SparkML またはマイクロソフトのオープン ソース [MMLSpark](https://github.com/Azure/mmlspark) ライブラリを使用して ML アプリケーションを構築する  |
| サンプルへのリンク      |    Jupyter サンプル: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (Spark コンテキスト): /dsvm/samples/MRS/MRSSparkContextSample.R |
| DSVM 上の関連ツール       | PySpark、Scala<br/>Jupyter (Spark/PySpark カーネル)<br/>Microsoft R Server、SparkR、Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>使用方法
コマンド ラインから `spark-submit` または `pyspark` コマンドで Spark ジョブを送信することにより、Spark を実行できます。 Spark カーネルで新しいノートブックを作成することによって、Jupyter ノートブックを作成することもできます。 

DSVM で使用可能な SparkR、Sparklyr、Microsoft R Server などのライブラリを使用して、R から Spark を使用できます。 前出の表のサンプルへのリンクを参照してください。 

> [!NOTE]
> DSVM の Spark コンテキストでの Microsoft R Server の実行は、Ubuntu Linux DSVM エディションでのみサポートされています。 



### <a name="setup"></a>セットアップ
Ubuntu Linux DSVM エディション上の Microsoft R Server で Spark コンテキストで実行する前に、1 回限りのセットアップ手順を実行して、単一ノードのローカル Hadoop (HDFS と Yarn) インスタンスを有効にする必要があります。 Hadoop サービスはインストールされていますが、既定では DSVM で無効になっています。 これを有効にするには、最初に次のコマンドを root 権限で実行する必要があります。

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

Hadoop 関連サービスが不要な場合は、```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` を実行してサービスを停止することができます。MRS をリモート Spark コンテキスト (つまり DSVM 上のスタンドアロン Spark インスタンス) で開発およびテストする方法を示したサンプルは、`/dsvm/samples/MRS` ディレクトリで入手して使用することができます。 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>DSVM での構成/インストール方法 
|プラットフォーム|インストール場所 ($SPARK_HOME)|
|:--------|:--------|
| Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Azure BLOB または Azure Data Lake Storage (ADLS) から、またマイクロソフトの MMLSpark 機械学習ライブラリを使用してデータにアクセスするためのライブラリは、$SPARK_HOME/jars にプレインストールされています。 これらの JAR は Spark の起動時に自動的に読み込まれます。 既定では、Spark はローカル ディスク上のデータを使用します。 DSVM 上の Spark インスタンスから Azure BLOB または ADLS に格納されたデータにアクセスするためには、$SPARK_HOME/conf/core-site.xml.template (BLOB および ADLS 構成のためのプレースホルダーがある場所) にあるテンプレートをベースに `core-site.xml` ファイルを作成/構成し、Azure Blob および Azure Data Lake Storage への適切な資格情報を使用する必要があります。 ADLS サービス資格情報の作成手順の詳細については、[こちら](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)を参照してください。 Azure BLOB または ADLS の資格情報が core-site.xml ファイルに入力されたら、URI プレフィックス wasb:// または adl:// を使用して、それらのソースに格納されたデータを参照することができます。 

