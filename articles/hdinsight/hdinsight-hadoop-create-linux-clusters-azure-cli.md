---
title: Azure クラシック CLI を使用して Apache Hadoop クラスターを作成する - Azure HDInsight
description: クロスプラットフォームの Azure クラシック CLI を使用して HDInsight クラスターを作成する方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: c9b1f3570ae3f9b945c87c2ce316c3f72e32b4ad
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792052"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Azure クラシック CLI を使用して HDInsight クラスターを作成する

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

このドキュメントの手順では、Azure クラシック CLI を使用して HDInsight 3.5 クラスターをプロセスを順を追って説明します。

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* **Azure クラシック CLI**。 このドキュメントの手順は、Azure クラシック CLI バージョン 0.10.14 で最後にテストされました。

## <a name="log-in-to-your-azure-subscription"></a>Azure サブスクリプションにログイン

「[Azure コマンド ライン インターフェイスからの Azure サブスクリプションへの接続](/cli/azure/authenticate-azure-cli)」に記載されている手順に従い、**login** メソッドを使用してサブスクリプションに接続します。

## <a name="create-a-cluster"></a>クラスターの作成

PowerShell または Bash などのコマンド ラインから、次の手順を実行してください。

1. 次のコマンドを使用して、Azure サブスクリプションに対して認証します。

        azure login

    名前とパスワードを入力するように求められます。 複数の Azure サブスクリプションがある場合は、`azure account set <subscriptionname>` を使用して、クラシック CLI コマンドが使用するサブスクリプションを設定します。

2. 次のコマンドで Azure リソース マネージャー モードに切り替えます。

        azure config mode arm

3. リソース グループを作成します。 このリソース グループには、HDInsight クラスターおよび関連するストレージ アカウントが含まれます。

        azure group create groupname location

    * `groupname` には、グループの一意の名前を指定します。

    * `location` には、グループの作成先となる地理的領域を指定します。

       グループの作成先として有効な場所は、`azure location list` コマンドで一覧表示できます。`Name` 列に表示されるいずれかの場所を使用してください。

4. ストレージ アカウントを作成します。 このストレージ アカウントは、HDInsight クラスターの既定のストレージとして使用されます。

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * `groupname` には、前の手順で作成したグループの名前を指定します。

    * `location` には、前の手順で使用した場所を指定します。

    * `storagename` には、ストレージ アカウントの一意の名前を指定します。

        > [!NOTE]  
        > このコマンドのパラメーターの詳細については、「`azure storage account create -h`」と入力してコマンドのヘルプを表示してください。

5. ストレージ アカウントにアクセスするためのキーを取得します。

        azure storage account keys list -g groupname storagename

    * `groupname` には、リソース グループ名を指定します。
    * `storagename` には、ストレージ アカウントの名前を指定します。

      返されたデータで、`key1` の `key` の値を保存します。

6. HDInsight クラスターを作成します。

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * `groupname` には、リソース グループ名を指定します。

    * `Hadoop` には、作成するクラスターの種類を指定します。 たとえば、`Hadoop`、`HBase`、`Kafka`、`Spark`、`Storm` などです。

      > [!IMPORTANT]  
      > HDInsight クラスターにはさまざまな種類があり、それぞれに適したワークロードやテクノロジに対応しています。 複数の種類 (Storm と HBase など) を組み合わせたクラスターを作成することはできません。

    * `location` には、前の手順で使用した場所を指定します。

    * `storagename` には、ストレージ アカウント名を指定します。

    * `storagekey` には、前の手順で取得したキーを指定します。

    * `--defaultStorageContainer` パラメーターには、クラスターに使用している名前と同じ名前を指定します。

    * `admin` と `httppassword` には、HTTPS を使用してクラスターにアクセスするときに使用する名前とパスワードを指定します。

    * `sshuser` と `sshuserpassword` には、SSH を使用してクラスターにアクセスするときに使用するユーザー名とパスワードを指定します。

      > [!IMPORTANT]  
      > この例では、2 つの worker ノードを持つクラスターが作成されます。 クラスターの作成後にスケーリング操作を実行することによって、ワーカー ノードの数を変更することもできます。 32 個を超えるワーカー ノードの使用を予定している場合は、コアが 8 個以上で RAM が 14 GB 以上のヘッド ノード サイズを選択する必要があります。 ヘッド ノード サイズは、クラスターの作成中に `--headNodeSize` パラメーターを使用して設定できます。
      >
      > ノードのサイズと関連コストに関する詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。
      
      クラスターの作成処理は、完了までに数分かかる場合があります。 通常は約 15 です。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順

クラシック CLI を使用して HDInsight クラスターを作成したら、クラスターの使用方法について、以下のトピックを参照してください。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop クラスター

* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase クラスター

* [HDInsight での Apache HBase の使用](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight での Apache HBase の Java アプリケーションの開発](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm クラスター

* [HDInsight での Apache Storm の Java トポロジの開発](storm/apache-storm-develop-java-topology.md)
* [HDInsight の Apache Storm での Python コンポーネントの使用](storm/apache-storm-develop-python-topology.md)
* [Deploy and monitor topologies with Apache Storm on HDInsight (HDInsight の Storm を使用したトポロジのデプロイと監視)](storm/apache-storm-deploy-monitor-topology-linux.md)
