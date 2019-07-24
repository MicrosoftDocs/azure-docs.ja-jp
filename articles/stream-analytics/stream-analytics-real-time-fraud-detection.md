---
title: Azure Stream Analytics を使用したリアルタイムの不正行為の検出
description: Stream Analytics を使用してリアルタイムの不正行為検出ソリューションを作成する方法について説明します。 リアルタイム イベント処理のためにイベント ハブを使用します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a13d3b24cd7845de144183d9f2ea825e0e24219f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883719"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Azure Stream Analytics の使用:リアルタイムでの不正検出

このチュートリアルでは、Azure Stream Analytics の使い方について詳しく説明します。 学習内容は次のとおりです。 

* Azure Event Hubs のインスタンスにストリーミング イベントを取り込みます。 このチュートリアルでは、携帯電話のメタデータ レコードのストリームをシミュレートするアプリを使います。

* SQL に似た Stream Analytics クエリを記述して、データの変換、情報の集約、パターンの検索を行います。 クエリを使って、受信ストリームを調査し、不正な可能性のある呼び出しを検索する方法がわかります。

* さらに洞察をえるための分析を実行できる出力シンク (ストレージ) に結果を送信します。 ここでは、疑わしい呼び出しデータを Azure BLOB ストレージに送信します。

このチュートリアルでは、電話データに基づくリアルタイムでの不正検出の例を使います。 ここで説明する技法は、クレジット カード詐欺やなりすましなど、他の種類の不正行為検出にも適しています。 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>シナリオ: 通信および SIM におけるリアルタイムでの不正行為の検出

ある通信会社は、膨大な量の着信データを扱っています。 この会社は、不正な呼び出しをリアルタイムに検出し、顧客に通知したり、特定の番号に対してサービスを停止したりすることを考えています。 SIM 不正行為の種類の 1 つとして、地理的に異なる場所からほぼ同時に同じ ID から行われる複数の呼び出しがあります。 この種の不正を検出するには、着信レコードを調べて特定のパターンを見つける必要があります。この場合は、ほぼ同時に異なる複数の国で行われた発信です。 このカテゴリに分類される通話レコードは、後で分析できるようにストレージに書き込まれます。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、サンプル通話メタデータを生成するクライアント アプリを使って通話データをシミュレートします。 アプリが生成するレコードの一部は、不正な呼び出しのように見えます。 

始める前に、以下のものを用意してください。

* Azure アカウント。
* 呼び出しイベント ジェネレーター アプリ [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) (Microsoft ダウンロード センターからダウンロード可能)。 コンピューター上のフォルダーにこのパッケージを解凍します。 ソース コードを確認し、デバッガーでアプリを実行したい場合は、[GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) からアプリのソース コードを入手できます。 

    >[!NOTE]
    >ダウンロードされた zip ファイルが Windows によってブロックされる可能性があります。 ファイルを解凍できない場合は、ファイルを右クリックし、**[プロパティ]** を選びます。 "このファイルは他のコンピューターから取得したものです。このコンピューターを保護するため、このファイルへのアクセスはブロックされる可能性があります" というメッセージが表示されたら、**[ブロック解除]** オプションをオンにして、**[適用]** をクリックします。

