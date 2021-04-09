---
title: Azure HDInsight (Apache Hadoop) での Apache Sqoop ジョブの実行
description: ワークステーションから Azure PowerShell を使用して、Hadoop クラスターと Azure SQL データベース間で Sqoop インポートとエクスポートを実行する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/06/2019
ms.openlocfilehash: 1c34b673cd970a9e7577b7ff01d27eb0e4cc1ac1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946427"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>HDInsight の Hadoop での Apache Sqoop の使用

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

HDInsight 内で Apache Sqoop を使用して、HDInsight クラスターと Azure SQL Database 間でデータのインポートとエクスポートを行う方法について学習します。

Apache Hadoop はログやファイルなどの非構造化データおよび半構造化データを処理する場合に自然な選択ですが、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) は、Hadoop クラスターとリレーショナル データベース間でデータを転送するためのツールです。 このツールを使用して、SQL、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Apache Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。 この記事では、リレーショナル データベースとして Azure SQL Database を使用します。

> [!IMPORTANT]  
> この記事では、データ転送を実行するテスト環境を設定します。 次に、後述の「[Sqoop ジョブの実行](#run-sqoop-jobs)」セクションにあるいずれかの方法から、この環境のデータ転送方法を選択します。

HDInsight クラスターでサポートされている Sqoop のバージョンについては、「[HDInsight で提供されるクラスター バージョンの新機能](../hdinsight-component-versioning.md)」をご覧ください。

## <a name="understand-the-scenario"></a>シナリオの理解

HDInsight クラスターにはサンプル データがいくつか付属しています。 ここでは、以下の 2 つのサンプルを使用します。

* `/example/data/sample.log` にある Apache Log4j ログ ファイル。 次のようなログがファイルから抽出されます。

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* `hivesampletable` という名前の Hive テーブル。これは `/hive/warehouse/hivesampletable` にあるデータ ファイルを参照します。 テーブルはモバイル デバイスのデータを含んでいます。
  
  | フィールド | データ型 |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | country |string |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

この記事では、これら 2 つのデータセットを使用して Sqoop のインポートとエクスポートをテストします。

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>テスト環境のセットアップ

クラスター、SQL データベース、およびその他のオブジェクトは、Azure Resource Manager テンプレートを使用して Azure portal から作成されます。 テンプレートは [Azure クイック スタート テンプレート集](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/)にあります。 Resource Manager テンプレートでは、SQL データベースにテーブル スキーマをデプロイするために bacpac パッケージを呼び出します。  bacpac パッケージは、パブリック BLOB コンテナー、 https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac の中にあります。 bacpac ファイルのプライベート コンテナーを使用する場合は、テンプレートで、次の値を使用します。

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> テンプレートまたは Azure Portal を使用したインポートでは、Azure Blob Storage からのインポートのみをサポートします。

1. 次の画像を選択して、Azure portal で Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 次のプロパティを入力します。

    |フィールド |値 |
    |---|---|
    |サブスクリプション |ドロップダウン リストから Azure サブスクリプションを選択します。|
    |Resource group |ドロップダウン リストからリソース グループを選択するか、新しいリソース グループを作成します。|
    |場所 |ドロップダウン リストからリージョンを選択します。|
    |クラスター名 |Hadoop クラスターの名前を入力します。 小文字のみを使用します。|
    |[Cluster Login User Name]\(クラスター ログイン ユーザー名\) |事前に設定されている値 `admin` のままにします。|
    |[クラスター ログイン パスワード] |パスワードを入力します。|
    |SSH ユーザー名 |事前に設定されている値 `sshuser` のままにします。|
    |SSH パスワード |パスワードを入力します。|
    |SQL の管理者ログイン |事前に設定されている値 `sqluser` のままにします。|
    |SQL の管理者パスワード |パスワードを入力します。|
    |_artifacts の場所 | 別の場所で独自の bacpac ファイルを使用する場合を除き、既定値を使用します。|
    |Sas トークンとしての _artifacts の場所 |空白のままにします。|
    |Bacpac ファイル名 |独自の bacpac ファイルを使用する場合を除き、既定値を使用します。|
    |場所 |既定値を使用します。|

    [論理 SQL サーバー](../../azure-sql/database/logical-servers.md)名は `<ClusterName>dbserver` になります。 データベース名は `<ClusterName>db` になります。 既定のストレージ アカウント名は `e6qhezrh2pdqu` になります。

3. **[上記の使用条件に同意する]** を選択します。

4. **[購入]** を選択します。 "Submitting deployment for Template deployment" という新しいタイルが表示されます。 クラスターと SQL Database の作成には約 20 分かかります。

## <a name="run-sqoop-jobs"></a>Sqoop ジョブの実行

HDInsight では、さまざまな方法を使用して Sqoop ジョブを実行できます。 次の表を使用して、適切な方法を判別してから、該当するチュートリアルのリンクをクリックしてください。

| **方法** | **対話型** シェルの有無 | **バッチ** 処理の有無 | 使用元の **クライアントのオペレーティング システム** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux、Unix、Mac OS X、または Windows |
| [.NET SDK for Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (現時点) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>制限事項

* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。
* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入を実行します。

## <a name="next-steps"></a>次のステップ

ここでは Sqoop の使用方法を学習しました。 詳細については、次を参照してください。

* [HDInsight での Apache Hive の使用](./hdinsight-use-hive.md)
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md):HDInsight/Azure Blob Storage にデータをアップロードするその他の方法を説明します。
* [Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と SQL Database の間でデータをインポートおよびエクスポートする](./apache-hadoop-use-sqoop-mac-linux.md)