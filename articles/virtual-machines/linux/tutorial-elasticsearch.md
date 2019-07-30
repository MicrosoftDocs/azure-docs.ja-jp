---
title: ElasticSearch を Azure の開発仮想マシンにデプロイする
description: チュートリアル - Elastic Stack を Azure の開発 Linux VM にインストールする
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 4d6dce952eca3d528a310685106a017dd7e3b80f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66166053"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Elastic Stack を Azure VM にインストールする

この記事では、[Elasticsearch](https://www.elastic.co/products/elasticsearch)、[Logstash](https://www.elastic.co/products/logstash)、および[Kibana](https://www.elastic.co/products/kibana) を、Azure の Ubuntu VM にデプロイする方法について説明します。 実行中の Elastic Stack を表示するために、必要に応じて Kibana に接続し、サンプル ログ データを使用できます。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure リソース グループでの Ubuntu VM の作成
> * VM への Elasticsearch、Logstash、および Kibana のインストール
> * Logstash による Elasticsearch へのサンプル データの送信 
> * ポートのオープンと Kibana コンソールでのデータ操作


 このデプロイは、Elastic Stack での基本的な開発に適しています。 運用環境の推奨事項を含め、Elastic Stack の詳細については、[Elastic のドキュメント](https://www.elastic.co/guide/index.html)と [Azure アーキテクチャ センター](/azure/architecture/elasticsearch/)を参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm) コマンドで VM を作成します。 

次の例では、*myVM* という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>VM への SSH 接続

VM のパブリック IP アドレスが未確認である場合は、[az network public-ip list](/cli/azure/network/public-ip) コマンドを実行してください。

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

次のコマンドを使用して、仮想マシンとの SSH セッションを作成します。 ご使用の仮想マシンの正しいパブリック IP アドレスに置き換えてください。 この例の IP アドレスは *40.68.254.142* です。

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Elastic Stack のインストール

Elasticsearch 署名キーをインポートし、APT ソース リストを更新して、Elastic パッケージ リポジトリを追加します。

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

VM に Java Virtual をインストールし、JAVA_HOME 変数を構成します。これは、Elastic Stack コンポーネントを実行するうえで必要です。

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

次のコマンドを実行して、Ubuntu パッケージ ソースを更新し、Elasticsearch、Kibana、および Logstash をインストールします。

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> ディレクトリ レイアウト、初期構成など、インストール手順の詳細については、[Elastic のドキュメント](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)を参照してください

## <a name="start-elasticsearch"></a>Elasticsearch の起動 

次のコマンドを使用して、VM で Elasticsearch を起動します。

```bash
sudo systemctl start elasticsearch.service
```

このコマンドによる出力はありません。そこで、この `curl` コマンドを使用して、Elasticsearch が VM で実行されていることを確認します。

```bash
sudo curl -XGET 'localhost:9200/'
```

Elasticsearch が実行されている場合は、次のような出力が表示されます。

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Logstash の起動と、Elasticsearch へのデータの追加

次のコマンドを使用して、Logstash を起動します。

```bash 
sudo systemctl start logstash.service
```

対話モードで Logstash をテストして、適切に動作していることを確認します。

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

これは、標準入力を標準出力にエコーする基本的な logstash [パイプライン](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html)です。 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

この VM から Elasticsearch にカーネル メッセージを転送するように Logstash を設定します。 空のディレクトリに `vm-syslog-logstash.conf` という新しいファイルを作成し、次の Logstash 構成で貼り付けます。

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

この構成のテストし、syslog データを Elasticsearch に送信します。

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

端末の syslog エントリが、Elasticsearch に送信されたようにエコーされていることがわかります。 データをいくつか送信したら、`CTRL+C` を使用して Logstash を終了します。

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Kibana の起動と、Elasticsearch のデータの視覚化

`/etc/kibana/kibana.yml` を編集し、Web ブラウザーからアクセスできるように、リッスン中の Kibana の IP アドレスを変更します。

```bash
server.host:"0.0.0.0"
```

次のコマンドを使用して、Kibana を起動します。

```bash
sudo systemctl start kibana.service
```

Azure CLI からポート 5601 を開いて、Kibana コンソールへのリモート アクセスを許可します。

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Kibana コンソールを開いて、 **[作成]** を選択し、前に Elasticsearch に送信した syslog データに基づいて、既定のインデックスを生成します。 

![Kibana で Syslog イベントを参照](media/elasticsearch-install/kibana-index.png)

Kibana コンソールで **[検出]** を選択して、syslog イベントを検索、参照、およびフィルター処理します。

![Kibana で Syslog イベントを参照](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Elastic Stack を Azure の開発 VM にデプロイしました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Azure リソース グループでの Ubuntu VM の作成
> * VM への Elasticsearch、Logstash、および Kibana のインストール
> * Logstash から Elasticsearch へのサンプル データの送信 
> * ポートのオープンと Kibana コンソールでのデータ操作
