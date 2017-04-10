---
title: "Hadoop で MapReduce と PowerShell を使用する | Microsoft Docs"
description: "PowerShell を使用して HDInsight の Hadoop で MapReduce ジョブをリモートで実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 9eafaca75bbf3db639a42c8206d2ac506c255273
ms.lasthandoff: 03/22/2017


---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>PowerShell を使用して HDInsight の Hadoop で MapReduce ジョブを実行

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。

## <a id="prereq"></a>前提条件

* **Azure HDInsight (HDInsight で Hadoop を使用) クラスター**

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **Azure PowerShell を実行できるワークステーション**。

## <a id="powershell"></a>Azure PowerShell を使用した MapReduce ジョブの実行

Azure PowerShell では、HDInsight で MapReduce ジョブをリモートで実行できる *コマンドレット* が提供されます。 これは、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出し (旧称: Templeton) を内部的に使用することで機能します。

リモート HDInsight クラスターで MapReduce ジョブを実行するときに次のコマンドレットを使用します。

* **Login-AzureRmAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。

* **New-AzureRmHDInsightMapReduceJobDefinition**: 指定された MapReduce 情報を使用して、新しい "*ジョブ定義*" を作成します。

* **Start-AzureRmHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる "*ジョブ*" オブジェクトを返します。

* **Wait-AzureRmHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。 ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureRmHDInsightJobOutput**: ジョブの出力を取得する場合に使用します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **mapreducejob.ps1** として保存します。

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. **Azure PowerShell** コマンド プロンプトを開きます。 ディレクトリを **mapreducejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。
   
        .\mapreducejob.ps1
   
    スクリプトを実行すると、HDInsight クラスターの名前と、クラスターの HTTPS/管理者アカウント名およびパスワードの入力を求められます。 Azure サブスクリプションの認証が求められる場合もあります。

3. ジョブが完了すると、次のような出力が返されます。
    
        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071
    
    この出力は、ジョブが正常に完了したことを示しています。
    
    > [!NOTE]
    > **ExitCode** が 0 以外の値の場合は、 [トラブルシューティング](#troubleshooting)をご覧ください
    
    この例では、スクリプトが実行されるディレクトリにある **output.txt** ファイルにダウンロードしたファイルを格納します。

### <a name="view-output"></a>出力の表示

テキスト エディターで **output.txt** ファイルを開き、ジョブによって生成された文字と文字数を確認します。

> [!NOTE]
> MapReduce ジョブの出力ファイルは変更できません。 そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

## <a id="troubleshooting"></a>トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。 このジョブに関するエラーを表示するには、次のコマンドを **mapreducejob.ps1** ファイルの末尾に追加して保存し、再実行します。

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

このコマンドレットは、ジョブの実行時にサーバー上の STDERR に書き込まれた情報を返します。これは、ジョブ失敗の特定に役立ちます。

## <a id="summary"></a>概要

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)


