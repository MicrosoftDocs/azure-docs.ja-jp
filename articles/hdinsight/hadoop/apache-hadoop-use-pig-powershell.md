---
title: PowerShell を使用して HDInsight 上で Apache Pig を使用する - Azure
description: Azure PowerShell を使用して HDInsight 上の Apache Hadoop クラスターに Apache Pig ジョブを送信する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: bb00f6ccd22be75a235d9cd6fc174741207a76e0
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359162"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Azure PowerShell を使用して HDInsight 上で Apache Pig ジョブを実行する

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスター上の Apache Hadoop に Apache Pig ジョブを送信する方法について説明します。 Pig では map 関数や reduce 関数ではなく、データ変換をモデル化する言語 (Pig Latin) を使用して MapReduce ジョブを記述できます。

> [!NOTE]  
> このドキュメントには、例で使用される Pig Latin ステートメントで何が実行されるかに関する詳細は含まれていません。 この例で使用される Pig Latin については「[HDInsight 上の Apache Pig で Apache Pig を使用する](hdinsight-use-pig.md)」をご覧ください。

## <a id="prereq"></a>前提条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* **Azure HDInsight クラスター**

  > [!IMPORTANT]  
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* **Azure PowerShell を実行できるワークステーション**。

## <a id="powershell"></a>Apache Pig ジョブを実行する

Azure PowerShell では、HDInsight で Pig ジョブをリモートで実行できる *コマンドレット* が提供されます。 PowerShell は、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出しを内部的に使用します。

リモート HDInsight クラスターで Pig ジョブを実行するときに次のコマンドレットを使用します。

* **Connect-AzAccount**:Azure サブスクリプションに対して Azure PowerShell を認証します。
* **New-AzHDInsightPigJobDefinition**:指定された Pig Latin ステートメントを使用して、*ジョブ定義*を作成します。
* **Start-AzHDInsightJob**:ジョブ定義を HDInsight に送信し、ジョブを開始します。 "*ジョブ*" オブジェクトが返されます。
* **Wait-AzHDInsightJob**:ジョブ オブジェクトを使用して、ジョブの状態を確認します。 ジョブの完了を待機するか、待機時間が上限に達します。
* **Get-AzHDInsightJobOutput**:ジョブの出力を取得するために使用します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **pigjob.ps1**として保存します。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

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

ジョブが完了しても情報が返されない場合は、エラー ログを調べてください。 このジョブに関するエラー情報を表示するには、次のコマンドを **pigjob.ps1** ファイルの末尾に追加して保存し、再実行します。

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

このコマンドレットは、ジョブ処理中に STDERR に書き込まれた情報を返します。

## <a id="summary"></a>概要
このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Pig ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ
HDInsight での Pig に関する全般的な情報:

* [HDInsight 上の Apache Hadoop で Apache Pig を使用する](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight 上の Apache Hadoop で Apache Hive を使用する](hdinsight-use-hive.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)
