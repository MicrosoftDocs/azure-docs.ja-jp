---
title: NSG フロー ログの視覚化 - Elastic Stack
titleSuffix: Azure Network Watcher
description: Network Watcher と Elastic Stack を使用して Azure のネットワーク セキュリティ グループ フロー ログを管理および分析します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: aca8c75f262e472cbc770c052b86d6e760ee449a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026473"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>オープン ソース ツールを使用した Azure Network Watcher NSG フロー ログの視覚化

ネットワーク セキュリティ グループのフロー ログの情報は、ネットワーク セキュリティ グループでのイングレスおよびエグレス IP トラフィックについて理解するのに役立ちます。 こうしたフロー ログからは、ルールごとの送信および受信フロー、フローが適用されている NIC、フローに関する 5 組の情報 (送信元/送信先 IP、送信元/送信先ポート、プロトコル)、およびトラフィックの許可/拒否の状況が分かります。

これらのフロー ログを手動で解析したり、そこから手動で有用な情報を取得したりするのは難しいかもしれません。 しかし、いくつかのオープン ソース ツールによって、こうしたデータを視覚化できます。 この記事では、Elastic Stack を使用してこれらのログを視覚化するソリューションについて説明します。このソリューションによって、Kibana ダッシュボードのフロー ログに簡単にインデックスを作成して視覚化できます。

## <a name="scenario"></a>シナリオ

この記事では、Elastic Stack を使用してネットワーク セキュリティ グループのフロー ログを視覚化するソリューションを設定します。  Logstash 入力プラグインは、フロー ログを格納するように構成されているストレージ BLOB から直接、フロー ログを取得します。 Elastic Stack を使用して、フロー ログにインデックスを作成し、Kibana ダッシュボードを作成して、情報を視覚化します。

![Elastic Stack を使用してネットワーク セキュリティ グループのフロー ログを視覚化できるシナリオの図。][scenario]

## <a name="steps"></a>手順

### <a name="enable-network-security-group-flow-logging"></a>ネットワーク セキュリティ グループのフロー ログの有効化
このシナリオでは、アカウント内の少なくとも 1 つのネットワーク セキュリティ グループで、ネットワーク セキュリティ グループのフロー ログを有効にする必要があります。 ネットワーク セキュリティ フロー ログを有効にする手順については、「[Introduction to flow logging for Network Security Groups (ネットワーク セキュリティ グループのフロー ログの概要)](network-watcher-nsg-flow-logging-overview.md)」をご覧ください。

### <a name="set-up-the-elastic-stack"></a>Elastic Stack の設定
NSG のフロー ログを Elastic Stack に接続すると、Kibana ダッシュボードを作成して、ログから得た有用な情報を検索、グラフ化、分析、取得できます。

#### <a name="install-elasticsearch"></a>Elasticsearch のインストール

1. Elastic Stack のバージョン 5.0 以降では、Java 8 が必要です。 `java -version` コマンドを実行して、現在のバージョンを確認します。 Java がインストールされていない場合は、[Azure でサポートされている JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) に関するドキュメントを参照してください。
2. お使いのシステムに適合するバイナリ パッケージをダウンロードします。

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   他のインストール方法については[Elasticsearch のインストール](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)のページを参照してください。

3. 次のコマンドを使用して、Elasticsearch が実行されていることを確認します。

    ```bash
    curl http://127.0.0.1:9200
    ```

    次のような応答画面が表示されます。

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Elasticsearch のインストール方法について詳しくは、[インストールの方法](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)に関するページをご覧ください。

### <a name="install-logstash"></a>Logstash のインストール

1. Logstash をインストールするには、次のコマンドを実行します。

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. 次に、フロー ログにアクセスし、解析するように Logstash を構成する必要があります。 以下のコマンドで logstash.conf ファイルを作成します。

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. 次の内容をファイルに追加します。

   ```
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

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
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
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
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

Logstash のインストール方法の詳細については、[公式ドキュメント](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)を参照してください。

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Azure Blob Storage 向けの Logstash 入力プラグインのインストール

この Logstash プラグインを使用すると、指定されたストレージ アカウントから、フロー ログに直接アクセスできます。 このプラグインをインストールするには、既定の Logstash インストール ディレクトリ (この場合、/usr/share/logstash/bin) から、次のコマンドを実行します。

```bash
logstash-plugin install logstash-input-azureblob
```

Logstash を開始するには、次のコマンドを実行します。

```bash
sudo /etc/init.d/logstash start
```

このプラグインについて詳しくは、[ドキュメント](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)をご覧ください。

### <a name="install-kibana"></a>Kibana のインストール

1. Kibana をインストールするには、次のコマンドを実行します。

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Kibana を実行するには、次のコマンドを使用します。

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Kibana Web インターフェイスを表示するには、`http://localhost:5601` に移動します。
4. ここでは、フロー ログで使用されているインデックスのパターンは "nsg-flow-logs" です。 インデックスのパターンは、logstash.conf ファイルの "output" セクションで変更できます。
5. Kibana ダッシュボードをリモートで確認する場合は、NSG 受信ルールを作成して、**ポート 5601** にアクセスできるようにします。

