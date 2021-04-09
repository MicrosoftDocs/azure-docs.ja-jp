---
title: Azure CLI を使用した Azure HDInsight クラスターの管理
description: Azure CLI を使用して、Azure HDInsight クラスターを管理する方法について説明します。 クラスターの種類には、Apache Hadoop、Spark、HBase、Storm、Kafka、Interactive Query、および ML サービスがあります。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: b17c5a2abc036c16ff3ce36b81428f9149e36b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942852"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure CLI を使用した Azure HDInsight クラスターの管理

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure CLI](/cli/azure/) を使用して、Azure HDInsight クラスターを管理する方法について説明します。 Azure コマンド ライン インターフェイス (CLI) は、Azure リソースを管理するための、Microsoft のクロスプラットフォーム コマンド ライン エクスペリエンスです。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure CLI。 Azure CLI をインストールしていない場合、手順については「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

* HDInsight の Apache Hadoop クラスター。 [Linux での HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

## <a name="connect-to-azure"></a>Azure に接続する

Azure サブスクリプションにサインインします。 Azure Cloud Shell を使用する予定の場合は、コード ブロックの右上隅で **[使ってみる]** を選択します。 それ以外の場合は、次のコマンドを入力します。

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>クラスターを一覧表示する

[az hdinsight list](/cli/azure/hdinsight#az-hdinsight-list) を使用して、クラスターを一覧表示します。 次のコマンドを編集して `RESOURCE_GROUP_NAME` を自分のリソース グループの名前に置き換えてから、そのコマンドを入力します。

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>クラスターの表示

[az hdinsight show](/cli/azure/hdinsight#az-hdinsight-show) を使用して、指定したクラスターに関する情報を表示します。 次のコマンドを編集して `RESOURCE_GROUP_NAME` と `CLUSTER_NAME` を関連する情報に置き換えてから、そのコマンドを入力します。

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>クラスターの削除

[az hdinsight delete](/cli/azure/hdinsight#az-hdinsight-delete) を使用して、指定したクラスターを削除します。 次のコマンドを編集して `RESOURCE_GROUP_NAME` と `CLUSTER_NAME` を関連する情報に置き換えてから、そのコマンドを入力します。

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

クラスターが含まれるリソース グループを削除して、クラスターを削除することもできます。 これにより、既定のストレージ アカウントを含め、グループ内のすべてのリソースが削除されることに注意してください。

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>クラスターのスケール

[az hdinsight resize](/cli/azure/hdinsight#az-hdinsight-resize) を使用して、指定した HDInsight クラスターのサイズを指定のサイズに変更します。 次のコマンドを編集して、`RESOURCE_GROUP_NAME` と `CLUSTER_NAME` を関連する情報に置き換えます。 `WORKERNODE_COUNT` を、お使いのクラスターに必要なワーカー ノードの数に置き換えます。 クラスターのスケーリングの詳細については、[HDInsight クラスターのスケーリング](./hdinsight-scaling-best-practices.md)に関するページを参照してください。 次のコマンドを入力します。

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>次のステップ

この記事では、さまざまな HDInsight クラスター管理タスクを実行する方法について学習しました。 詳細については、以下の記事をお読みください。

* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI の概要](/cli/azure/get-started-with-azure-cli)
