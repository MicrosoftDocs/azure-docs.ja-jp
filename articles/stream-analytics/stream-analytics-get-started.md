---
title: "Azure Stream Analytics の使用: リアルタイムの不正行為の検出 | Microsoft Docs"
description: "Stream Analytics を使用してリアルタイムの不正行為検出ソリューションを作成する方法について説明します。 リアルタイム イベント処理のためにイベント ハブを使用します。"
keywords: "異常検出, 不正行為の検出, リアルタイムの異常検出"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: d1ed7bd8-9296-4ff8-ade3-d2765541d377
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 14c708405ea73bb7bc81863bdcf51a056447a296
ms.lasthandoff: 11/17/2016


---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Azure Stream Analytics の使用 | リアルタイムの不正行為の検出
Azure Stream Analytics を使用してリアルタイムに不正行為を検出するための、エンド ツー エンド ソリューションを作成する方法について説明します。 Azure イベント ハブにイベントを取り込み、集計用または警告用の Stream Analytics クエリを作成し、結果を出力シンクに送信することで、リアルタイム処理でデータに対する洞察を得ることができます。 通信に関するリアルタイムの異常検出が説明されていますが、このサンプルの手法は、クレジット カードや ID の盗難のシナリオなど、その他の種類の不正行為の検出にも同様に適しています。

Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。 詳細については、「 [Azure Stream Analytics の概要](stream-analytics-introduction.md)」を参照してください。

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>シナリオ: 通信および SIM におけるリアルタイムでの不正行為の検出
ある通信会社は、膨大な量の着信データを扱っています。 この会社では、このデータを使用して次のことを行う必要があります。

* このデータを管理可能な量に削減し、時間および地理的領域で変化する顧客の利用状況に関する洞察を得る。
* SIM 不正行為 (地理的に異なる場所でほぼ同じ時刻に同じ ID から複数の着信がある) をリアルタイムで検出することで、顧客への通知やサービスのシャットダウンによって不正行為に簡単に対応できるようにする。

これはモノのインターネット (IoT) に関する一般的なシナリオです。つまり、遠隔測定やセンサーによりデータが大量に生成され、顧客はその集計や、異常についてのアラート通知をリアルタイムに必要とするという状況です。

