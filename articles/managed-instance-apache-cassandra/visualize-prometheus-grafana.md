---
title: Azure Managed Instance for Apache Cassandra から生成されたメトリックを視覚化するように Grafana を構成する
description: Azure Managed Instance for Apache Cassandra クラスターから生成されたメトリックを視覚化するために、VM に Grafana をインストールして構成する方法について説明します。
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743797"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>マネージド インスタンス クラスターから生成されたメトリックを視覚化するように Grafana を構成する

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra は現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Managed Instance for Apache Cassandra クラスターをデプロイすると、さまざまなクライアント ツールで利用できる [Prometheus](https://prometheus.io/) をホストするサーバーがこのサービスによってプロビジョニングされます。 Prometheus は、オープンソースの監視ソリューションです。 マネージド インスタンスはメトリックを生成し、データが 10 GB に達するか 10 分が経過するまで (どちらかのしきい値に達するまで) それらを保持します。 この記事では、マネージド インスタンス クラスターから生成されたメトリックを視覚化するように Grafana を構成する方法について説明します。 メトリックを視覚化するには、次のタスクが必要となります。

* マネージド インスタンスが存在する Azure 仮想ネットワーク内に Ubuntu 仮想マシンをデプロイします。
* ダッシュボードを作成し、Prometheus から生成されたメトリックを視覚化するためのオープンソースの [Grafana ツール](https://grafana.com/grafana/)をインストールします。

## <a name="deploy-a-ubuntu-server"></a>Ubuntu サーバーをデプロイする

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. マネージド インスタンス クラスターがあるリソース グループに移動します。 **[追加]** を選択し、**Ubuntu Server 18.04 LTS** イメージを検索します。

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Azure portal から Ubuntu サーバー イメージを検索する。" border="true":::

1. そのイメージを選択し、 **[作成]** を選択します。

1. **[仮想マシンの作成]** ブレードで、次の各フィールドの値を入力します。その他のフィールドは既定値のままでかまいません。

   * **[仮想マシン名]** - VM の名前を入力します。
   * **[リージョン]** - 仮想ネットワークのデプロイ先と同じリージョンを選択します。

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Ubuntu サーバー イメージを使用して VM を作成するためのフォームに記入する。" border="true":::

1. **[ネットワーク]** タブで、マネージド インスタンスがデプロイされている仮想ネットワークを選択します。

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Ubuntu サーバーのネットワーク設定を構成する。" border="true":::

1. 最後に **[確認と作成]** を選択して Grafana サーバーを作成します。

## <a name="install-grafana"></a>Grafana のインストール

1. Azure portal から、マネージド インスタンスと Grafana サーバーをデプロイした仮想ネットワークを開きます。 **cassandra-jump (instance 0)** という名前の仮想マシン スケール セット インスタンスが表示されます。 この Prometheus のメトリックは、この仮想マシン スケール セット内でホストされます。 このインスタンスの IP アドレスを書き留めておいてください。

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Prometheus インスタンスの IP アドレスを入手する。" border="true":::

1. [Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) や任意のクライアント ツールを使用して、新しく作成した Ubuntu サーバーに SSH で接続します。

1. VM に接続した後、Grafana をインストールし、メトリックのホストとなる仮想マシン スケール セットに接続するための構成を行う必要があります。 コマンド プロンプトを開き、`nano` コマンドを入力して Nano テキスト エディターを開きます。 次のスクリプトをテキスト エディターに貼り付けます。`<prometheus IP address>` は、前の手順で記録した IP アドレスに置き換えてください。

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. 「`ctrl + X`」と入力してファイルを保存します。 このファイルには、`grafana.sh` という名前を付けます。

1. コマンド プロンプトに `./grafana.sh` コマンドを入力して Grafana をインストールします。

1. インストールが完了すると、次のスクリーンショットに示すように、サーバーの IP アドレスの **ポート 3000** で Grafana を利用できるようになります。

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="ポート 3000 で Grafana を実行する。" border="true":::

1. Grafana では、Apache Cassandra 向けに作成されたオープンソースのダッシュボード ([cluster-overview](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) JSON ファイルなど) を選択できます。 ダッシュボードの JSON 定義をダウンロードして Grafana にインポートします。

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Grafana の JSON 定義をインポートする。" border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Grafana の JSON 定義をアップロードする。" border="true":::

1. その後、選択したダッシュボードを使用して、Cassandra マネージド インスタンス クラスターを監視することができます。

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Cassandra マネージド インスタンスのメトリックをダッシュボードで表示する。" border="true":::

## <a name="next-steps"></a>次のステップ

この記事では、Grafana を使用してダッシュボードを構成し、Prometheus のメトリックを視覚化する方法について説明しました。 Azure Managed Instance for Apache Cassandra の詳細については、次の記事を参照してください。

* [Azure Managed Instance for Apache Cassandra の概要](introduction.md)
* [Azure Databricks でマネージド Apache Spark クラスターをデプロイする (プレビュー)](deploy-cluster-databricks.md)
