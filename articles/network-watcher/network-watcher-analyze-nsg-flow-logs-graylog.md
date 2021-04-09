---
title: Azure ネットワーク セキュリティ グループ フロー ログの分析 - Graylog | Microsoft Docs
description: Network Watcher と Graylog を使用して、Azure のネットワーク セキュリティ グループ フロー ログを管理および分析する方法について説明します。
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965546"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Network Watcher と Graylog を使用した Azure のネットワーク セキュリティ グループ フロー ログの管理と分析

[ネットワーク セキュリティ グループ フロー ログ](network-watcher-nsg-flow-logging-overview.md)の情報を使用して、Azure ネットワーク インターフェイスのイングレスおよびエグレス IP トラフィックを把握できます。 フロー ログには、ネットワーク セキュリティ グループの規則ごとの送信および受信フロー、フローが適用されているネットワーク インターフェイス、フローに関する 5 組の情報 (送信元/送信先 IP、送信元/送信先ポート、プロトコル)、トラフィックが許可されたか拒否されたかが示されます。

ネットワークの複数のネットワーク セキュリティ グループでフロー ログを有効にすることができます。 複数のネットワーク セキュリティ グループでフロー ログを有効にすると、ログを解析し、ログから洞察を得ることが難しくなる可能性があります。 この記事では、Graylog (オープン ソースのログ管理および分析ツール) と Logstash (オープン ソースのサーバー側データ処理パイプライン) を使用して、これらのネットワーク セキュリティ グループ フロー ログを一元的に管理するソリューションについて説明します。

> [!Warning]
> 次の手順は、フロー ログのバージョン 1 に使用できます。 詳細については、「[ネットワーク セキュリティ グループのフローのログ記録の概要](network-watcher-nsg-flow-logging-overview.md)」を参照してください。 次の手順は、変更しなければ、ログ ファイルのバージョン 2 で使用できません。

## <a name="scenario"></a>シナリオ

Network Watcher を使用して、ネットワーク セキュリティ グループ フロー ログを有効にします。 フロー ログは Azure Blob Storage に送信されます。 Logstash プラグインを使用して、Blob Storage のフロー ログに接続して処理し、Graylog に送信します。 フロー ログが Graylog に保存されたら、ログを分析し、カスタマイズされたダッシュボードで視覚化できます。

