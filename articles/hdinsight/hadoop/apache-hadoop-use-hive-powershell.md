---
title: PowerShell を使用して HDInsight 上で Apache Hive を使用する - Azure
description: PowerShell を使用して、HDInsight 上の Apache Hadoop で Hive クエリを実行します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 243713d7961c911cdda93d3d680a952d424da22b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078360"
---
# <a name="run-apache-hive-queries-using-powershell"></a>PowerShell を使用して Apache Hive クエリを実行する
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、Azure PowerShell を Azure リソース グループ モードで使用して、HDInsight クラスター上の Apache Hadoop で Hive クエリを実行する方法の例を示します。

> [!NOTE]  
> このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。 この例で使用される HiveQL については、[HDInsight での Apache Hive と Apache Hadoop の使用](hdinsight-use-hive.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* バージョン 3.4 以上の HDInsight クラスター上の Linux ベースの Apache Hadoop。

* Azure PowerShell を搭載するクライアント。

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Hive クエリを実行する

Azure PowerShell では、HDInsight で Hive クエリをリモートに実行できる *コマンドレット* が提供されます。 内部的には、コマンドレットは HDInsight クラスター上の [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出しを行います。

リモート HDInsight クラスターで Hive クエリを実行するときに次のコマンドレットを使用します。

* `Connect-AzAccount`:Azure サブスクリプションに対して Azure PowerShell を認証します。
* `New-AzHDInsightHiveJobDefinition`:指定された HiveQL ステートメントを使用して、"*ジョブ定義*" を作成します。
* `Start-AzHDInsightJob`:ジョブ定義を HDInsight に送信し、ジョブを開始します。 "*ジョブ*" オブジェクトが返されます。
* `Wait-AzHDInsightJob`:ジョブ オブジェクトを使用して、ジョブの状態を確認します。 ジョブの完了を待機するか、待機時間が上限に達します。
* `Get-AzHDInsightJobOutput`:ジョブの出力を取得する場合に使用します。
* `Invoke-AzHDInsightHiveJob`:HiveQL ステートメントを実行する場合に使用します。 このコマンドレットはクエリの完了をブロックし、その結果を返します。
* `Use-AzHDInsightCluster`:`Invoke-AzHDInsightHiveJob` コマンドに現在のクラスターを使用するように設定します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを `hivejob.ps1` として保存します。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. **Azure PowerShell** コマンド プロンプトを開きます。 ディレクトリを `hivejob.ps1` ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

        .\hivejob.ps1

    スクリプトの実行時に、クラスター名と HTTPS/クラスター管理者アカウントの資格情報を入力するように求められます。 Azure サブスクリプションへのサインインを求められる場合もあります。

3. ジョブが完了すると、次のテキストのような情報が返されます。

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 前述のように、`Invoke-Hive` を使用してクエリを実行し、応答を待機できます。 次のスクリプトを使用して、Invoke-Hive の動作を確認します。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    出力は次のテキストのようになります。

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > より長い HiveQL クエリの場合は、Azure PowerShell の **Here-Strings** コマンドレットや HiveQL スクリプト ファイルを使用できます。 次のスニペットでは、`Invoke-Hive` コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。 HiveQL スクリプト ファイルは、wasb:// にアップロードする必要があります。
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > **Here-Strings** の詳細については、<a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings の使用</a>に関するページをご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング

ジョブが完了しても情報が返されない場合は、エラー ログを調べてください。 このジョブに関するエラーを表示するには、以下を `hivejob.ps1` ファイルの末尾に追加して保存し、再実行します。

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

このコマンドレットは、ジョブ処理中に STDERR に書き込まれた情報を返します。

## <a name="summary"></a>まとめ

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a name="next-steps"></a>次の手順

HDInsight での Hive に関する全般的な情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
