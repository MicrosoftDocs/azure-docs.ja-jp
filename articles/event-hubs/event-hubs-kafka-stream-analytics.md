---
title: Stream Analytics を使用して Apache Kafka イベントを処理する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Stream Analytics を使用してイベント ハブを介して取り込まれた Kafka イベントを処理する方法について説明します。
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: spelluru
ms.openlocfilehash: 0c4beede2508104fc9af934d3f9a2bbcce791292
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626182"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Stream Analytics を使用して Event Hubs イベントの Apache Kafka を処理する 
この記事では、データを Kafka 対応 Event Hubs にストリームし、Azure Stream Analytics で処理する方法について説明します。 次の手順について説明します。 

1. Kafka 対応 Event Hubs 名前空間を作成する。
2. イベント ハブにメッセージを送信する Kafka クライアントを作成する。
3. イベント ハブから AzureBlob ストレージにデータをコピーする Stream Analytics ジョブを作成する。 

イベント ハブで公開されている Kafka エンドポイントを使用する場合は、プロトコル クライアントを変更したり、独自のクラスターを実行したりする必要はありません。 Azure Event Hubs では、[Apache Kafka バージョン 1.0](https://kafka.apache.org/10/documentation.html) がサポートされています。 以降がサポートされています。 


## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を満たしている必要があります。

* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](https://aka.ms/azure-jdks)
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
* [Git](https://www.git-scm.com/)
* **Azure ストレージ アカウント**。 持っていない場合は、次に進む前に[作成します](../storage/common/storage-quickstart-create-account.md)。 このチュートリアルの Stream Analytics ジョブでは、出力データを Azure Blob ストレージに格納します。 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka 対応 Event Hubs 名前空間の作成

1. [Azure portal](https://portal.azure.com) にサインインし、画面の左上にある **[リソースの作成]** をクリックします。
2. **Event Hubs** を検索し、以下に示すオプションを選択します。
    
    ![ポータルでの Event Hubs の検索](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. **[Event Hubs]** ページで **[作成]** を選択します。
4. **[名前空間の作成]** ページで、次の操作を行います。 
    1. 名前空間には一意の**名前**を指定します。 
    2. **[価格レベル]** を選択します。 
    3. **[Kafka を有効にする]** を選択します。 これは**重要な**手順です。 
    4. イベント ハブ名前空間を作成する**サブスクリプション**を選択します。 
    5. 新しい**リソース グループ**を作成するか、既存のリソース グループを選択します。 
    6. **場所**を選択します。 
    7. **Create** をクリックしてください。
    
        ![名前空間の作成](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. **通知メッセージ**で、**リソース グループ名**を選択します。 

    ![名前空間の作成](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. リソース グループ内の**イベント ハブ名前空間**を選択します。 
2. 名前空間が作成されたら、 **[設定]** の **[共有アクセス ポリシー]** を選択します。

    ![[共有アクセス ポリシー] をクリックする](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. 既定値の **RootManageSharedAccessKey** を選択することも、新しいポリシーを追加することもできます。 ポリシー名をクリックして、**接続文字列**をコピーします。 接続文字列を使用して Kafka クライアントを構成します。 
    
    ![ポリシーの選択](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

これで、Kafka プロトコルを使用するアプリケーションからイベントを Event Hubs にストリーミングできます。

## <a name="send-messages-with-kafka-in-event-hubs"></a>Event Hubs で Kafka を使用してメッセージを送信する

1. [Kafka 用の Azure Event Hubs リポジトリ](https://github.com/Azure/azure-event-hubs-for-kafka)をお使いのマシンに複製します。
2. `azure-event-hubs-for-kafka/quickstart/java/producer` フォルダーに移動します。 
4. `src/main/resources/producer.config` でプロデューサーの構成の詳細を更新します。 **イベント ハブの名前空間**に**名前**と**接続文字列**を指定します。 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app` に移動し、**TestDataReporter.java** ファイルを任意のエディターで開きます。 
6. 次のコード行をコメント アウトします。

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. コメント アウトしたコードの代わりに、次のコード行を追加します。 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    このコードはイベント データを **JSON** 形式で送信します。 Stream Analytics ジョブの入力を構成するときは、入力データの形式として JSON を指定します。 
7. **プロデューサーを実行し**、Kafka 対応イベント ハブにストリーム配信します。 Windows マシンでは、**Node.js コマンド プロンプト**を使用するときに、これらのコマンドを実行する前に `azure-event-hubs-for-kafka/quickstart/java/producer` フォルダーに切り替えます。 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>イベント ハブがデータを受信したことを確認する

1. **[エンティティ]** で **[イベント ハブ]** を選択します。 **test** というイベント ハブが表示されていることを確認します。 

    ![イベント ハブ - test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. イベント ハブに送信されたメッセージが表示されることを確認します。 

    ![イベント ハブ - メッセージ](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Stream Analytics ジョブを使用してイベント データを処理する
このセクションでは、Azure Stream Analytics ジョブを作成します。 Kafka クライアントはイベントをイベント ハブに送信します。 イベント データを入力として受け取り、Azure Blob ストレージに出力する Stream Analytics ジョブを作成します。 **Azure ストレージ アカウント**を持っていない場合は、[作成します](../storage/common/storage-quickstart-create-account.md)。

Stream Analytics ジョブのクエリは、分析を実行せずにデータをパス スルーします。 入力データを変換して、異なる形式で、得られた分析情報を使用して出力データを生成するクエリを作成できます。  

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成 

1. [Azure portal](https://portal.azure.com) で、 **[+ リソースの作成]** を選択します。
2. **[Azure Marketplace]** メニューで **[Analytics]** を選択し、 **[Stream Analytics ジョブ]** を選択します。 
3. **[新しい Stream Analytics]** ページで、次の手順を実行します。 
    1. ジョブの**名前**を入力します。 
    2. **サブスクリプション**を選択します。
    3. **リソース グループ**の **[新規作成]** を選択し、名前を入力します。 **既存のリソース グループを使う**こともできます。 
    4. ジョブの**場所**を選択します。
    5. **[作成]** を選択してジョブを作成します。 

        ![新しい Stream Analytics ジョブ](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>ジョブの入力を構成する

1. 通知メッセージで、 **[リソースに移動]** を選択すると、 **[Stream Analytics ジョブ]** ページが表示されます。 
2. 左側のメニューの **[ジョブ トポロジ]** セクションで **[入力]** を選択します。
3. **[ストリーム入力の追加]** を選択して **[イベント ハブ]** を選択します。 

    ![入力としてイベント ハブを追加する](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. **[イベント ハブ] の入力**の構成ページで、次の手順を実行します。 

    1. 入力に**エイリアス**を指定します。 
    2. **Azure サブスクリプション**を選択します。
    3. 以前作成した**イベント ハブの名前空間**を選択します。 
    4. **イベント ハブ**に **test** を選択します。 
    5. **[保存]** を選択します。 

        ![イベント ハブの入力の構成](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>ジョブの出力を構成する 

1. メニューの **[ジョブ トポロジ]** セクションで **[出力]** を選択します。 
2. ツールバーの **[+ 追加]** を選択し、 **[BLOB ストレージ]** を選択します。
3. BLOB のストレージ出力設定ページで、次の手順を実行します。 
    1. 出力に**エイリアス**を指定します。 
    2. Azure **サブスクリプション**を選択します。 
    3. **[Azure ストレージ アカウント]** を選択します。 
    4. Stream Analytics クエリの出力データを格納する**コンテナーの名前**を入力します。
    5. **[保存]** を選択します。

        ![BLOB ストレージの出力の構成](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>クエリを定義する
着信データ ストリームを読み取るように Stream Analytics ジョブを設定したら、次の手順として、データをリアルタイムで分析する変換を作成します。 変換クエリの定義には、[Stream Analytics クエリ言語](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)を使用します。 このチュートリアルでは、変換を実行せずにデータをパス スルーするクエリを定義します。

1. **[クエリ]** を選択します。
2. クエリ ウィンドウで、`[YourOutputAlias]` を前に作成した出力エイリアスで置き換えます。
3. `[YourInputAlias]` を前に作成した入力エイリアスに置き換えます。 
4. ツールバーの **[保存]** を選択します。 

    ![Query](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

1. 左側のメニューで **[概要]** を選択します。 
2. **[開始]** を選択します。 

    ![[スタート] メニュー](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. **[ジョブの開始]** ウィンドウで **[開始]** を選択します。 

    ![[ジョブの開始] ページ](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. ジョブの状態が **[開始中]** から **[実行中]** に変わるまで待ちます。 

    ![ジョブの状態 - 実行中](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>シナリオをテストする
1. **Kafka プロデューサー**を再度実行してイベントをイベントのハブに送信します。 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. **出力データ** が **Azure Blob ストレージ**に生成されていることを確認します。 次のサンプル行のような 100 行のコンテナーに JSON ファイルが表示されます。 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    このシナリオで、Azure Stream Analytics ジョブは、イベント ハブから入力データを受け取り、Azure Blob ストレージに格納しました。 



## <a name="next-steps"></a>次の手順
この記事では、プロトコル クライアントを変更したり、独自のクラスターを実行したりせずに、Kafka 対応 Event Hubs にストリーム配信する方法を紹介しました。 Event Hubs と Kafka 用 Event Hubs の詳細については、次のトピックを参照してください。  

- [Event Hubs について確認する](event-hubs-what-is-event-hubs.md)
- [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka 対応 Event Hubs の作成方法](event-hubs-create-kafka-enabled.md)
- [Kafka アプリケーションから Event Hubs へのストリーム](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka 対応イベント ハブでの Kafka ブローカーのミラーリング](event-hubs-kafka-mirror-maker-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Spark の接続](event-hubs-kafka-spark-tutorial.md)
- [Kafka 対応イベント ハブへの Apache Flink の接続](event-hubs-kafka-flink-tutorial.md)
- [Kafka 対応イベント ハブとの Kafka Connect の統合](event-hubs-kafka-connect-tutorial.md)
- [Kafka 対応イベント ハブへの Akka Streams の接続](event-hubs-kafka-akka-streams-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka) 
