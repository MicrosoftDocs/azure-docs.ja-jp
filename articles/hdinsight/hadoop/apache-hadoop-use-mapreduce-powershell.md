---
title: Apache Hadoop で MapReduce と PowerShell を使用する - Azure HDInsight
description: PowerShell を使用して HDInsight 上の Apache Hadoop で MapReduce ジョブをリモートで実行する方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939675"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>PowerShell を使用して HDInsight 上の Apache Hadoop で MapReduce ジョブを実行する

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。

## <a name="prerequisites"></a>前提条件

* HDInsight の Apache Hadoop クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

* インストール済みの PowerShell [Az モジュール](/powershell/azure/)。

## <a name="run-a-mapreduce-job"></a>MapReduce ジョブを実行する

Azure PowerShell では、HDInsight で MapReduce ジョブをリモートで実行できる *コマンドレット* が提供されます。 PowerShell は、HDInsight クラスター上で実行されている [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (旧称: Templeton) への REST 呼び出しを内部的に行います。

リモート HDInsight クラスターで MapReduce ジョブを実行するときに次のコマンドレットを使用します。

|コマンドレット | 説明 |
|---|---|
|Connect-AzAccount|Azure サブスクリプションに対して Azure PowerShell を認証します。|
|New-AzHDInsightMapReduceJobDefinition|指定された MapReduce 情報を使用して、新しい "*ジョブ定義*" を作成します。|
|Start-AzHDInsightJob|ジョブ定義を HDInsight に送信し、ジョブを開始します。 "*ジョブ*" オブジェクトが返されます。|
|Wait-AzHDInsightJob|ジョブ オブジェクトを使用して、ジョブの状態を確認します。 ジョブの完了を待機するか、待機時間が上限に達します。|
|Get-AzHDInsightJobOutput|ジョブの出力を取得するために使用します。|

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **mapreducejob.ps1** として保存します。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. **Azure PowerShell** コマンド プロンプトを開きます。 ディレクトリを **mapreducejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    スクリプトを実行すると、HDInsight クラスター名とクラスター ログインの入力を求められます。 Azure サブスクリプションの認証が求められる場合もあります。

3. ジョブが完了すると、次のような出力が返されます。

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    この出力は、ジョブが正常に完了したことを示しています。

    > [!NOTE]  
    > **ExitCode** が 0 以外の値の場合は、 [トラブルシューティング](#troubleshooting)をご覧ください

    この例では、スクリプトが実行されるディレクトリにある **output.txt** ファイルにダウンロードしたファイルを格納します。

### <a name="view-output"></a>出力の表示

ジョブによって生成された文字と文字数を確認するには、テキスト エディターで **output.txt** ファイルを開きます。

> [!NOTE]  
> MapReduce ジョブの出力ファイルは変更できません。 そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

## <a name="troubleshooting"></a>トラブルシューティング

ジョブが完了しても情報が返されない場合は、ジョブのエラーを調べてください。 このジョブに関するエラーを表示するには、次のコマンドを **mapreducejob.ps1** ファイルの末尾に追加します。 その後、ファイルを保存し、スクリプトを再実行します。

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

このコマンドレットは、ジョブ実行時に STDERR に書き込まれた情報を返します。

## <a name="next-steps"></a>次のステップ

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。 HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)
* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)