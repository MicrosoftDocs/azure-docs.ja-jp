---
title: IoT Hub から Azure Data Explorer にデータを取り込む
description: この記事では、IoT Hub から Azure Data Explorer にデータを取り込む (読み込む) 方法を学習します。
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: cbe9aa2ea664d97df6008de05d6cb84da9771bcc
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166444"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer-preview"></a>IoT Hub から Azure Data Explorer にデータを取り込む (プレビュー)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer では、IoT Hub からの取り込み (データの読み込み) が可能で、ビッグ データのストリーミング プラットフォーム、IoT の取り込みサービスがあります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

* *testdb* というデータベース名の[テスト クラスターとデータベース](create-cluster-database-portal.md)を作成します。

* デバイスをシミュレートする[サンプル アプリ](https://github.com/Azure-Samples/azure-iot-samples-csharp)とドキュメントです。

* サンプル アプリを実行するための [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

## <a name="create-an-iot-hub"></a>IoT ハブを作成する

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>IoT Hub にデバイスを登録する

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure データ エクスプローラーでターゲット テーブルを作成する

次に、IoT Hub からデータを送信するテーブルを Azure Data Explorer に作成します。 「[**前提条件**](#prerequisites)」でプロビジョニングしたクラスターとデータベースにテーブルを作成します。

1. Azure portal でクラスターに移動し、 **[クエリ]** を選択します。

    ![ポータルでの ADX クエリ](media/ingest-data-iot-hub/adx-initiate-query.png)

1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、取り込んだデータを受け取るテーブル (TestTable) を作成します。

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![クエリの作成の実行](media/ingest-data-iot-hub/run-create-query.png)

1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、テーブル (TestTable) の列名とデータ型に受信 JSON データをマップします。

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Azure Data Explorer テーブルを IoT ハブに接続する

ここで、Azure Data Explorer から IoT Hub に接続します。 この接続が完了すると、IoT ハブに送信されるデータは、[作成したターゲット テーブル](#create-a-target-table-in-azure-data-explorer)にストリーム配信されます。

1. ツールバーの **[通知]** を選択して、IoT Hub のデプロイが成功したことを確認します。

1. 作成したクラスターで、 **[データベース]** を選択し、**testdb** を作成したデータベースを選択します。
    
    ![テスト データベースの選択](media/ingest-data-iot-hub/select-database.png)

1. **[データ インジェスト]** 、 **[データ接続の追加]** の順に選択します。 その後、フォームに次の情報を入力します。 完了したら、 **[作成]** を選択します。

    ![IoT Hub 接続](media/ingest-data-iot-hub/iot-hub-connection.png)

    **データ ソース**:

    **設定** | **フィールドの説明**
    |---|---|
    | データ接続名 | Azure Data Explorer で作成する接続の名前
    | IoT Hub | IoT Hub 名 |
    | 共有アクセス ポリシー | 共有アクセス ポリシーの名前。 読み取りアクセス許可が必要 |
    | コンシューマー グループ |  IoT Hub の組み込みのエンドポイントに定義されているコンシューマー グループ |
    | イベント システム プロパティ | IoT Hub イベントのシステム プロパティ |
    | | 

    > [!NOTE]
    > [手動フェールオーバー](/azure/iot-hub/iot-hub-ha-dr#manual-failover)が発生した場合は、データ接続を再作成する必要があります。

    **ターゲット テーブル**:

    挿入したデータをルーティングするには、"*静的*" と "*動的*" という 2 つのオプションがあります。 
    この記事では、静的ルーティングを使用し、テーブル名、データ形式、およびマッピングを指定します。 そのため、 **[My data includes routing info]\(データにルーティング情報が含まれている\)** はオフのままにしておきます。

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | テーブル | *TestTable* | **testdb** に作成したテーブル。 |
    | データ形式 | *JSON* | サポートされている形式は、Avro、CSV、JSON、MULTILINE JSON、PSV、SOH、SCSV、TSV、および TXT です。 |
    | 列マッピング | *TestMapping* | **testdb** に作成したマッピング。受信 JSON データを **testdb** の列名とデータ型にマッピングします。 JSON、MULTILINE JSON、AVRO では必須。その他の形式では省略可能。|
    | | |

    > [!TIP]
    > 動的ルーティングを使用するには、 **[My data includes routing info]\(データにルーティング情報が含まれている\)** を選択します。この場合、[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)のコメントに示されているように、データに必要なルーティング情報が含まれています。 静的プロパティと動的プロパティの両方が設定されている場合、静的プロパティは動的プロパティによってオーバーライドされます。 

## <a name="generate-sample-data-for-testing"></a>テスト用のサンプル データを生成する

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度と湿度の利用統計情報を送信します。

1. [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip ) からサンプル C# プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

1. ローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\simulated-device** フォルダーに移動します。

1. 適当なテキスト エディターで **SimulatedDevice.cs** ファイルを開きます。

    `s_connectionString` 変数の値を、「[IoT Hub にデバイスを登録する](#register-a-device-to-the-iot-hub)」のデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.cs** ファイルに保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

1. ローカル ターミナル ウィンドウで次のコマンドを実行し、シミュレートされたデバイス アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>データ フローの確認

データを生成するアプリで、IoT ハブからお使いのクラスター内のテーブルにそのデータが送信されるのを確認できるようになりました。

1. アプリの実行中に Azure portal でお使いの IoT ハブを確認すると、アクティビティの急上昇が見られます。

    ![IoT Hub メトリック](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. これまでにデータベースに届いたメッセージ数を確認するには、テスト データベースで次のクエリを実行します。

    ```Kusto
    TestTable
    | count
    ```

1. メッセージの内容を表示するには、次のクエリを実行します。

    ```Kusto
    TestTable
    ```

    結果セット:
    
    ![取り込まれたデータの結果の表示](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer には、インジェスト プロセスを最適化することを目的とした、データ インジェストの集計 (バッチ処理) ポリシーがあります。 既定では、このポリシーは 5 分または 500 MB のデータに構成されているため、待ち時間が生じることがあります。 集計オプションについては、[バッチ処理のポリシー](/azure/kusto/concepts/batchingpolicy)に関するページを参照してください。 
    > * ストリーミングをサポートし、応答時間でのラグを削除するようにテーブルを構成します。 [ストリーミング ポリシー](/azure/kusto/concepts/streamingingestionpolicy)に関するページを参照してください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

お使いの IoT Hub を今後再利用する予定がない場合は、コストが発生しないように **test-hub-rg** をクリーンアップします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。  

    左側のメニューが折りたたまれている場合は、 ![展開ボタン](media/ingest-data-event-hub/expand.png) をクリックして展開します。

   ![削除するリソース グループの選択](media/ingest-data-event-hub/delete-resources-select.png)

1. **test-resource-group** で **[リソース グループの削除]** を選択します。

1. 新しいウィンドウで、削除するリソース グループの名前 (*test-hub-rg*) を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
