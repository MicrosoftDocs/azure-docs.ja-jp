---
title: オープン ソース ツールを使用してネットワーク不正侵入検出を実行する
titleSuffix: Azure Network Watcher
description: この記事では、Azure Network Watcher とオープン ソース ツールを使用してネットワーク不正侵入検出を実行する方法について説明します
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b4b38112e32a55739ea14d5ff06e327819171557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014554"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Network Watcher とオープン ソース ツールを使用したネットワーク不正侵入検出の実行

パケット キャプチャは、ネットワークの侵入検出システム (IDS) を実装し、ネットワーク セキュリティ監視 (NSM) を実行するうえで重要なコンポーネントです。 パケット キャプチャを処理し、ネットワークへの侵入と悪意のあるアクティビティの可能性を示すシグネチャを検出するオープン ソース IDS ツールは複数公開されています。 Network Watcher で取得したパケット キャプチャを使用して、害のある侵入または脆弱性に関してネットワークを分析できます。

そのようなオープン ソース ツールの 1 つに Suricata があります。これは、ルール セットを使用してネットワーク トラフィックを監視し、疑わしいイベントが発生したときにアラートをトリガーする IDS エンジンです。 Suricata にはマルチスレッド エンジンがあるため、高速かつ高効率なネットワーク トラフィック分析を実行することができます。 Suricata とその機能の詳細については、Suricata の Web サイト (https://suricata-ids.org/ ) を参照してください。

## <a name="scenario"></a>シナリオ

この記事では、Network Watcher、Suricata、Elastic Stack を使用して、ネットワーク不正侵入検出を実行する環境をセットアップする方法について説明します。 Network Watcher を使用して、ネットワーク不正侵入検出を実行するために使用するパケット キャプチャを取得します。 Suricata でパケット キャプチャを処理し、指定の脅威のルール セットに一致するパケットに基づいて、アラートをトリガーします。 これらのアラートは、ローカル コンピューターのログ ファイルに格納されます。 Elastic Stack を使用すると、Suricata によって生成されたログにインデックスを作成し、ログを使用して Kibana ダッシュボードを作成できます。これにより、ログを視覚化し、ネットワークの潜在的な脆弱性に関する洞察を迅速に得ることができます。  

![単純な Web アプリケーションのシナリオ][1]

どちらのオープン ソース ツールも Azure VM で設定できるので、この分析は独自の Azure ネットワーク環境内で実行できます。

## <a name="steps"></a>手順

### <a name="install-suricata"></a>Suricata のインストール

その他すべてのインストール方法については、 https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation を参照してください。

1. VM のコマンドライン ターミナルで、次のコマンドを実行します。

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. インストールを確認するには、コマンド `suricata -h` を実行して、コマンドの完全な一覧を表示します。

### <a name="download-the-emerging-threats-ruleset"></a>Emerging Threats ルール セットのダウンロード

この段階では、Suricata で実行するルールが 1 つもありません。 検出したい特定の脅威がネットワークにある場合、独自のルールを作成できます。または、Emerging Threats や Snort の VRT ルールなど、さまざまなプロバイダーが開発したルール セットを使用することもできます。 ここでは、無料で公開されている Emerging Threats ルール セットを使用します。

ルール セットをダウンロードして、ディレクトリにコピーします。

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Suricata によるパケット キャプチャの処理

Suricata を使用してパケット キャプチャを処理するには、次のコマンドを実行します。

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
生成されるアラートをチェックするには、fast.log ファイルを確認します。
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Elastic Stack の設定

Suricata によって作成されたログには、ネットワークで発生したイベントに関する貴重な情報が含まれていますが、これらのログ ファイルを読んで理解するのは簡単ではありません。 Suricata を Elastic Stack に接続すると、Kibana ダッシュボードを作成して、ログから有用な情報を検索、グラフ化、分析、取得できます。

#### <a name="install-elasticsearch"></a>Elasticsearch のインストール

1. Elastic Stack のバージョン 5.0 以降では、Java 8 が必要です。 `java -version` コマンドを実行して、現在のバージョンを確認します。 Java がインストールされていない場合は、[Azure でサポートされている JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) に関するドキュメントを参照してください。

1. お使いのシステムに適合するバイナリ パッケージをダウンロードします。

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    他のインストール方法については[Elasticsearch のインストール](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)のページを参照してください。

1. 次のコマンドを使用して、Elasticsearch が実行されていることを確認します。

    ```
    curl http://127.0.0.1:9200
    ```

    次のような応答画面が表示されます。

    ```
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

Elasticsearch のインストール方法の詳細については、[インストール](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)のページを参照してください。

### <a name="install-logstash"></a>Logstash のインストール

1. Logstash をインストールするには、次のコマンドを実行します。

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. 次に、eve.json ファイルの出力を読み取るように Logstash を構成する必要があります。 以下のコマンドで logstash.conf ファイルを作成します。

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. 次の内容をファイルに追加します (eve.json ファイルへのパスが正しいことを確認してください)。

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Logstash がファイルを取り込めるように、必ず適切なアクセス許可を eve.json ファイルに付与します。
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Logstash を開始するには、次のコマンドを実行します。

    ```
    sudo /etc/init.d/logstash start
    ```

Logstash のインストール方法の詳細については、[公式ドキュメント](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)を参照してください。

### <a name="install-kibana"></a>Kibana のインストール

1. Kibana をインストールするには、次のコマンドを実行します。

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Kibana を実行するには、次のコマンドを使用します。

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Kibana Web インターフェイスを表示するには、`http://localhost:5601` に移動します。
1. ここでは、Suricata ログで使用されているインデックスのパターンは "logstash-*" です。

1. Kibana ダッシュボードをリモートで確認する場合は、NSG 受信ルールを作成して、**ポート 5601** にアクセスできるようにします。

### <a name="create-a-kibana-dashboard"></a>Kibana ダッシュボードの作成

この記事では、アラートのトレンドと詳細を確認するためのサンプル ダッシュボードが提供されています。

1. [ダッシュボード ファイル](https://aka.ms/networkwatchersuricatadashboard)、[視覚化ファイル](https://aka.ms/networkwatchersuricatavisualization)、[保存された検索ファイル](https://aka.ms/networkwatchersuricatasavedsearch)をそれぞれダウンロードします。

1. Kibana の **[Management (管理)]** タブの下で **[Saved Objects (保存されたオブジェクト)]** に移動して、3 つのファイルすべてをインポートします。 これで、 **[Dashboard (ダッシュボード)]** タブからサンプル ダッシュボードを開いて読み込むことができます。

関心のあるメトリックに合わせて独自の視覚化とダッシュボードを作成することもできます。 Kibana の視覚化を作成する方法の詳細については、Kibana の[公式ドキュメント](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm)を参照してください。

![Kibana ダッシュボード][2]

### <a name="visualize-ids-alert-logs"></a>IDS アラート ログの視覚化

サンプル ダッシュボードでは、Suricata アラート ログを複数の方法で視覚化できます。

1. GeoIP によるアラート – (IP で特定された) 地理的な場所に基づいて、発信元の国または地域別でアラートの分布を示すマップ

    ![GeoIP][3]

1. 上位 10 個のアラート – 最も頻繁にトリガーされた 10 個のアラートの概要と説明。 個別のアラートをクリックすると、ダッシュボードにフィルターを適用して特定のアラートに関する情報のみを表示できます。

    ![画像 4][4]

1. アラートの数 – ルール セットによってトリガーされたアラートの総数。

    ![画像 5][5]

1. 上位 20 個の送信元/送信先 IP/ポート – アラートがトリガーされた上位 20 個の IP とポートを示す円グラフ。 特定の IP/ポートでフィルターを適用して、トリガーされているアラートの数と種類を表示できます。

    ![画像 6][6]

1. アラートの概要 – アラートごとの詳細な情報を要約した表。 この表をカスタマイズして、アラートごとに関心のある他のパラメーターが表示されるようにできます。

    ![画像 7][7]

カスタム視覚化とカスタム ダッシュボードの作成の詳細については、[Kibana の公式ドキュメント](https://www.elastic.co/guide/en/kibana/current/introduction.html)を参照してください。

## <a name="conclusion"></a>まとめ

Network Watcher で取得したパケット キャプチャと、Suricata などのオープン ソース IDS ツールを組み合わせることで、広範な脅威を対象としたネットワーク不正侵入検出を実行できます。 これらのダッシュボードでは、ネットワーク内の傾向と異常を迅速に特定できます。また、データを詳しく調査して、悪意のあるユーザー エージェントや脆弱なポートなど、アラートの根本原因を突き止めることができます。 抽出したこのデータを活用すると、害のある侵入の試行からネットワークを保護する方法について、情報に基づいた意思決定を行えるほか、ネットワークへの今後の侵入を防ぐルールを作成できます。

## <a name="next-steps"></a>次のステップ

アラート ベースでパケット キャプチャをトリガーする方法については、「[パケット キャプチャを使用して Azure Functions によるプロアクティブなネットワーク監視を実行する](network-watcher-alert-triggered-packet-capture.md)」を参照してください。

[Power BI による NSG フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-power-bi.md)に関するページから、Power BI で NSG フロー ログを視覚化する方法について確認する



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png