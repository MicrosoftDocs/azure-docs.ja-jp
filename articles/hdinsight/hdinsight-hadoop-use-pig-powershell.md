---
title: "HDInsight での Hadoop Pig と PowerShell の使用 | Microsoft Docs"
description: "Azure PowerShell を使用して HDInsight で Pig ジョブを Hadoop クラスターに送信する方法を説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 737089c1-b494-4387-9def-7b4dac3be532
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: b4f2f1195887d8b64599e82334ab36b21eef1d1d
ms.lasthandoff: 03/22/2017

---
# <a name="use-azure-powershell-to-run-pig-jobs-with-hdinsight"></a>Azure PowerShell を使用して HDInsight で Pig ジョブを使用する

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスターで Pig ジョブを Hadoop に送信する方法を説明します。 Pig では map 関数や reduce 関数ではなく、データ変換をモデル化する言語 (Pig Latin) を使用して MapReduce ジョブを記述できます。

> [!NOTE]
> このドキュメントには、例で使用される Pig Latin ステートメントで何が実行されるかに関する詳細は含まれていません。 この例で使用される Pig Latin については「 [HDInsight での Pig Latin と Hadoop の使用](hdinsight-use-pig.md)」をご覧ください。

## <a id="prereq"></a>前提条件

* **Azure HDInsight クラスター**

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **Azure PowerShell を実行できるワークステーション**。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a id="powershell"></a>PowerShell を使用した Pig ジョブの実行

Azure PowerShell では、HDInsight で Pig ジョブをリモートで実行できる *コマンドレット* が提供されます。 PowerShell は、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出しを内部的に使用します。

リモート HDInsight クラスターで Pig ジョブを実行するときに次のコマンドレットを使用します。

* **Login-AzureRmAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。
* **New-AzureRmHDInsightPigJobDefinition**: 指定された Pig Latin ステートメントを使用して、*ジョブ定義* を作成します。
* **Start-AzureRmHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる *ジョブ* オブジェクトを返します。
* **Wait-AzureRmHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。 ジョブの完了を待機するか、待機時間が上限に達します。
* **Get-AzureRmHDInsightJobOutput**: ジョブの出力を取得する場合に使用します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **pigjob.ps1**として保存します。

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. 新しい Windows PowerShell コマンド プロンプトを開きます。 ディレクトリを **pigjob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。
   
        .\pigjob.ps1
   
    Azure サブスクリプションへのログインを求められます。 次に、HDInsight クラスターの HTTPS/管理者アカウント名とパスワードの入力が求められます。

2. ジョブが完了すると、次のテキストのような情報が返されます。
   
        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。 このジョブに関するエラー情報を表示するには、次のコマンドを **pigjob.ps1** ファイルの末尾に追加して保存し、再実行します。

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

## <a id="summary"></a>概要
このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Pig ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ
HDInsight での Pig に関する全般的な情報:

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)


