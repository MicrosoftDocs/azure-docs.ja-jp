---
title: "PowerShell を使用して Hadoop クラスターを作成する - Azure HDInsight | Microsoft Docs"
description: "Azure PowerShell を使用して、HDInsight 用の Linux ベースの Hadoop、HBase、Storm、または Spark クラスターを作成する方法について説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: ca75974e6ec4f60739137d4cb5458bbfd735de3e
ms.contentlocale: ja-jp
ms.lasthandoff: 06/13/2017

---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Azure PowerShell を使用した HDInsight の Linux ベースのクラスターの作成

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell は、Microsoft Azure のワークロードのデプロイと管理を制御し、自動化するために使用できる強力なスクリプト作成環境です。 このドキュメントでは、Azure PowerShell を使用して Linux ベースの HDInsight クラスターを作成する方法について説明します。 また、スクリプトの例も含まれています。

> [!NOTE]
> Azure PowerShell は、Windows クライアントだけで利用できます。 Linux、Unix、または Mac OS X クライアントを使用している場合、Azure CLI を使用したクラスターの作成方法については、 [Azure CLI を使用した Linux ベースの HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md) に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件
この手順を開始するには、以下が必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > Azure Service Manager を使用した HDInsight リソースの管理に関する Azure PowerShell のサポートは**廃止**され、2017 年 1 月 1 日に削除されました。 このドキュメントの手順では、Azure Resource Manager で機能する新しい HDInsight コマンドレットを使用します。
    >
    > [Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関するページの手順に従って、Azure PowerShell の最新バージョンをインストールしてください。 Azure Resource Manager で機能する新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合、詳細については、「 [Migrating to Azure Resource Manager-based development tools for HDInsight clusters (HDInsight クラスターの Azure Resource Manager ベースの開発ツールへの移行)](hdinsight-hadoop-development-using-azure-resource-manager.md) 」をご覧ください。

## <a name="create-cluster"></a>クラスターの作成

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure PowerShell を使用して HDInsight クラスターを作成するには、次の手順を完了する必要があります。

* Azure リソース グループの作成
* Azure Storage アカウントの作成
* Azure BLOB コンテナーの作成
* HDInsight クラスターの作成

次のスクリプトでは、新しいクラスターを作成する方法を示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

クラスターのログインに指定する値は、クラスターの Hadoop ユーザー アカウントの作成に使用されます。 Web UI や REST API など、クラスターでホストされるサービスに接続するには、このアカウントを使用します。

SSH ユーザーに指定する値は、クラスターの SSH ユーザーの作成に使用されます。 このアカウントを使用してクラスターでリモート SSH セッションを開始し、ジョブを実行します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

> [!IMPORTANT]
> (クラスターの作成時または作成後のスケーリングで) 32 個を超える worker ノードを使用することを計画している場合は、コア数が 8 個以上、RAM が 14 GB 以上のヘッド ノード サイズも指定する必要があります。
>
> ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

クラスターを作成するには最大 20 分かかることがあります。

## <a name="create-cluster-configuration-object"></a>クラスターの作成: 構成オブジェクト

HDInsight の構成オブジェクトは、`New-AzureRmHDInsightClusterConfig` コマンドレットを使用して作成することもできます。 作成後に、クラスターの追加の構成オプションを有効にするように、この構成オブジェクトを変更できます。 最後に、`New-AzureRmHDInsightCluster` コマンドレットの `-Config` パラメーターを使用して構成を使用します。

次のスクリプトでは、HDInsight クラスター タイプで R Server を構成する構成オブジェクトを作成します。 この構成は、エッジ ノード、RStudio、および追加のストレージ アカウントを有効にします。

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> HDInsight クラスター以外の場所でストレージ アカウントを使用することはできません。 この例の使用時には、サーバーと同じ場所に追加のストレージ アカウントを作成します。

## <a name="customize-clusters"></a>クラスターのカスタマイズ

* 「 [ブートストラップを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)」を参照してください。
* 「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

## <a name="delete-the-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight クラスターが正常に作成されました。次に、クラスターの使用方法について、以下のリソースを参照してください。

### <a name="hadoop-clusters"></a>Hadoop クラスター

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase クラスター

* [HDInsight での HBase の使用](hdinsight-hbase-tutorial-get-started-linux.md)
* [HDInsight での HBase の Java アプリケーションの開発](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm クラスター

* [HDInsight での Storm の Java トポロジの開発](hdinsight-storm-develop-java-topology.md)
* [HDInsight の Storm での Python コンポーネントの使用](hdinsight-storm-develop-python-topology.md)
* [HDInsight の Storm を使用したトポロジのデプロイと監視](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Spark クラスター

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)


