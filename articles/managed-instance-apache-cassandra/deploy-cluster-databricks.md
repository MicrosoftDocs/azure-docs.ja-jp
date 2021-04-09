---
title: クイックスタート - Azure Databricks でマネージド Apache Spark クラスターをデプロイする
description: このクイックスタートでは、Azure portal を使用して Azure Databricks でマネージド Apache Spark クラスターをデプロイする方法について説明します。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747648"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>クイックスタート - Azure Databricks でマネージド Apache Spark クラスター (プレビュー) をデプロイする

Azure Managed Instance for Apache Cassandra は、マネージド オープンソースの Apache Cassandra データセンターのデプロイおよびスケーリング操作を自動化し、ハイブリッド シナリオを推進して継続的なメンテナンスを削減します。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

このクイックスタートでは、Azure portal を使用して、Azure Managed Instance for Apache Cassandra クラスターの Azure 仮想ネットワーク内にフル マネージド Apache Spark クラスターを作成する方法を紹介します。 Spark クラスターは Azure Databricks で作成します。 その後、そのクラスターにノートブックを作成 (アタッチ) し、さまざまなデータ ソースのデータを読み取って分析情報を得ます。

詳しい手順については、[Azure 仮想ネットワークでの Azure Databricks のデプロイ (仮想ネットワーク インジェクション)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject) に関するページも参照してください。

## <a name="create-an-azure-databricks-cluster"></a>Azure Databricks クラスターを作成する

Azure Managed Instance for Apache Cassandra がある仮想ネットワークに Azure Databricks クラスターを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーションから **[リソース グループ]** を探し、マネージド インスタンスがデプロイされている仮想ネットワークを含むリソース グループに移動します。

1. **[Virtual Network]** リソースを開き、 **[アドレス空間]** をメモしておきます。

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="仮想ネットワークのアドレス空間を入手する。" border="true":::

1. そのリソース グループから **[追加]** を選択し、検索フィールドで「**Azure Databricks**」を検索します。

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Azure Databricks を検索する。" border="true":::

1. **[作成]** を選択して Azure Databricks アカウントを作成します。

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Azure Databricks アカウントを作成する。" border="true":::

1. 次の値に入力します。

   * **[ワークスペース名]** - Databricks ワークスペースの名前を指定します。
   * **[リージョン]** - 仮想ネットワークと同じリージョンを必ず選択します。
   * **[価格レベル]** - Standard、Premium、Trial のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Databricks アカウントのワークスペース名、リージョン、価格レベルを記入する。" border="true":::

1. 次に、 **[ネットワーク]** タブを選択し、以下の情報を入力します。

   * **[Deploy Azure Databricks workspace in your Virtual Network (VNet)]\(仮想ネットワーク (VNet) に Azure Databricks ワークスペースをデプロイする\)** - **[はい]** を選択します。
   * **[仮想ネットワーク]** - マネージド インスタンスが存在する仮想ネットワークをボックスの一覧から選択します。
   * **[パブリック サブネット名]** - パブリック サブネットの名前を入力します。
   * **[パブリック サブネットの CIDR 範囲]** - パブリック サブネットの IP 範囲を入力します。
   * **[プライベート サブネット名]** - プライベート サブネットの名前を入力します。
   * **[プライベート サブネットの CIDR 範囲]** - プライベート サブネットの IP 範囲を入力します。

   範囲の競合を防ぐために、選択する範囲は大きくするようにしてください。 必要に応じて、[Visual Subnet Calculator](https://www.fryguy.net/wp-content/tools/subnets.html) を使用して範囲を分割します。

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="仮想ネットワーク サブネット計算ツールを使用する。" border="true":::

   次のスクリーンショットに、ネットワーク ペインの詳しい例を示します。

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="パブリック サブネット名とプライベート サブネット名を指定する。" border="true":::

1. **[確認と作成]** を選択し、 **[作成]** を選択してワークスペースをデプロイします。

1. 作成した **ワークスペースを起動** します。

1. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[New Cluster]\(新しいクラスター\)** を選択します。

1. **[New Cluster]\(新しいクラスター\)** ペインでは、次のフィールドを除くすべてのフィールドの既定値をそのまま使用します。

   * **[クラスター名]** - クラスターの名前を入力します。
   * **[Databricks Runtime Version]\(Databricks Runtime のバージョン\)** - Cassandra Connector でサポートされている Scala 2.11 以下のバージョンを選択します。

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Databricks Runtime のバージョンと Spark クラスターを選択する。" border="true":::

1. **[Advanced Options]\(詳細オプション\)** を展開し、次の構成を追加します。 ノードの IP と資格情報は、必ず置き換えてください。

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. **[ライブラリ]** タブから、Cassandra 用 Spark コネクタの最新バージョン (*spark-cassandra-connector*) をインストールし、クラスターを再起動します。

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Cassandra コネクタをインストールする。" border="true":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このマネージド インスタンス クラスターを引き続き使用しない場合は、次の手順でそれを削除します。

1. Azure portal の左側にあるメニューで、 **[リソース グループ]** を選択します。
1. 一覧から、このクイック スタートで作成したリソース グループを選択します。
1. リソース グループの **[概要]** ペインで、 **[リソース グループの削除]** を選択します。
3. 次のウィンドウで、削除するリソース グループの名前を入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Managed Instance for Apache Cassandra クラスターの仮想ネットワーク内にフル マネージドの Apache Spark クラスターを作成する方法について説明しました。 次に、クラスターとデータセンターのリソースを管理する方法を学習します。 

> [!div class="nextstepaction"]
> [Azure CLI を利用して Azure Managed Instance for Apache Cassandra リソースを管理する](manage-resources-cli.md)

