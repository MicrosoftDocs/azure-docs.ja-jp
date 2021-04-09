---
title: Grafana を使用して NSG フロー ログを管理する
titleSuffix: Azure Network Watcher
description: Network Watcher と Grafana を使用して Azure のネットワーク セキュリティ グループのフロー ログを管理および分析します。
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: d522d305c70214009b8aa2886d07d2d5403dd2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656310"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Network Watcher と Grafana を使用してネットワーク セキュリティ グループのフロー ログを管理および分析する

[ネットワーク セキュリティ グループ (NSG) のフロー ログ](network-watcher-nsg-flow-logging-overview.md)の情報を使用して、ネットワーク インターフェイスでのイングレスおよびエグレス IP トラフィックについて理解できます。 こうしたフロー ログからは、NSG ルールごとの送信および受信フロー、フローが適用されている NIC、フローに関する 5 組の情報 (送信元/送信先 IP、送信元/送信先ポート、プロトコル)、およびトラフィックの許可/拒否の状況がわかります。

フロー ログが有効になっているネットワークには多くの NSG がある場合があります。 このログ データ量により、ログを解析して洞察を得ることが煩雑になります。 この記事では、オープン ソースのグラフ作成ツールである Grafana、分散型の検索および分析エンジンである ElasticSearch、オープン ソースのサーバー側のデータ処理パイプラインである Logstash を使用してこれらの NSG フロー ログを一元的に管理するソリューションについて説明します。  

## <a name="scenario"></a>シナリオ

NSG フロー ログは、Network Watcher を使用して有効化され、Azure Blob Storage に格納されます。 Logstash プラグインを使用して、Blob Storage に接続してそこからのフロー ログを処理し、ElasticSearch に送信します。  ElasticSearch にフロー ログが格納されたら、これらを分析し、Grafana のカスタマイズされたダッシュ ボードに視覚化できます。

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>インストール手順

### <a name="enable-network-security-group-flow-logging"></a>ネットワーク セキュリティ グループのフロー ログの有効化

このシナリオでは、アカウント内の少なくとも 1 つのネットワーク セキュリティ グループで、ネットワーク セキュリティ グループのフロー ログを有効にする必要があります。 ネットワーク セキュリティ フロー ログを有効にする手順については、「[Introduction to flow logging for Network Security Groups (ネットワーク セキュリティ グループのフロー ログの概要)](network-watcher-nsg-flow-logging-overview.md)」をご覧ください。

### <a name="setup-considerations"></a>設定の考慮事項

この例では、Grafana、ElasticSearch、Logstash は Azure にデプロイされた Ubuntu 16.04 LTS サーバー上で構成されます。 この最小限の設定が 3 つのコンポーネントすべてを実行するために使用されます。これらのコンポーネントは、すべて同じ VM で実行されます。 この設定は、テストおよび重大ではないワークロードにのみ使用する必要があります。 Logstash、Elasticsearch、Grafana はすべて、多くのインスタンスで個別に拡大縮小するように構築できます。 詳細については、これらの各コンポーネントのドキュメントをご覧ください。

### <a name="install-logstash"></a>Logstash のインストール

Logstash を使用して、JSON 形式のフロー ログをフロー タプル レベルにフラット化します。

1. Logstash をインストールするには、次のコマンドを実行します。

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. フロー ログを解析し、ElasticSearch に送信するように Logstash を構成します。 以下のコマンドで Logstash.conf ファイルを作成します。

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. 次のコンテンツをファイルに追加します。 ストレージ アカウントの詳細を反映するようにストレージ アカウント名とアクセス キーを変更します。

   ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
    "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
    "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
    "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
    "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
    "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
   ```

用意されている Logstash 構成ファイルは、input、filter、output の 3 つの部分で構成されます。
input セクションでは、Logstash が処理するログの入力ソースを指定します。この例では、(次の手順でインストールする) "azureblob" 入力プラグインを使用します。この入力プラグインを使用すると、Blob Storage に保存されている NSG フロー ログの JSON ファイルにアクセスできます。 

filter セクションは、個々のフロー タプルとそれに関連付けられたプロパティが別の Logstash イベントになるように、各フロー ログ ファイルをフラット化します。

最後に、output セクションは、各 Logstash イベントを ElasticSearch サーバーに転送します。 Logstash 構成ファイルは、特定のニーズに合わせて自由に変更できます。

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Azure Blob Storage 向けの Logstash 入力プラグインのインストール

この Logstash プラグインを使用すると、指定された Blob Storage アカウントから、フロー ログに直接アクセスできます。 このプラグインをインストールするには、既定の Logstash インストール ディレクトリ (この場合、/usr/share/logstash/bin) から、次のコマンドを実行します。

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

このプラグインの詳細については、[Azure Storage Blob の Logstash 入力のプラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)に関するページをご覧ください。

### <a name="install-elasticsearch"></a>ElasticSearch のインストール

次のスクリプトを使用して、ElasticSearch をインストールすることができます。 ElasticSearch のインストールの詳細については、[Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html) のページをご覧ください。

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Grafana のインストール

Grafana をインストールして実行するには、次のコマンドを実行します。

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

追加のインストール情報については、「[Installing on Debian / Ubuntu (Debian/Ubuntu へのインストール)](https://docs.grafana.org/installation/debian/)」をご覧ください。

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>ElasticSearch サーバーをデータ ソースとして追加する

次に、フロー ログを含む ElasticSearch インデックスをデータ ソースとして追加する必要があります。 **[データ ソースの追加]** を選択し、関連情報をフォームに入力して、データ ソースを追加することができます。 この構成のサンプルは、次のスクリーン ショットをご覧ください。

![データ ソースを追加する](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>ダッシュボードを作成する

NSG フロー ログを含む ElasticSearch インデックスから読み取るように Grafana が正常に構成されたら、ダッシュボードを作成し、カスタマイズできます。 新しいダッシュボードを作成するには、 **[Create your first dashboard]\(最初のダッシュボードの作成\)** を選択します。 次のサンプル グラフ構成は、NSG ルールによってセグメント化されたフローを示しています。

![ダッシュボード グラフ](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

次のスクリーンショットは、最上位のフローとその頻度を示すグラフとチャートを示しています。 フローは、NSG ルールごと、および決定別のフローごとにも表示されます。 Grafana はカスタマイズの自由度が高いため、特定の監視ニーズに合わせてダッシュボードを作成することをお勧めします。 次の例は、一般的なダッシュボードを示しています。

![NSG ルールによってセグメント化されたフローを含むサンプル グラフ構成を示すスクリーンショット。](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>まとめ

Network Watcher を ElasticSearch および Grafana と統合すると、NSG フロー ログだけでなく他のデータを簡単かつ一元的に管理および視覚化することができます。 Grafana には、他にもいくつかの強力なグラフ機能があります。その機能を使用して、フロー ログをさらに細かく管理し、ネットワーク トラフィックをより深く理解できます。 Grafana インスタンスを設定し、Azure に接続したので、提供される他の機能の探索に進むことができます。

## <a name="next-steps"></a>次のステップ

- [Network Watcher](network-watcher-monitoring-overview.md) の詳細な使用方法を確認します。

