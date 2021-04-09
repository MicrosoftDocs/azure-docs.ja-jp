---
title: Logstash を通じてデータ ソースを Azure Sentinel に接続する | Microsoft Docs
description: Logstash を使用して外部データ ソースから Azure Sentinel にログを転送する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: da7d540a4b7982c7f743a7ae968515485b45aa5a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035429"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Logstash を使用して Azure Sentinel にデータ ソースを接続する

> [!IMPORTANT]
> Logstash 出力プラグインを使用したデータ インジェストは、現在パブリック プレビューの段階にあります。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

**Logstash データ収集エンジン** 用の Azure Sentinel の出力プラグインを使用することで、Logstash 経由で任意の種類のログを Azure Sentinel の Log Analytics ワークスペースに直接送信できます。 ログは、出力プラグインを使用して定義するカスタム テーブルに送信されます。

Logstash データ収集エンジンの操作の詳細については、[Logstash の概要](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)に関するページをご覧ください。

## <a name="overview"></a>概要

### <a name="architecture-and-background"></a>アーキテクチャと背景

![ログの一時退避アーキテクチャの図。](./media/connect-logstash/logstash-architecture.png)

Logstash エンジンは、次の 3 つのコンポーネントで構成されています。

- 入力プラグイン:さまざまなソースのデータのカスタマイズされたコレクション。
- フィルター プラグイン:指定した条件に従ったデータの操作と正規化。
- 出力プラグイン:収集および処理されたデータのさまざまな宛先へのカスタマイズされた送信。

> [!NOTE]
> Azure Sentinel サポートは用意された独自の出力プラグインのみをサポートしています。 Azure Sentinel 用のサード パーティ製出力プラグインや、あらゆる種類のその他の Logstash プラグインはサポートされていません。

Logstash 用の Azure Sentinel 出力プラグインは、Log Analytics HTTP データ コレクター REST API を使用して、JSON 形式のデータを Log Analytics ワークスペースに送信します。 データはカスタム ログに取り込まれます。

- [Log Analytics REST API ](/rest/api/loganalytics/create-request)の詳細を確認します。
- [カスタム ログ](../azure-monitor/agents/data-sources-custom-logs.md)の詳細を確認します。

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Logstash での Azure Sentinel 出力プラグインのデプロイ

### <a name="step-1-installation"></a>手順 1:インストール

Azure Sentinel 出力プラグインは、Logstash コレクションで使用できます。

- Logstash の [プラグインの操作](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html)に関するドキュメントに記載されている手順に従って、***[microsoft-logstash-output-azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)*** プラグインをインストールします。
   
- Logstash システムがインターネットにアクセスできない場合は、Logstash の[オフライン プラグイン管理](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html)に関するドキュメントに記載されている手順に従って、オフライン プラグイン パックを準備して使用します。 (この場合、インターネットにアクセスできる別の Logstash システムを構築する必要があります)。

### <a name="step-2-configuration"></a>手順 2:構成

Logstash の[構成ファイルの構造](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html)に関するドキュメントの情報を使用して、次のキーと値を使用して Azure Sentinel 出力プラグインを構成に追加します。 (適切な構成ファイルの構文は表の後に示しています)。

| フィールド名 | データ型 | 説明 |
|----------------|---------------|-----------------|
| `workspace_id` | string | ワークスペース ID GUID を入力します。 * |
| `workspace_key` | string | ワークスペースの主キー GUID を入力します。 * |
| `custom_log_table_name` | string | ログが取り込まれるテーブルの名前を設定します。 構成できるのは、出力プラグインごとに 1 つのテーブル名だけです。 ログ テーブルは、Azure Sentinel の **[ログ]** の下の **[カスタム ログ]** カテゴリの **[テーブル]** に `_CL` サフィックス付きで表示されます。 |
| `endpoint` | string | 省略可能なフィールド。 既定では、これは Log Analytics エンドポイントです。 代替エンドポイントを設定するには、このフィールドを使用します。 |
| `time_generated_field` | string | 省略可能なフィールド。 このプロパティは Log Analytics の既定の **TimeGenerated** フィールドよりも優先されます。 データ ソースのタイムスタンプ フィールドの名前を入力します。 フィールドのデータは、ISO 8601 形式 (`YYYY-MM-DDThh:mm:ssZ`) に準拠している必要があります |
| `key_names` | array | Log Analytics 出力スキーマ フィールドの一覧を入力します。 各リスト項目は、単一引用符で囲み、項目をコンマで区切り、リスト全体を角かっこで囲む必要があります。 以下の例を参照してください。 |
| `plugin_flush_interval` | 数値 | 省略可能なフィールド。 Log Analytics へのメッセージの転送間の最大間隔 (秒) を定義するように設定します。 既定値は 5 です。 |
    | `amount_resizing` | boolean | true または false 自動スケーリング メカニズムを有効または無効にします。これにより、受信したログ データの量に応じてメッセージ バッファー サイズが調整されます。 |
| `max_items` | 数値 | 省略可能なフィールド。 `amount_resizing` が "false" に設定されている場合にのみ適用されます。 メッセージ バッファー サイズ (レコード単位) の上限の設定に使用します。 既定値は 2000 です。  |
| `azure_resource_id` | string | 省略可能なフィールド。 データが存在する Azure リソースの ID を定義します。 <br>リソース ID 値は特に、[リソースコンテキスト RBAC](resource-context-rbac.md) を使用して特定のデータへのアクセスのみを許可する場合に便利です。 |
| | | |

*  ワークスペース ID と主キーは、ワークスペース リソースの **[エージェント管理]** で確認できます。

#### <a name="sample-configurations"></a>サンプルの構成

いくつかの異なるオプションを使用するいくつかのサンプル構成を次に示します。

- filebeat 入力パイプを使用する基本構成:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- tcp 入力パイプを使用する基本構成:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- 高度な構成:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > 内部の動作、構成、およびパフォーマンス設定の詳細については、出力プラグインの [GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)をご覧ください。

### <a name="step-3-restart-logstash"></a>手順 3:Logstash を再起動する

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>手順 4:Azure Sentinel で受信ログを表示する

1. メッセージが出力プラグインに送信されていることを確認します。

1. Azure Sentinel ナビゲーション メニューから **[ログ]** をクリックします。 **[テーブル]** 見出しの下にある **[カスタム ログ]** カテゴリを展開します。 構成で (`_CL` サフィックスで) 指定したテーブルの名前を見つけてクリックします。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="ログの一時退避カスタム ログのスクリーンショット。":::

1. テーブルのレコードを表示するには、テーブル名をスキーマとして使用して、テーブルに対してクエリを実行します。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="ログの一時退避カスタム ログ クエリのスクリーンショット。":::

## <a name="monitor-output-plugin-audit-logs"></a>出力プラグインの監査ログを監視する

Azure Sentinel 出力プラグインの接続とアクティビティを監視するには、適切な Logstash ログ ファイルを有効にします。 ログ ファイルの場所については、[Logstash ディレクトリ レイアウト](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout)に関するドキュメントをご覧ください。

このログ ファイルにデータが表示されない場合は、いくつかのイベントをローカルで生成して送信し (入力プラグインとフィルター プラグインを使用)、出力プラグインがデータを受信していることを確認します。 Azure Sentinel では、出力プラグインに関連する問題のみがサポートされます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Logstash を使用して外部データ ソースを Azure Sentinel に接続する方法について説明します。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [組み込み](tutorial-detect-threats-built-in.md)または[カスタム](tutorial-detect-threats-custom.md)のルールを使用して、Azure Sentinel で脅威の検出を開始する。