Streaming Analytics ジョブの結果を確認する場合は、Azure Blob Storage コンテナーの内容を表示するためのツールも必要です。 Visual Studio を使っている場合は、[Azure Tools for Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) または [Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) を使うことができます。 または、[Azure Storage Explorer](https://storageexplorer.com/) や [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage) などのスタンドアロン ツールをインストールすることもできます。 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>イベントを取り込むための Azure Event Hubs を作成する

データ ストリームを分析するには、Azure に "*取り込む*" 必要があります。 データを取り込む一般的な方法は、[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) を使うことです。Azure Event Hubs は、1 秒間に数百万件のイベントを取り込み、処理して、イベント情報を格納することができます。 このチュートリアルでは、イベント ハブを作成し、呼び出しイベント ジェネレーター アプリでそのイベント ハブに呼び出しデータを送信します。 Event Hubs について詳しくは、[Azure Service Bus のドキュメント](https://docs.microsoft.com/azure/service-bus/)をご覧ください。

>[!NOTE]
>この手順についてさらに詳しくは、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](../event-hubs/event-hubs-create.md)」をご覧ください。 

### <a name="create-a-namespace-and-event-hub"></a>名前空間とイベント ハブを作成する
この手順では、まずイベント ハブの名前空間を作成し、その名前空間にイベント ハブを追加します。 イベント ハブの名前空間は、関連するイベント バス インスタンスを論理的にグループ化するために使われます。 

1. Azure Portal にログインし、**[リソースの作成]** > **[モノのインターネット]** > **[イベント ハブ]** をクリックします。 

2. **[名前空間の作成]** ウィンドウで、名前空間の名前 (例: `<yourname>-eh-ns-demo`) を入力します。 名前空間には任意の名前を使用できますが、この名前は URL で有効である必要があり、Azure 全体で一意である必要があります。 
    
3. サブスクリプションを選択し、リソース グループを作成または選択して、**[作成]** をクリックします。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. イベント ハブの名前空間のデプロイが完了したら、Azure リソースの一覧でその名前空間を見つけます。 

5. 新しい名前空間をクリックし、名前空間ウィンドウで **[イベント ハブ]** をクリックします。

   ![新しいイベント ハブを作成するための [イベント ハブの追加] ボタン](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. 新しいイベント ハブに `asa-eh-frauddetection-demo` という名前を付けます。 別の名前を使用してもかまいません。 その場合、名前を書き留めておきます。後でこの名前が必要になります。 イベント ハブの他のオプションをここで設定する必要はありません。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. **Create** をクリックしてください。

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>イベント ハブへのアクセスを許可し、接続文字列を取得する

プロセスがイベント ハブにデータを送信できるようにするには、イベント ハブに、適切なアクセスを許可するポリシーが必要です。 アクセス ポリシーにより、承認情報を含む接続文字列が生成されます。

1.  イベント名前空間ウィンドウで、**[イベント ハブ]** をクリックし、新しいイベント ハブの名前をクリックします。

2.  イベント ハブ ウィンドウで、**[共有アクセス ポリシー]** をクリックし、**[+&nbsp;追加]** をクリックします。

    >[!NOTE]
    >イベント ハブの名前空間ではなく、イベント ハブを操作していることを確認してください。

3.  `sa-policy-manage-demo` という名前のポリシーを追加し、**[要求]** の **[管理]** を選択します。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  **Create** をクリックしてください。

5.  ポリシーがデプロイされたら、共有アクセス ポリシーの一覧でそのポリシーをクリックします。

6.  **[接続文字列 - 主キー]** というボックスを見つけ、接続文字列の横のコピー ボタンをクリックします。 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  接続文字列をテキスト エディターに貼り付けます。 この接続文字列は、少し編集を加えた後に、次のセクションで必要になります。

    接続文字列は次のようになります。

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    接続文字列には、セミコロンで区切られた複数のキーと値のペア (`Endpoint`、`SharedAccessKeyName`、`SharedAccessKey`、`EntityPath`) が含まれています。  

## <a name="configure-and-start-the-event-generator-application"></a>イベント ジェネレーター アプリケーションの構成と起動

TelcoGenerator アプリを開始する前に、作成したイベント ハブに呼び出しレコードを送信するように構成する必要があります。

### <a name="configure-the-telcogenerator-app"></a>TelcoGenerator アプリを構成する

1. 接続文字列をエディターにコピーし、`EntityPath` の値を書き留めた後、`EntityPath` のペアを削除します (忘れず、前にあるセミコロンも削除します)。 

2. TelcoGenerator.zip ファイルを解凍したフォルダーにある telcodatagen.exe.config ファイルをエディターで開きます  (複数の .config ファイルがあるので、正しいファイルを開くように注意してください)。

3. `<appSettings>` 要素で次のように設定します。

   * `EventHubName` キーの値をイベント ハブ名 (つまり、エンティティ パスの値) に設定します。
   * `Microsoft.ServiceBus.ConnectionString` キーの値を接続文字列に設定します。 

   `<appSettings>` セクションは、次の例のようになります  (わかりやすくするため、行を折り返し、承認トークンから一部の文字を削除してあります)。

   ![TelcoGenerator 構成ファイルに示されるイベント ハブ名と接続文字列](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. ファイルを保存します。 

### <a name="start-the-app"></a>アプリを起動する
1.  コマンド ウィンドウを開き、TelcoGenerator アプリを解凍したフォルダーに移動します。
2.  次のコマンドを入力します。

        ```cmd
        telcodatagen.exe 1000 0.2 2
        ```

    パラメーターは、次のとおりです。 

    * 1 時間あたりの CDR の数。 
    * SIM カード不正の確率:アプリが不正な呼び出しをシミュレートする頻度 (すべての呼び出しに対するパーセンテージ)。 値 0.2 は、呼び出しレコードの約 20% が不正に見えることを意味します。
    * 継続時間。 アプリを実行する時間数。 コマンドラインで Ctrl + C キーを押して、いつでもアプリを停止できます。

    数秒後に、アプリはイベント ハブに送信する呼び出しレコードを画面に表示し始めます。

このリアルタイム不正行為検出アプリケーションで使う主要なフィールドは次のとおりです。

|**レコード**|**定義**|
|----------|--------------|
|`CallrecTime`|通話開始時刻のタイムスタンプ。 |
|`SwitchNum`|通話の接続に使われた電話交換機。 この例では、交換機は発信国を表す文字列です (US、China、UK、Germany、Australia)。 |
|`CallingNum`|発信元の電話番号。 |
|`CallingIMSI`|IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。 これは、発信元の一意の識別番号です。 |
|`CalledNum`|通話受信者の電話番号。 |
|`CalledIMSI`|IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。 これは、通話受信者の一意の識別番号です。 |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>ストリーミング データを管理する Stream Analytics ジョブを作成する

呼び出しイベントのストリームを用意したので、Stream Analytics ジョブを設定できます。 このジョブは、設定したイベント ハブからデータを読み取ります。 

### <a name="create-the-job"></a>ジョブを作成する 

1. Azure Portal で、**[リソースの作成]** > **[モノのインターネット]** > **[Stream Analytics ジョブ]** の順にクリックします。

2. ジョブに `asa_frauddetection_job_demo` という名前を付け、サブスクリプション、リソース グループ、場所を指定します。

    最適なパフォーマンスを実現し、リージョン間でのデータ転送の料金がかからないように、ジョブとイベント ハブを同じリージョンに配置することをお勧めします。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. **Create** をクリックしてください。

    ジョブが作成され、ポータルにジョブの詳細が表示されます。 まだ何も実行されていませんが、開始する前にジョブを構成する必要があります。

### <a name="configure-job-input"></a>ジョブの入力を構成する

1. ダッシュボードまたは **[すべてのリソース]** ウィンドウで、`asa_frauddetection_job_demo` Stream Analytics ジョブを探して選びます。 
2. [Stream Analytics ジョブ] ウィンドウの **[概要]** セクションで、**[入力]** ボックスをクリックします。

   ![[Streaming Analytics ジョブ] ウィンドウの [トポロジ] の下にある [入力] ボックス](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. **[ストリーム入力の追加]** をクリックして **[イベント ハブ]** を選択します。 [新しい入力] ページに次の情報を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |入力のエイリアス  |  CallStream   |  ジョブの入力を識別する名前を入力します。   |
   |サブスクリプション   |  \<該当するサブスクリプション\> |  作成したイベント ハブがある Azure サブスクリプションを選択します。   |
   |Event Hub 名前空間  |  asa-eh-ns-demo |  イベント ハブの名前空間の名前を入力します。   |
   |イベント ハブ名  | asa-eh-frauddetection-demo | イベント ハブの名前を選択します。   |
   |イベント ハブ ポリシー名  | asa-policy-manage-demo | 以前に作成したアクセス ポリシーを選択します。   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. **Create** をクリックしてください。

## <a name="create-queries-to-transform-real-time-data"></a>リアルタイム データを変換するクエリを作成する

ここまでで、着信データ ストリームを読み取る Stream Analytics ジョブを設定しました。 次に、リアルタイムでデータを分析するクエリを作成します。 Stream Analytics では、リアルタイム処理のために変換を記述する単純な宣言型のクエリ モデルがサポートされます。 このクエリは、Stream Analytics に固有のいくつかの拡張を含む SQL に似た言語を使います。 

単純なクエリで、着信したすべてのデータを読み取るだけです。 ただし、多くの場合は、特定のデータやデータ内の関係を検索するクエリを作成します。 チュートリアルのこのセクションでは、複数のクエリを作成してテストし、分析のために入力ストリームを変換するいくつかの方法を学習します。 

ここで作成するクエリは、変換されたデータを画面に表示するだけです。 後のセクションでは、出力シンクとそのシンクに変換されたデータを書き込むクエリを構成します。

言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/dn834998.aspx)を参照してください。

### <a name="get-sample-data-for-testing-queries"></a>クエリをテストするためのサンプル データを取得する

TelcoGenerator アプリはイベント ハブに呼び出しレコードを送信しており、Stream Analytics ジョブはイベント ハブから読み取るように構成されています。 クエリを使ってジョブをテストし、正しく読み取っていることを確認できます。 Azure コンソールでクエリをテストするには、サンプル データが必要です。 このチュートリアルでは、イベント ハブに送られてくるストリームからサンプル データを抽出します。

1. TelcoGenerator アプリが稼働していて呼び出しレコードを生成していることを確認します。
2. ポータルで、[Streaming Analytics ジョブ] ウィンドウに戻ります  (ウィンドウを閉じた場合は、**[すべてのリソース]** ウィンドウで `asa_frauddetection_job_demo` を検索します)。
3. **[クエリ]** ボックスをクリックします。 そのジョブ用に構成されている入力と出力が一覧表示されます。出力に送信される入力ストリームを変換できるクエリを作成できます。
4. **[クエリ]** ウィンドウで、`CallStream` 入力の横の点をクリックし、**[入力からのサンプル データ]** を選びます。

   ![Stream Analytics ジョブ エントリにサンプル データを使用するためのメニュー オプション - [入力からのサンプル データ] を選択](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. **[分]** を 3 に設定し、**[OK]** をクリックします。 
    
   ![3 分が選択された入力ストリームのサンプリング オプション。](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    入力ストリームから 3 分間分のデータがサンプリングされ、サンプル データの準備ができると通知されます  (少し時間がかかります)。 

サンプル データは一時的に保存され、クエリ ウィンドウを開いている間使用できます。 クエリ ウィンドウを閉じると、サンプル データは破棄されるので、サンプル データの新しいセットを作成する必要があります。 

代わりの方法として、サンプル データが含まれる .json ファイルを [GitHub から](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)入手し、その .json ファイルをアップロードして、`CallStream` 入力のサンプル データとして使うこともできます。 

### <a name="test-using-a-pass-through-query"></a>パススルー クエリを使ってテストする

すべてのイベントをアーカイブする場合は、パススルー クエリを使って、イベントのペイロード内のすべてのフィールドを読み取ることができます。

1. クエリ ウィンドウに次のクエリを入力します。
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >SQL と同じように、キーワードに大文字と小文字の区別はなく、空白は重要ではありません。

    このクエリで、`CallStream` は入力を作成するときに指定した別名です。 別の別名を使った場合は、代わりにその名前を使います。

2. **[Test]** をクリックします。

    Stream Analytics ジョブは、サンプル データに対してクエリを実行し、ウィンドウの下部に出力を表示します。 結果は、イベント ハブと Streaming Analytics ジョブが正しく構成されていることを示します  (前述のように、クエリがデータを書き込むことができる出力シンクを後で作成します)。

   ![73 レコードが生成されたことを示す Stream Analytics ジョブの出力](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    表示される正確なレコード数は、3 分間のサンプルでキャプチャされたレコードの数によって異なります。
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>列のプロジェクションを使ってフィールドの数を減らす

多くの場合、分析に入力ストリームのすべての列は必要ありません。 クエリを使って、パススルー クエリより少ない数のフィールドを返すことができます。

1. コード エディターでクエリを次のように変更します。

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. もう一度 **[Test]** をクリックします。 

   ![25 レコードを示すプロジェクションの Stream Analytics ジョブの出力](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>リージョン別に着信通話の数をカウントする:集計を含むタンブリング ウィンドウ

リージョンごとに受信通話の数をカウントしたいものとします。 ストリーミング データでは、カウントのような集計関数を実行する場合、ストリームを時間単位に分割する必要があります (データ ストリーム自体は実質的にエンドレスであるため)。 これを行うには、Streaming Analytics の[ウィンドウ関数](stream-analytics-window-functions.md)を使います。 その後、そのウィンドウ内のデータを単位として処理できます。

この変換では、重ならないテンポラル ウィンドウのシーケンスを使います。各ウィンドウの個別のデータ セットをグループ化して集計できます。 この種のウィンドウは、"*タンブリング ウィンドウ*" と呼ばれます。 タンブリング ウィンドウでは、発信元の国を表す `SwitchNum` でグループ化された受信通話の数を取得できます。 

1. コード エディターでクエリを次のように変更します。

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    このクエリでは、`FROM` 句で `Timestamp By` キーワードを使って、タンブリング ウィンドウの定義に使う入力ストリームのタイムスタンプ フィールドを指定します。 この場合、ウィンドウは各レコードの `CallRecTime` フィールドによってデータをセグメントに分割します。 フィールドを指定しないと、各イベントがイベント ハブに到着した時刻がウィンドウ化操作に使われます。 「[Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)」(Stream Analytics クエリ言語リファレンス) の「Arrival Time Vs Application Time」(到着時刻とアプリケーション時刻) をご覧ください。 

    プロジェクションに含まれる `System.Timestamp` は、各ウィンドウの終わりのタイムスタンプを返します。 

    タンブリング ウィンドウを使うことを指定するには、`GROUP BY` 句で [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) 関数を使います。 この関数では、時間単位 (1 マイクロ秒から 1 日まで) とウィンドウ サイズ (単位数) を指定します。 この例では、タンブリング ウィンドウは 5 秒間隔で構成されるので、5 秒間の国別の通話数が表示されます。

2. もう一度 **[Test]** をクリックします。 結果では、**WindowEnd** のタイムスタンプが 5 秒刻みになっていることに注目してください。

   ![13 レコードを示す集計の Stream Analytics ジョブの出力](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>自己結合を使って SIM 不正を検出する

この例では、不正な使用を、5 秒以内に別の場所で同じユーザーから発信された呼び出しと考えます。 たとえば、合法的に同じユーザーが米国とオーストラリアで同時に呼び出しを行うことはできません。 

このようなケースをチェックするには、ストリーミング データの自己結合を使って、`CallRecTime` の値に基づいてストリームをそれ自体に結合します。 その後、`CallingIMSI` の値 (発信番号) が同じなのに `SwitchNum` の値 (発信国) が同じではない呼び出しレコードを探すことができます。

ストリーミング データで結合を使うときは、一致する行と見なす最大時間差を結合で制限する必要があります  (前に説明したように、ストリーミング データは実質的にエンドレスです)。リレーションシップの時間限界は、結合の `ON` 句で `DATEDIFF` 関数を使って指定します。 この例では、結合は 5 秒間隔の通話データに基づきます。

1. コード エディターでクエリを次のように変更します。 

        ```SQL
        SELECT  System.Timestamp as Time, 
            CS1.CallingIMSI, 
            CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, 
            CS1.SwitchNum as Switch1, 
            CS2.SwitchNum as Switch2 
        FROM CallStream CS1 TIMESTAMP BY CallRecTime 
            JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
            ON CS1.CallingIMSI = CS2.CallingIMSI 
            AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
        WHERE CS1.SwitchNum != CS2.SwitchNum
        ```

    このクエリは、`DATEDIFF` 関数が結合に含まれることを除けば、他の SQL 結合と似ています。 `DATEDIFF` のこのバージョンは Streaming Analytics に固有であり、`ON...BETWEEN` 句で使う必要があります。 パラメーターは、時間単位 (この例では秒) と、結合の 2 つのソースの別名です  これは、SQL の標準的な `DATEDIFF` 関数と異なります。

    `WHERE` 句には、発信元の交換機が同じではないという、通話を不正と見なす条件が含まれます。 

2. もう一度 **[Test]** をクリックします。 

   ![6 レコードが生成されたことを示す自己結合の Stream Analytics ジョブの出力](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. **[保存]** をクリックして、自己結合クエリを Stream Analytics ジョブの一部として保存します  (サンプル データは保存されません)。

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>変換されたデータを格納する出力シンクを作成する

イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義しました。 最後に、ジョブの出力シンク、つまり変換されたストリームを書き込む場所を定義します。 

出力シンクとしては、SQL Server データベース、テーブル ストレージ、Data Lake ストレージ、Power BI、別のイベント ハブなど、多くのリソースを使うことができます。 このチュートリアルでは、Azure Blob Storage にストリームを書き込みます。Azure Blob Storage は非構造化データに対応しているため、これは後で分析できるようにイベント情報を収集するための一般的な選択肢です。

既存の BLOB ストレージ アカウントがある場合は、それを使うことができます。 このチュートリアルでは、新しいストレージ アカウントを作成する方法を学習します。

### <a name="create-an-azure-blob-storage-account"></a>Azure Blob Storage アカウントを作成する

1. Azure Portal の左上隅で、**[リソースの作成]** > **[ストレージ]** > **[ストレージ アカウント]** の順に選択します。 ストレージ アカウント ジョブのページで、**[名前]** を「asaehstorage」に、**[場所]** を「米国東部」に、**[リソース グループ]** を「asa-eh-ns-rg」に設定するよう入力します (パフォーマンスを高めるために、ストリーミング ジョブと同じリソース グループ内のストレージ アカウントをホストします)。 その他の設定は既定値のままにします。  

   ![Azure portal でストレージ アカウントを作成する](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. Azure Portal で、[Streaming Analytics ジョブ] ウィンドウに戻ります  (ウィンドウを閉じた場合は、**[すべてのリソース]** ウィンドウで `asa_frauddetection_job_demo` を検索します)。

3. **[ジョブ トポロジ]** セクションで、**[出力]** ボックスをクリックします。

4. **[出力]** ウィンドウで **[追加]** をクリックし、**[Blob ストレージ]** を選びます。 [新規出力] ページに次の情報を入力します。

   |**設定**  |**推奨値**  |**説明**  |
   |---------|---------|---------|
   |出力エイリアス  |  CallStream-FraudulentCalls   |  ジョブの出力を識別する名前を入力します。   |
   |サブスクリプション   |  \<該当するサブスクリプション\> |  作成したストレージ アカウントを持っている Azure サブスクリプションを選択します。 ストレージ アカウントは、同じサブスクリプションにある場合も、別のサブスクリプションにある場合もあります。 この例では、同じサブスクリプションにストレージ アカウントを作成したと想定しています。 |
   |ストレージ アカウント  |  asaehstorage |  作成したストレージ アカウントの名前を入力します。 |
   |コンテナー  | asa-fraudulentcalls-demo | [新規作成] を選択し、コンテナー名を入力します。 |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. **[Save]** をクリックします。 


## <a name="start-the-streaming-analytics-job"></a>Stream Analytics ジョブを開始する

ジョブの構成が完了しました。 入力 (イベント ハブ)、変換 (不正通話を検索するクエリ)、出力 (BLOB ストレージ) を指定しました。 ジョブを開始できます。 

1. TelcoGenerator アプリが動いていることを確認します。

2. ジョブ ウィンドウで、**[開始]** をクリックします。 **[ジョブの開始]** ウィンドウの [ジョブ出力の開始時刻] で、**[現在]** を選択します。 

   ![Stream Analytics ジョブの開始](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>変換後のデータを調べる

Streaming Analytics ジョブが完成しました。 このジョブは、通話メタデータのストリームを調べて、不正な呼び出しをリアルタイムに検出し、不正な通話に関する情報をストレージに書き込みます。 

このチュートリアルの最後に、Streaming Analytics ジョブによってキャプチャされているデータを確認しておく必要があります。 データは、Azure BLOB ストレージにチャンク (ファイル) として書き込まれます。 Azure Blob Storage を読み込む任意のツールを使うことができます。 前提条件セクションで触れたように、Visual Studio の Azure 拡張機能、または [Azure Storage Explorer](https://storageexplorer.com/) や [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage) などのツールを使うことができます。 

BLOB ストレージ内のファイルの内容を調べると、次のようなデータが表示されます。

   ![Azure BLOB ストレージに格納される Streaming Analytics の出力](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不正検出シナリオから続けて読むことができ、このチュートリアルで作成したリソースを使う他の記事があります。 続けて読む場合は、「**次のステップ**」をご覧ください。

一方、これで終了し、作成したリソースがもう必要ない場合は、不要な Azure の料金が発生しないように削除できます。 その場合は、次のようにすることをお勧めします。

1. Stream Analytics ジョブを停止します。 **[ジョブ]** ウィンドウの上部にある **[停止]** をクリックします。
2. TelcoGenerator アプリを停止します。 アプリを起動したコマンド ウィンドウで、Ctrl + C キーを押します。
3. このチュートリアルのためだけに新しい BLOB ストレージ アカウントを作成した場合は、それを削除します。 
4. Stream Analytics ジョブを削除します。
5. イベント ハブを削除します。
6. イベント ハブの名前空間を削除します。

## <a name="get-support"></a>サポートを受ける

さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルに続けて、次の記事を読むことができます。

* [Stream Analytics と Power BI:ストリーミング データのリアルタイム分析ダッシュボード](stream-analytics-power-bi-dashboard.md)。 この記事では、Stream Analytics ジョブの TelCo 出力を、リアルタイムの視覚化と分析のために Power BI に送信する方法を説明します。

Stream Analytics の一般的な情報について詳しくは、以下の記事をご覧ください。

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
