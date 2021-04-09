---
title: Azure HDInsight を作成する - Azure Data Lake Storage Gen2 - Azure CLI
description: Azure CLI を使用して Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する方法について学習します。
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: bbc1cd27d5c16eddd3aaad748c34445e5017e209
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945488"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Azure CLI を使用して Data Lake Storage Gen2 でクラスターを作成する

ストレージに Data Lake Storage Gen2 を使用する HDInsight クラスターを作成するには、次の手順に従います。

## <a name="prerequisites"></a>前提条件

- Azure Data Lake Storage Gen2 に慣れていない場合は、[概要セクション](hdinsight-hadoop-use-data-lake-storage-gen2.md)をご覧ください。 
- まだ Azure アカウントを持っていない場合は、[無料のアカウントにサインアップ](https://azure.microsoft.com/free/)してから先に進んでください。
- CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。
    - Azure ポータルから [Azure Cloud Shell](../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
    - 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
    - ローカル CLI コンソールを使用する場合は、[Azure CLI の最新バージョン (2.0.13 以降) をインストール](/cli/azure/install-azure-cli)します。 ユーザー割り当てマネージド ID をデプロイする Azure サブスクリプションに関連付けられているアカウントを使用して、Azure に `az login` でサインインします (Azure CLI)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[サンプルのテンプレート ファイルをダウンロード](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)して、[サンプルのパラメーター ファイルをダウンロード](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)できます。 このテンプレートと以下の Azure CLI のコード スニペットを使用する前に、次のプレース ホルダーを正しい値に置き換えます。

| プレースホルダー | 説明 |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure サブスクリプションの ID です |
| `<RESOURCEGROUPNAME>` | 新しいクラスターとストレージ アカウントを作成するリソース グループです。 |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage Gen2 を使用するストレージ アカウントに対するアクセス許可を付与するマネージド ID の名前です。 |
| `<STORAGEACCOUNTNAME>` | 作成する、Azure Data Lake Storage Gen2 を使用する新しいストレージ アカウントです。 |
| `<FILESYSTEMNAME>`  | このクラスターがストレージ アカウントで使用する必要があるファイルシステムの名前。 |
| `<CLUSTERNAME>` | HDInsight クラスターの名前です。 |
| `<PASSWORD>` | SSH と Ambari ダッシュボードを使用してクラスターにサインインするために選択したパスワードです。 |

以下のコード スニペットでは、次の初期手順が実行されます。

1. Azure アカウントにログインします。
1. 作成操作が実行されるアクティブなサブスクリプションを設定します。
1. 新しいデプロイ アクティビティ用の新しいリソース グループを作成します。
1. ユーザー割り当てマネージド ID を作成します。
1. Data Lake Storage Gen2 の機能を使用するために、Azure CLI に拡張機能を追加します。
1. `--hierarchical-namespace true` フラグを使用して、Data Lake Storage Gen2 を使用する新しいストレージ アカウントを作成します。

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

次に、ポータルにサインインします。 ストレージ アカウントでユーザー割り当ての新しいマネージド ID を **ストレージ BLOB データ共同作成者** ロールに割り当てます。 この手順については、[Azure portal の使用](hdinsight-hadoop-use-data-lake-storage-gen2.md)に関するページの手順 3 で説明しています。

 > [!IMPORTANT]
 > ストレージ アカウントに、**ストレージ BLOB データ共同作成者** ロールのアクセス許可を持つユーザー割り当て ID があることを確認します。そうでない場合、クラスターの作成は失敗します。

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

記事を完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

次のコマンドのすべてまたは一部を入力して、リソースを削除します。

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](./hdinsight-hadoop-customize-cluster-linux.md#access-control)」を参照してください。

## <a name="next-steps"></a>次のステップ

HDInsight クラスターが正常に作成されました。 次に、クラスターを操作する方法を学習してください。

### <a name="apache-spark-clusters"></a>Apache Spark クラスター

* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)
* [Scala を使用してスタンドアロン アプリケーションを作成する](spark/apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](spark/apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop クラスター

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase クラスター

* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での Apache HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)
