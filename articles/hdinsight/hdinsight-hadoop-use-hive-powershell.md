---
title: "HDInsight での Hadoop Hive と PowerShell の使用 | Microsoft Docs"
description: "PowerShell を使用して、HDInsight での Hadoop で Hive クエリを実行します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: cb795b7c-bcd0-497a-a7f0-8ed18ef49195
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
ms.openlocfilehash: 8f3fc05668fb9c89aa1854e592e0cbced11fe7dc
ms.lasthandoff: 03/22/2017


---
# <a name="run-hive-queries-using-powershell"></a>PowerShell を使用して Hive クエリを実行
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、Azure PowerShell を Azure リソース グループ モードで使用して、HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。

> [!NOTE]
> このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。 この例で使用される HiveQL については、「 [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)」をご覧ください。

**前提条件**

* **Azure HDInsight クラスター**: クラスターが Windows ベースか Linux ベースかは関係ありません。

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **Azure PowerShell を実行できるワークステーション**。
  
[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-hive-queries-using-azure-powershell"></a>Azure PowerShell を使用して Hive クエリを実行する

Azure PowerShell では、HDInsight で Hive クエリをリモートに実行できる *コマンドレット* が提供されます。 内部的には、コマンドレットは HDInsight クラスター上の [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出しを行います。

リモート HDInsight クラスターで Hive クエリを実行するときに次のコマンドレットを使用します。

* **Add-AzureRmAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。
* **New-AzureRmHDInsightHiveJobDefinition**: 指定された HiveQL ステートメントを使用して、"*ジョブ定義*" を作成します。
* **Start-AzureRmHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる *ジョブ* オブジェクトを返します。
* **Wait-AzureRmHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。 ジョブの完了を待機するか、待機時間が上限に達します。
* **Get-AzureRmHDInsightJobOutput**: ジョブの出力を取得する場合に使用します。
* **Invoke-AzureRmHDInsightHiveJob**: HiveQL ステートメントを実行する場合に使用します。 このコマンドレットはクエリの完了にブロックし、その結果を返します。
* **Use-AzureRmHDInsightCluster**: **Invoke-AzureRmHDInsightHiveJob** コマンドに現在のクラスターを使用するように設定します

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **hivejob.ps1**として保存します。

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. **Azure PowerShell** コマンド プロンプトを開きます。 ディレクトリを **hivejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。
   
        .\hivejob.ps1
   
    スクリプトの実行時に、クラスター名とクラスター用の HTTPS/管理者アカウントの資格情報を入力するように求められます。 Azure サブスクリプションへのログインが求められる場合もあります。

3. ジョブが完了すると、次のような情報が返されます。
   
        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 前述のように、 **Invoke-Hive** を使用してクエリを実行し、応答を待機できます。 次のスクリプトを使用して、Invoke-Hive の動作を確認します。

    [!code-powershell[main](../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    出力は次のテキストのようになります。

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > より長い HiveQL クエリの場合は、Azure PowerShell の **Here-Strings** コマンドレットや HiveQL スクリプト ファイルを使用できます。 次のスニペットでは、 **Invoke-Hive** コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。 HiveQL スクリプト ファイルは、wasbs:// にアップロードする必要があります。
   > 
   > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   > 
   > **Here-Strings** の詳細については、<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings の使用</a>に関するページをご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。 このジョブに関するエラーを表示するには、以下を **hivejob.ps1** ファイルの末尾に追加して保存し、再実行します。

```powershell
# Print the output of the Hive job.
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

このコマンドレットは、ジョブの実行時にサーバー上の STDERR に書き込まれた情報を返します。

## <a name="summary"></a>まとめ

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a name="next-steps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)


