---
title: Azure HDInsight 上のカスタム Apache Ambari データベース
description: 独自のカスタム Apache Ambari データベースを使用して HDInsight クラスターを作成する方法について学習します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: 1858e06567a0ab0907e6d2cb60358ff4ac00f9a2
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086349"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>カスタム Ambari DB を使用して HDInsight クラスターを設定する

Apache Ambari を使用すると、Apache Hadoop クラスターを簡単に管理および監視できます。 Apache Ambari には、使いやすい Web UI と REST API が用意されています。 Ambari は HDInsight クラスターに含まれ、クラスターの監視と構成の変更を行うために使用します。

他の記事 ([HDInsight でのクラスターの設定](hdinsight-hadoop-provision-linux-clusters.md)に関する記事など) で説明されているように、通常のクラスターの作成では、Ambari は HDInsight によって管理され、ユーザーがアクセスできない [S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) にデプロイされます。

カスタム Ambari DB 機能を使用すると、新しいクラスターをデプロイし、自分が管理する外部データベースに Ambari を設定できます。 デプロイには、Azure Resource Manager テンプレートを使用します。 この機能には、次のような利点があります。

- カスタマイズ - データベースのサイズと処理容量を選択します。 大規模なクラスターで大量のワークロードを処理している場合は、性能の低い Ambari データベースが管理操作のボトルネックになる可能性があります。
- 柔軟性 - 必要に応じて、要件に合わせてデータベースをスケーリングできます。
- 管理 - データベースのバックアップとセキュリティを、組織の要件に合った方法で管理できます。

この記事の残りの部分では、次の点について説明します。

- カスタム Ambari DB 機能を使用するための要件
- Apache Ambari 用の独自の外部データベースを使用して HDInsight クラスターをプロビジョニングするために必要な手順

## <a name="custom-ambari-db-requirements"></a>カスタム Ambari DB の要件

すべてのクラスターの種類とバージョンでカスタム Ambari DB をデプロイできます。 複数のクラスターで同じ Ambari DB を使用することはできません。

カスタム Ambari DB には、これ以外に次の要件があります。

- データベースの名前にハイフンまたはスペースを含めることはできません
- 既存の Azure SQL DB サーバーとデータベースが必要です。
- Ambari の設定用に指定するデータベースは、空である必要があります。 既定の dbo スキーマにテーブルが含まれていてはいけません。
- データベースへの接続に使用するユーザーには、データベースに対する SELECT、CREATE TABLE、および INSERT 権限が必要です。
- Ambari をホストするサーバー上で [[Azure サービスへのアクセスを許可]](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) オプションをオンにします。
- ファイアウォール規則で、HDInsight サービスからの管理 IP アドレスを許可する必要があります。 サーバーレベルのファイアウォール規則に追加する必要がある IP アドレスの一覧については、「[HDInsight の管理 IP アドレス](hdinsight-management-ip-addresses.md)」を参照してください。

Apache Ambari DB を外部データベースでホストする場合は、次の点に注意してください。

- Ambari を保持する Azure SQL DB の追加コストの責任はお客様にあります。
- カスタム Ambari DB を定期的にバックアップします。 Azure SQL Database ではバックアップが自動的に生成されますが、バックアップのリテンション期間は異なります。 詳しくは、「[SQL Database 自動バックアップについての詳細情報](../azure-sql/database/automated-backups-overview.md)」をご覧ください。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>カスタム Ambari DB を使用するクラスターをデプロイする

独自の外部 Ambari データベースを使用する HDInsight クラスターを作成するには、[カスタム Ambari DB クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)を使用します。

`azuredeploy.parameters.json` 内のパラメーターを編集して、新しいクラスターと、Ambari を保持するデータベースに関する情報を指定します。

Azure CLI を使用してデプロイを開始できます。 `<RESOURCEGROUPNAME>` を、クラスターをデプロイするリソース グループに置き換えます。

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>次のステップ

- [Azure HDInsight で外部メタデータ ストアを使用する](hdinsight-use-external-metadata-stores.md)
