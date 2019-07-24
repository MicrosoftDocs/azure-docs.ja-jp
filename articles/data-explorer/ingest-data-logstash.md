---
title: クイック スタート:Logstash から Azure Data Explorer にデータを取り込む
description: このクイック スタートでは、Logstash から Azure Data Explorer にデータを取り込む (読み込む) 方法について学習します
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 01/14/2019
ms.openlocfilehash: e0d81454e6036d09bb74af6f522063a1aed5fffe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046412"
---
# <a name="quickstart-ingest-data-from-logstash-to-azure-data-explorer"></a>クイック スタート:Logstash から Azure Data Explorer にデータを取り込む

[Logstash](https://www.elastic.co/products/logstash) は、多くのソースからデータを同時に取り込み、変換したデータを任意の "一時退避場所" に送信する、オープン ソースのサーバー側データ処理パイプラインです。 このクイック スタートでは、そのデータを Azure Data Explorer に送信します。これは、ログとテレメトリ データのための高速で非常にスケーラブルなデータ探索サービスです。 最初にテスト クラスターにテーブルとデータのマッピングを作成し、次にデータをテーブルに送信して結果を検証するように Logstash に指示します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure Data Explorer の[テスト クラスターとデータベース](create-cluster-database-portal.md)
* Logstash バージョン 6 以降の[インストール手順](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>テーブルを作成する

クラスターとデータベースが用意できたら、次はテーブルを作成します。

1. データベース クエリ ウィンドウで次のコマンドを実行して、テーブルを作成します。

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. 次のコマンドを実行して、新しいテーブル `logs` が作成されたこと、およびそれが空であることを確認します。
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>マッピングを作成する

マッピングは、受信データをターゲット テーブルのスキーマに変換するために Azure Data Explorer によって使用されます。 次のコマンドでは、`path` で示される受信 json からプロパティを抽出して `column` に出力する、`basicmsg` という名前の新しいマッピングを作成しています。

クエリ ウィンドウで次のコマンドを実行します。

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Logstash 出力プラグインをインストールする

Logstash 出力プラグインは、Azure Data Explorer と通信してデータをサービスに送信します。
プラグインをインストールするには、Logstash のルート ディレクトリ内で次のコマンドを実行します。

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>サンプル データセットを生成するように Logstash を構成する

Logstash では、エンドツーエンドのパイプラインをテストするために使用できるサンプル イベントを生成できます。
既に Logstash を使用していて、ご自分のイベント ストリームにアクセスできる場合は、次のセクションに進んでください。 

> [!NOTE]
> 独自のデータを使用している場合は、前の手順で定義したテーブル オブジェクトとマッピング オブジェクトを変更してください。

1. (vi を使用して) 必要なパイプライン設定が含まれる新しいテキスト ファイルを編集します。

    ```sh
    vi test.conf
    ```

1. 1,000 件のテスト イベントを生成することを Logstash に指示する、次の設定を貼り付けます。

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

この構成には、`stdin` 入力プラグインも含まれています。これを使用すると、自分でメッセージをさらに作成することができます (それらをパイプラインに送信するには、必ず *Enter* キーを使用してください)。

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Azure Data Explorer にデータを送信するように Logstash を構成する

次の設定を、前の手順で使用したのと同じ構成ファイルに貼り付けます。 すべてのプレースホルダーは、お客様の設定に対応する値に置き換えてください。 詳細については、[AAD アプリケーションの作成](/azure/kusto/management/access-control/how-to-provision-aad-app)に関するページを参照してください。 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| パラメーター名 | 説明 |
| --- | --- |
| **path** | Logstash プラグインでは、イベントを Azure Data Explorer に送信する前に、それらを一時ファイルに書き込みます。 このパラメーターには、ファイルを書き込む場所のパスと、Azure Data Explorer サービスへのアップロードをトリガーするためのファイル ローテーションの時間表現が含まれます。|
| **ingest_url** | インジェストに関連する通信の Kusto エンドポイント。|
| **app_id**、**app_key**、および **app_tenant**| Azure Data Explorer に接続するために必要な資格情報。 取り込み特権を備えたアプリケーションを必ず使用してください。 |
| **database**| イベントを配置するデータベースの名前。 |
| **table** | イベントを配置するターゲット テーブルの名前。 |
| **mapping** | マッピングは、受信イベント json 文字列を適切な行形式にマッピングするために使用されます (どのプロパティをどの列に入力するかを定義します)。 |

## <a name="run-logstash"></a>Logstash を実行する

これで、Logstash を実行して設定をテストする準備が整いました。

1. Logstash のルート ディレクトリで、次のコマンドを実行します。

    ```sh
    bin/logstash -f test.conf
    ```

    画面に情報が表示され、サンプル構成によって生成された 1,000 件のメッセージが表示されます。 この時点で、さらにメッセージを手動で入力することもできます。

1. 数分後、次の Data Explorer クエリを実行して、お客様が定義したテーブル内のメッセージを確認します。

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Ctrl + C キーを押して、Logstash を終了します

## <a name="clean-up-resources"></a>リソースのクリーンアップ

お客様のデータベースで次のコマンドを実行して、`logs` テーブルをクリーンアップします。

```Kusto
.drop table logs
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クエリを作成する](write-queries.md)