![Graylog のワークフロー](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>インストール手順

### <a name="enable-network-security-group-flow-logging"></a>ネットワーク セキュリティ グループ フロー ログを有効にする

このシナリオでは、アカウント内の少なくとも 1 つのネットワーク セキュリティ グループで、ネットワーク セキュリティ グループ フロー ログが有効になっている必要があります。 ネットワーク セキュリティ グループ フロー ログを有効にする手順については、「[ネットワーク セキュリティ グループのフローのログ記録の概要](network-watcher-nsg-flow-logging-overview.md)」をご覧ください。

### <a name="setting-up-graylog"></a>Graylog をセットアップする

この例では、Graylog と Logstash はどちらも Azure にデプロイされた Ubuntu 14.04 Server 上で構成されます。

- Ubuntu にインストールする手順については、Graylog の[ドキュメント](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html)を参照してください。
- [こちら](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)のドキュメントに従って、Graylog Web インターフェイスも構成する必要があります。

この例では、Graylog の最小限のセットアップ ( Graylog の単一のインスタンス) を使用しますが、システムと運用のニーズに応じて、複数のリソースにスケールするように構築できます。 アーキテクチャに関する考慮事項や詳細なアーキテクチャ ガイドについては、Graylog の[ドキュメント](https://docs.graylog.org/en/2.2/pages/architecture.html)と[アーキテクチャ ガイド](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)を参照してください。

Graylog は、プラットフォームと基本設定に応じて、さまざまな方法でインストールできます。 使用可能なインストール方法の一覧については、Graylog の公式[ドキュメント](https://docs.graylog.org/en/2.2/pages/installation.html)を参照してください。 Graylog サーバー アプリケーションは、Linux ディストリビューションで実行されます。前提条件は次のとおりです。

-  Java SE 8 以降 - [Azul Azure JDK のドキュメント](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Elastic Search 2.x (2.1.0 以降) - [Elasticsearch のインストール ドキュメント](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 移行 - [MongoDB のインストール ドキュメント](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash のインストール

Logstash を使用して、JSON 形式のフロー ログをフロー タプル レベルにフラット化します。 フロー ログをフラット化すると、ログの整理と Graylog での検索が容易になります。

1. Logstash をインストールするには、次のコマンドを実行します。

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. フロー ログを解析し、Graylog に送信するように Logstash を構成します。 logstash.conf ファイルを作成します。

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. 次のコンテンツをファイルに追加します。 強調表示された値を変更して、ストレージ アカウントの詳細を反映させます。

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   用意されている Logstash 構成ファイルは、input、filter、output の 3 つの部分で構成されます。 input セクションでは、Logstash が処理するログの入力ソースを指定します。この例では、(次の手順でインストールする) Azure BLOB 入力プラグインを使用します。このプラグインにより、Blob Storage に格納されているネットワーク セキュリティ グループ フロー ログ JSON ファイルにアクセスできるようになります。

filter セクションは、個々のフロー タプルとそれに関連付けられたプロパティが別の Logstash イベントになるように、各フロー ログ ファイルをフラット化します。

最後に、output セクションは、各 Logstash イベントを Graylog サーバーに転送します。 特定のニーズに合わせるために、Logstash 構成ファイルは適宜変更を加えてください。

   > [!NOTE]
   > 前の構成ファイルでは、Graylog サーバーがローカル ホスト ループバック IP アドレス 127.0.0.1 に 構成されているものと想定しています。 そうでない場合は、出力セクションのホスト パラメーターを正しい IP アドレスに変更してください。

Logstash のインストールの詳しい手順については、Logstash の[ドキュメント](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)を参照してください。

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Azure Blob Storage 向けの Logstash 入力プラグインをインストールする

Logstash プラグインを使用すると、指定された BLOB ストレージ アカウントからフロー ログに直接アクセスできます。 プラグインをインストールするには、既定の Logstash インストール ディレクトリ (この例では /usr/share/logstash/bin) から次のコマンドを実行します。

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

このプラグインの詳細については、[こちら](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)のドキュメントを参照してください。

### <a name="set-up-connection-from-logstash-to-graylog"></a>Logstash から Graylog への接続を設定する

Logstash を使用してフロー ログへの接続を確立し、Graylog サーバーを設定しました。次に、受信ログ ファイルを受け入れるように Graylog を構成する必要があります。

1. 構成済みの URL を使用して、Graylog サーバーの Web インターフェイスに移動します。 インターフェイスにアクセスするには、ブラウザーで `http://<graylog-server-ip>:9000/` を参照します。

2. 構成ページに移動するには、上部のナビゲーション バーの右にある **[System]\(システム\)** ドロップダウン メニューをクリックし、 **[Inputs]\(入力\)** をクリックします。
   または、`http://<graylog-server-ip>:9000/system/inputs` に移動します。

   ![作業の開始](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. 新しい入力を開始するには、 **[Select input]\(入力の選択\)** ドロップダウンで *[GELF UDP]* を選択し、フォームに入力します。 GELF は Graylog Extended Log Format の略です。 GELF 形式は Graylog によって開発されました。 この形式の利点の詳細については、Graylog の[ドキュメント](https://docs.graylog.org/en/2.2/pages/gelf.html)を参照してください。

   Graylog サーバーが構成されている IP に入力をバインドする必要があります。 IP アドレスは、Logstash 構成ファイルの UDP 出力の **host** フィールドと一致する必要があります。 既定のポートは *12201* です。 ポートが、Logstash 構成ファイルで指定されている UDP 出力の **port** フィールドと一致することを確認します。

   ![スクリーンショットには、Graylog の入力と、入力を起動して検索するオプションが示されています。](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   入力を開始すると、次の図に示すように、 **[Local inputs]\(ローカル入力\)** セクションに表示されます。

   ![開始した入力を含むローカル入力セクションを示すスクリーンショット。](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Graylog のメッセージ入力の詳細については、[こちら](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)のドキュメントを参照してください。

4. これらの構成を行ったら、`sudo systemctl start logstash.service` コマンドを使用して、Logstash を起動し、フロー ログの読み取りを開始できます。

### <a name="search-through-graylog-messages"></a>Graylog のメッセージを検索する

Graylog サーバーがメッセージを収集するまでしばらく待ってからメッセージを検索できます。 Graylog サーバーに送信されるメッセージを確認するには、 **[Inputs]\(入力\)** 構成ページで、作成済みの GELF UDP 入力の **[Show received messages]\(受信メッセージの表示\)** ボタンをクリックします。 次の図のような画面が表示されます。 

![スクリーンショットには、検索結果、ヒストグラム、およびメッセージが表示された Graylog サーバーが示されています。](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

青色の "%{Message}" リンクをクリックすると、各メッセージが展開され、次の図に示すように、各フロー タプルのパラメーターが表示されます。

![スクリーンショットには、Graylog サーバーからのメッセージの詳細が示されています。](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

検索対象となる特定のメッセージ フィールドを選択していない場合、既定ではすべてのメッセージ フィールドが検索に含まれます。 特定のメッセージ ( 特定のソース IP のフロー タプル) を検索する場合は、[こちら](https://docs.graylog.org/en/2.2/pages/queries.html)に記載されている Graylog の検索クエリ言語をご利用いただけます。

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Graylog を使用したネットワーク セキュリティ グループ フロー ログの分析

Graylog の設定が完了したので、その機能を使用してフロー ログ データの理解を深めることができます。 1 つの方法として、ダッシュボードを使用してデータの特定のビューを作成します。

### <a name="create-a-dashboard"></a>ダッシュボードを作成する

1. 上部のナビゲーション バーで、 **[Dashboards]\(ダッシュボード\)** を選択するか、`http://<graylog-server-ip>:9000/dashboards/` に移動します。

2. そこから、緑色の **[Create dashboard]\(ダッシュボードの作成\)** ボタンをクリックし、簡単なフォームにダッシュボードのタイトルと説明を入力します。 **[Save]\(保存\)** をクリックして、新しいダッシュボードを作成します。 次の図のようなダッシュボードが表示されます。

    ![スクリーンショットには、ダッシュボードを作成および編集するオプションと共に、Graylog サーバーのダッシュボードが示されています。](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>ウィジェットを追加する

ダッシュボードのタイトルをクリックすると、ダッシュボードが表示されますが、ウィジェットを追加していないので現時点では空です。 ダッシュボードに追加する使いやすい便利なウィジェットとして、**Quick Values** グラフがあります。このグラフには、選択したフィールドの値の一覧と値分布が表示されます。

1. 上部のナビゲーション バーの **[Search]\(検索\)** をクリックして、フロー ログを受信している UDP 入力の検索結果に戻ります。

2. 画面の左側の **[Search result]\(検索結果\)** パネルで、 **[Fields]\(フィールド\)** タブを見つけます。このタブには、受信フロー タプルの各メッセージのさまざまなフィールドが表示されます。

3. 視覚化する目的のパラメーターを選択します (この例では、IP ソースを選択しています)。 使用可能なウィジェットの一覧を表示するには、フィールドの左側の青色のドロップダウン矢印をクリックします。次に、 **[Quick values]** を選択してウィジェットを生成します。 次の図のような画面が表示されます。

   ![発信元 IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. ウィジェットの右上隅にある **[Add to dashboard]\(ダッシュボードに追加\)** をクリックし、対応するダッシュボードを選択して追加します。

5. ダッシュボードに戻ると、追加したウィジェットが表示されます。

   次の図に示すサンプル ダッシュボードのように、ヒストグラムやカウントなどの他のさまざまなウィジェットをダッシュボードに追加して、重要なメトリックを追跡できます。

   ![フロー ログ ダッシュボード](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    ダッシュボードと他の種類のウィジェットの詳細については、Graylog の[ドキュメント](https://docs.graylog.org/en/2.2/pages/dashboards.html)を参照してください。

Network Watcher を Graylog と統合することで、ネットワーク セキュリティ グループ フロー ログを簡単かつ一元的に管理し、視覚化できるようになりました。 Graylog には、ストリームやアラートなどの強力な機能が多数用意されています。これらの機能を使用してフロー ログを詳細に管理し、ネットワーク トラフィックの理解を深めることができます。 Graylog をセットアップし、Azure に接続したので、提供される他の機能を引き続き自由に探索してください。

## <a name="next-steps"></a>次のステップ

[Power BI を使用したネットワーク セキュリティ グループ フロー ログの視覚化](network-watcher-visualize-nsg-flow-logs-power-bi.md)に関する記事を参照して、Power BI でネットワーク セキュリティ グループ フロー ログを視覚化する方法を確認します。