## <a name="prerequisites"></a>前提条件
* Microsoft ダウンロード センターから [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) をダウンロードすること 
* 省略可能: [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Azure Event Hubs 入力とコンシューマー グループの作成
サンプル アプリケーションでは、イベントを生成し、それらを Event Hub インスタンスにプッシュしてリアルタイム処理を行います。 Service Bus Event Hubs は、Stream Analytics のイベント取り込みの推奨方法です。Event Hubs の詳細については、[Azure Service Bus のマニュアル](/documentation/services/service-bus/)をご覧ください。

イベント ハブを作成するには:

1. [Azure Portal](https://manage.windowsazure.com/) で、**[新規]** > **[App Services]** > **[Service Bus]** > **[イベント ハブ]** > **[簡易作成]** をクリックします。 名前、リージョン、および新規または既存の名前空間を入力し、新しい Event Hub を作成します。  
2. 各 Stream Analytics ジョブが単一の Event Hub コンシューマー グループから読み取るようにするのがベスト プラクティスです。 以下にコンシューマー グループを作成するプロセスを段階的に説明します。コンシューマー グループの詳細については、[ここ](https://msdn.microsoft.com/library/azure/dn836025.aspx)をご覧ください。 コンシューマー グループを作成するには、新たに作成されたイベント ハブに移動し、**[コンシューマー グループ]** タブをクリックし、ページ下部の **[作成]** をクリックして、コンシューマー グループの名前を指定します。
3. イベント ハブへのアクセスを許可するには、共有アクセス ポリシーを作成する必要があります。  Event Hub の **[構成]** タブをクリックします。
4. **[共有アクセス ポリシー]** で、**[管理]** アクセス許可を持つ新しいポリシーを作成します。
   
   ![管理アクセス許可のあるポリシーを作成できる [共有アクセス ポリシー]。](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)
5. ページの下部にある **[保存]** をクリックします。
6. **[ダッシュボード]** に移動し、ページ下部の **[接続情報]** をクリックし、接続情報をコピーして保存します。

## <a name="configure-and-start-event-generator-application"></a>イベント ジェネレーター アプリケーションの構成と起動
サンプル受信通話メタデータを生成し、それを Event Hub にプッシュするクライアント アプリケーションが提供されています。 次の手順に従って、このアプリケーションを設定します。  

1. [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)ファイルをダウンロードします。 次に、このファイルをディレクトリに解凍します。
   
   **注**: ダウンロードされた zip ファイルが Windows によってブロックされる可能性があります。 ファイルを右クリックし、[プロパティ] をクリックします。 "このファイルは他のコンピューターから取得したものです。 このコンピューターを保護するため、このファイルへのアクセスはブロックされる可能性があります" というメッセージが表示されたら、[ブロック解除] ボックスをオンにして、zip ファイルで [適用] をクリックします。
2. **telcodatagen.exe.config** 内の Microsoft.ServiceBus.ConnectionString 値と EventHubName 値を、Event Hub の接続文字列と名前に置き換えます。
   
   **注**: Azure ポータルからコピーされた接続文字列によって、接続の名前が最後に配置されます。 add key= フィールドからは ";EntityPath=<value>" を必ず削除してください。
3. アプリケーションを起動します。 使用方法は次のとおりです。
   
   telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

次の例では、2 時間の間に、不正行為の確率が 20% のイベントが 1000 件生成されます。

    telcodatagen.exe 1000 .2 2

Event Hub に送信されるレコードが表示されます。 このリアルタイムの不正行為検出アプリケーションで使用するいくつかのキー フィールドをここで定義します。

| レコード | 定義 |
| --- | --- |
| CallrecTime |通話開始時刻のタイムスタンプ。 |
| SwitchNum |通話の接続に使用された電話交換機。 |
| CallingNum |発信元の電話番号。 |
| CallingIMSI |IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。  発信元の一意の識別番号。 |
| CalledNum |通話受信者の電話番号。 |
| CalledIMSI |IMSI (International Mobile Subscriber Identity: 国際携帯機器加入者識別番号)。  通話受信者の一意の識別番号。 |

## <a name="create-stream-analytics-job"></a>Stream Analytics ジョブの作成
この時点で通信イベントのストリームは存在するため、リアルタイムでこれらのイベントを分析するための Stream Analytics ジョブを設定できます。

### <a name="provision-a-stream-analytics-job"></a>Stream Analytics のジョブの準備
1. Azure Portal で、**[新規]、[Data Services]、[Stream Analytics]、[簡易作成]** の順にクリックします。
2. 次の値を指定してから、 **[Stream Analytics ジョブの作成]**をクリックします。
   
   * **[ジョブ名]**: ジョブ名を入力します。
   * **[リージョン]**: ジョブを実行するリージョンを選択します。 パフォーマンスを向上させ、リージョン間のデータ転送が有料にならないように、同じリージョンにジョブとイベント ハブを配置することを検討します。
   * **[ストレージ アカウント]**: このリージョン内で実行されているすべての Stream Analytics ジョブの監視データを格納するために使用する Azure ストレージ アカウントを選択します。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成することができます。
3. Stream Analytics ジョブの一覧を表示するには、左側のウィンドウにある **[Stream Analytics]** をクリックします。
   
   ![Stream Analytics サービス アイコン](./media/stream-analytics-get-started/stream-analytics-service-icon.png)
4. 新しいジョブが **[作成済み]**の状態で表示されます。 ページの下部にある **[開始]** ボタンが無効になっていることがわかります。 ジョブを開始する前に、ジョブの入力、出力、クエリなどを構成する必要があります。

### <a name="specify-job-input"></a>ジョブの入力の指定
1. Stream Analytics ジョブで、ページ上部の **[入力]** をクリックしてから、**[入力の追加]** をクリックします。 表示されたダイアログ ボックスには、入力を設定する手順がいくつか表示されます。
2. **[データ ストリーム]**を選択し、右側のボタンをクリックします。
3. **[イベント ハブ]**を選択してから、右側のボタンをクリックします。
4. 3 ページ目で、次の値を入力または選択します。
   
   * **[入力のエイリアス]**: *CallStream*など、このジョブの入力のフレンドリ名を入力します。 後でクエリでこの名前を使用します。
   * **[イベント ハブ]**: 作成したイベント ハブが Stream Analytics ジョブと同じサブスクリプションにある場合は、イベント ハブがある名前空間を選択します。
   
   イベント ハブが他のサブスクリプションにある場合、**[別のサブスクリプションのイベント ハブを使用]** を選択し、**[Service Bus 名前空間]**、**[イベント ハブ名]**、**[イベント ハブ ポリシー名]**、**[イベント ハブ ポリシー キー]**、**[イベント ハブ パーティション数]** の情報を手動で入力します。
   
   * **[イベント ハブ名]**: イベント ハブの名前を選択します。
   * **[イベント ハブ ポリシー名]**: このチュートリアルで前に作成した Event Hub のポリシーを選択します。
   * **[イベント ハブ コンシューマー グループ]**: このチュートリアルで前に作成したコンシューマー グループを入力します。
5. 右側のボタンをクリックします。
6. 次の値を指定します。
   
   * **[イベント シリアライザー形式]**: JSON
   * **[エンコード]**: UTF8
7. チェック ボタンをクリックしてこのソースを追加し、Stream Analytics が Event Hub に正常に接続できることを確認します。

### <a name="specify-job-query"></a>ジョブのクエリの指定
Stream Analytics では、リアルタイム処理のために変換を記述する単純な宣言型のクエリ モデルがサポートされます。 言語に関する詳細については、 [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/dn834998.aspx)を参照してください。 このチュートリアルは、通話データのリアルタイム ストリームに対するいくつかのクエリを作成してテストするのに役立ちます。

#### <a name="optional-sample-input-data"></a>省略可能: サンプルの入力データ
実際のジョブ データに対するクエリを検証するには、**サンプル データ**機能を使用して、ストリームからイベントを抽出し、テスト用のイベントの .JSON ファイルを作成できます。  次の手順は、これを実行する方法を示しています。また、テスト用にサンプルの [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) ファイルも用意されています。

1. Event Hub の入力を選択して、ページ下部の **[サンプル データ]** をクリックします。
2. 表示されたダイアログ ボックスで、データ収集を開始する **[開始時間]** を指定し、使用する追加のデータ量に応じて **[期間]** を指定します。
3. 入力からのデータのサンプリングを開始するには、チェック ボタンをクリックします。  データ ファイルが生成されるまでに、1 ～ 2 分かかります。  プロセスが完了したら、 **[詳細]** をクリックし、生成された .JSON ファイルをダウンロードして保存します。
   
   ![処理済みデータをダウンロードして JSON ファイルに保存](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>パススルー クエリ
すべてのイベントをアーカイブする場合は、パススルー クエリを使用して、イベントまたはメッセージのペイロード内のすべてのフィールドを読み取ることができます。 最初に、イベント内のすべてのフィールドを示す単純なパススルー クエリを実行します。

1. Stream Analytics ジョブ ページの上部にある **[クエリ]** をクリックします。
2. 次の内容をコード エディターに追加します。
   
     SELECT * FROM CallStream
   
   > 入力ソースの名前が前に指定した入力の名前と必ず一致するようにします。
   > 
   > 
3. クエリ エディターの下の **[テスト]** をクリックします。
4. 前の手順を使用して作成したテスト ファイルを指定するか、 [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)を使用します。
5. チェック ボタンをクリックして、クエリ定義の下に表示される結果を確認します。
   
   ![クエリの定義の結果](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)

### <a name="column-projection"></a>列のプロジェクション
返されたフィールドを削減して、より小規模なセットにします。

1. コード エディターでクエリを次のように変更します。
   
     SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum   FROM CallStream
2. クエリ エディターの下の **[再実行]** をクリックして、クエリの結果を表示します。
   
   ![クエリ エディターで出力します。](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>リージョン別の着信通話の数: 集計を含むタンブリング ウィンドウ
リージョンごとの着信通話の量を比較するには、 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) を利用して、5 秒ごとに SwitchNum でグループ化された着信通話の数を取得します。

1. コード エディターでクエリを次のように変更します。
   
     SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount   FROM CallStream TIMESTAMP BY CallRecTime   GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
   
   このクエリでは、 **Timestamp By** キーワードを使用して、一時的な計算に使用されるペイロードのタイムスタンプ フィールドを指定しています。 このフィールドが指定されていない場合は、イベントが Event Hub に到着した時間を使用してウィンドウ化操作が実行されます。 [「Stream Analytics クエリ言語リファレンス」にある到着時間とアプリケーション時間の比較に関する説明](https://msdn.microsoft.com/library/azure/dn834998.aspx)を参照してください。
   
   各期間の終わりのタイムスタンプには、 **System.Timestamp** プロパティを使用してアクセスできることに注意してください。
2. クエリ エディターの下の **[再実行]** をクリックして、クエリの結果を表示します。
   
   ![Timestand By でのクエリ結果](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>自己結合による SIM 不正行為の検出
不正使用の可能性を識別するために、同一ユーザーからの、5 秒未満で別の場所から発信された通話を探します。  この場合は通話イベントのストリームを自己 [結合](https://msdn.microsoft.com/library/azure/dn835026.aspx) し、そのようなケースがないかを確認します。

1. コード エディターでクエリを次のように変更します。
   
     SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,   CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2   FROM CallStream CS1 TIMESTAMP BY CallRecTime   JOIN CallStream CS2 TIMESTAMP BY CallRecTime   ON CS1.CallingIMSI = CS2.CallingIMSI   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5   WHERE CS1.SwitchNum != CS2.SwitchNum
2. クエリ エディターの下の **[再実行]** をクリックして、クエリの結果を表示します。
   
   ![結合のクエリ結果](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>出力シンクの作成
イベント ストリーム、イベントを取り込むためのイベント ハブ入力、ストリームに対して変換を実行するためのクエリを定義したところで、最後に、ジョブの出力シンクを定義します。  Blob Storage に不正な動作のイベントを記述します。

Blob Storage 用のコンテナーがまだない場合は、次の手順に従って作成します。

1. 既存のストレージ アカウントを使用するか、新しいストレージ アカウントを作成します。新しいストレージ アカウントを作成するには、**[新規]、[Data Services]、[Storage]、[簡易作成]** の順にクリックして、画面の指示に従います。
2. ストレージ アカウントを選択し、ページ上部にある **[コンテナー]** をクリックし、**[追加]** をクリックします。
3. コンテナーの **[名前]** を指定し、その **[アクセス]** を[パブリック BLOB] に設定します。

## <a name="specify-job-output"></a>ジョブの出力の指定
1. Stream Analytics ジョブで、ページ上部の **[出力]** をクリックし、**[出力の追加]** をクリックします。 表示されたダイアログ ボックスには、出力を設定する手順がいくつか表示されます。
2. **[Blob Storage]**を選択し、右側のボタンをクリックします。
3. 3 ページ目で、次の値を入力または選択します。
   
   * **[出力のエイリアス]**: このジョブの出力のフレンドリ名を入力します。
   * **[サブスクリプション]**: 作成した Blob Storage が Stream Analytics ジョブと同じサブスクリプションにある場合は、**[現在のサブスクリプションのストレージ アカウントを使用]** を選択します。 ストレージが別のサブスクリプションにある場合は、**[別のサブスクリプションのストレージ アカウントを使用]** を選択し、**[ストレージ アカウント]**、**[ストレージ アカウント キー]**、**[コンテナー]** の情報を手動で入力します。
   * **[ストレージ アカウント]**: ストレージ アカウントの名前を選択します。
   * **[コンテナー]**: コンテナーの名前を選択します。
   * **[ファイル名プレフィックス]**: BLOB 出力の書き込み時に使用するファイルのプレフィックスを入力します。
4. 右側のボタンをクリックします。
5. 次の値を指定します。
   
   * **[イベント シリアライザー形式]**: JSON
   * **[エンコード]**: UTF8
6. チェック ボタンをクリックしてこのソースを追加し、Stream Analytics からストレージ アカウントに正常に接続できることを確認します。

## <a name="start-job-for-real-time-processing"></a>リアルタイム処理のためのジョブの開始
ジョブの入力、クエリ、および出力がすべて指定されたため、リアルタイムの不正行為検出のための Stream Analytics ジョブをいつでも開始できます。

1. ジョブ **ダッシュボード**から、ページの下部にある **[開始]** をクリックします。
2. 表示されたダイアログ ボックスで、 **[ジョブ開始時刻]** を選択し、ダイアログ ボックスの下部にあるチェック ボタンをクリックします。 ジョブの状態が **[開始中]** に変わりすぐに **[実行中]** に移行します。

## <a name="view-fraud-detection-output"></a>不正行為の検出出力の表示
不正なイベントが出力されたときにリアルタイムで表示するには、[Azure Storage エクスプローラー](https://azurestorageexplorer.codeplex.com/)や [Azure エクスプローラー](http://www.cerebrata.com/products/azure-explorer/introduction)などのツールを使用します。  

![不正行為の検出: 不正なイベントをリアルタイムで表示](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