### <a name="create-a-kibana-dashboard"></a>Kibana ダッシュボードの作成

アラートの傾向と詳細を表示するサンプル ダッシュボードを、次の図に示します。

![図 1][1]

[ダッシュボード ファイル](https://aka.ms/networkwatchernsgflowlogdashboard)、[視覚化ファイル](https://aka.ms/networkwatchernsgflowlogvisualizations)、[保存された検索ファイル](https://aka.ms/networkwatchernsgflowlogsearch)をそれぞれダウンロードします。

Kibana の **[Management (管理)]** タブの下で **[Saved Objects (保存されたオブジェクト)]** に移動して、3 つのファイルすべてをインポートします。 これで、 **[Dashboard (ダッシュボード)]** タブからサンプル ダッシュボードを開いて読み込むことができます。

関心のあるメトリックに合わせて独自の視覚化とダッシュボードを作成することもできます。 Kibana の視覚化を作成する方法の詳細については、Kibana の[公式ドキュメント](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)を参照してください。

### <a name="visualize-nsg-flow-logs"></a>NSG フロー ログの視覚化

サンプルのダッシュボードには、フロー ログの視覚化データが複数用意されています。

1. Flows by Decision/Direction Over Time (決定/方向別のフロー (時系列)) - 一定時間内のフローの数を時系列のグラフで表示します。 これらのグラフでは、時間の単位と範囲を編集できます。 Flows by Decision (決定別のフロー) は、許可または拒否の決定の割合を示しています。Flows by Direction (方向別のフロー) は、受信トラフィックと送信トラフィックの割合を示しています。 これらのグラフで、時間の経過に伴う傾向を精査し、急激な増加や異常なパターンがないかどうかを確認できます。

   ![サンプル ダッシュボードのスクリーンショット。時間の経過に伴うフローが決定別と方向別に表示されています。][2]

2. Flows by Destination/Source Port (送信先ポート/送信元ポート別のフロー) - 該当するポートごとにフローを分割した円グラフです。 このビューでは、最もよく使用されるポートを確認できます。 円グラフ内の特定のポートをクリックすると、ダッシュボードの空いている部分に、そのポートのフローの詳細が表示されます。

   ![サンプル ダッシュボードのスクリーンショット。フローが送信先別と送信元別に表示されています。][3]

3. Number of Flows (フローの数) と Earliest Log Time (最も早いログ時刻) - 記録されたフローの数と、ログが取得された最も早い時刻を示すメトリックです。

   ![サンプル ダッシュボードのスクリーンショット。フローの数と最も早いログ時刻を確認できます。][4]

4. Flows by NSG and Rule (NSG ごとのフローとルール) - 各 NSG 内のフローの分布、および各 NSG 内のルールの分布を示す棒グラフです。 ここから、どの NSG とルールがトラフィックの多くの部分を生成しているかを確認できます。

   ![サンプル ダッシュボードのスクリーンショット。フローが NSG 別とルール別に表示されています。][5]

5. Top 10 Source/Destination IPs (送信元/送信先 IP の上位 10 個) - 送信元/送信先 IP の上位 10 個を示す棒グラフです。 グラフを調整して、表示される IP を増やしたり減らしたりできます。 ここから、最もよく使われる IP や、各 IP へのトラフィックの決定 (許可または拒否) を確認できます。

   ![サンプル ダッシュボードのスクリーンショット。フローが上位 10 の送信元別と送信先 IP アドレス別に表示されています。][6]

6. Flow Tuples (フロー タプル) - 各フロー タプルに含まれる情報と、対応する NSG およびルールを表示する表です。

   ![テーブルのフロー タプルのスクリーンショット。][7]

ダッシュボードの最上部にあるクエリ バーを使用して、フローのパラメーター (サブスクリプション ID、リソース グループ、ルール、また関連するその他の変数など) に基づいて、ダッシュボードをフィルターできます。 Kibana のクエリとフィルターの詳細については、[公式ドキュメント](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)を参照してください。

## <a name="conclusion"></a>まとめ

ネットワーク セキュリティ グループのフロー ログと Elastic Stack とを組み合わせることで、ネットワーク トラフィックを視覚化する強力でカスタマイズ可能な方法が実現します。 これらのダッシュボードによって、ネットワーク トラフィックに関する有用な情報を素早く取得して共有したり、潜在的な異常をフィルターして調査したりできます。 Kibana を使用して、これらのダッシュボードをカスタマイズし、セキュリティ、監査、およびコンプライアンスのニーズを満たすための特別な視覚化されたデータを作成できます。

## <a name="next-steps"></a>次のステップ

[Power BI による NSG フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-power-bi.md)に関するページから、Power BI で NSG フロー ログを視覚化する方法について確認する

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png