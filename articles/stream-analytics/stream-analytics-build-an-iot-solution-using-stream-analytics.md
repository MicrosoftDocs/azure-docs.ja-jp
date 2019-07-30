---
title: Azure Stream Analytics を使って IoT ソリューションをビルドする
description: 料金所ブースを例に Stream Analytics を使った基本的な IoT ソリューションを紹介します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4b250a5e14ab37553d93453d05f8ff388bf1ba84
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620524"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Stream Analytics を使って IoT ソリューションを構築する

## <a name="introduction"></a>はじめに
このソリューションでは、データに隠された知見を Azure Stream Analytics を使ってリアルタイムで突き止める方法を説明します。 開発者は、データのストリーム (クリック ストリーム、ログ、デバイスによって生成されたイベントなど) に履歴レコードや参照データを簡単に組み合わせて、ビジネスに関する知見を導き出すことができます。 Azure Stream Analytics は、Microsoft Azure でホストされたフル マネージドの、リアルタイム ストリーム計算処理サービスであるため、高い障害回復力とスケーラビリティ、低遅延が実現され、短時間での立ち上げが可能となっています。

このソリューションを完了すると、以下を実現できます。

* Azure Stream Analytics ポータルについて理解を深める。
* ストリーミング ジョブを構成、デプロイする。
* 現実世界の問題を明確化し、Stream Analytics クエリ言語を使って解決する。
* Stream Analytics を使って顧客向けのストリーミング ソリューションを開発できるという確信が得られる。
* 監視機能とログ機能の使用経験を問題のトラブルシューティングに活かす。

## <a name="prerequisites"></a>前提条件
このソリューションを完了するには、次の前提条件を満たしておく必要があります。
* [Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>シナリオの概要:"Hello, Toll!"
有料道路の料金所は一般的な事象です。 世界中の多くの高速道路、橋、トンネルでそれらを目にします。 各料金所には複数の料金所ブースがあります。 手動式の料金所ブースでは、車を停めて通行料金を支払います。 自動式の料金所ブースでは、通過する車両のフロントガラスに貼り付けられた RFID カードを、各ブースの上方に設置されているセンサーがスキャンします。 車両が料金所を通過した事実は、イベント ストリームとして簡単に可視化でき、そのストリームを介して必要な処理を実行することができます。

![Picture of cars at toll booths](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>受信データ
このソリューションでは、2 つのデータ ストリームを扱います。 1 つ目のストリームは、料金所の入口と出口に設置されたセンサーによって生成されます。 2 つ目のストリームは、車両登録データを格納した静的なルックアップ データセットです。

### <a name="entry-data-stream"></a>入口データ ストリーム
入口データ ストリームは、料金所に入る車両の情報を含んでいます。 出口データ イベントは、サンプル アプリに含まれる Web アプリから Event Hub キューにライブでストリーミングされます。

| TollID | EntryTime | LicensePlate | 状態 | Make | モデル | VehicleType | VehicleWeight | Toll | タグ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

次に、それぞれの列について簡単に説明します。

| 列 | 説明 |
| --- | --- |
| TollID |料金所を一意に識別する料金所 ID |
| EntryTime |車両が料金所ブースに入った日時 (UTC) |
| LicensePlate |車両のナンバー プレートの番号 |
| 状態 |米国の州 |
| Make |自動車の製造元 |
| モデル |自動車の型式 |
| VehicleType |1 (乗用車) または 2 (商用車) |
| WeightType |車両重量 (トン)。乗用車の場合は 0 |
| Toll |通行料金の値 (米ドル) |
| タグ |決済を自動化するために自動車に設置された電子タグ (支払いが手動で行われた場合は空) |

### <a name="exit-data-stream"></a>出口データ ストリーム
出口データ ストリームは、料金所から出る車両の情報を含んでいます。 出口データ イベントは、サンプル アプリに含まれる Web アプリから Event Hub キューにライブでストリーミングされます。

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

次に、それぞれの列について簡単に説明します。

| 列 | 説明 |
| --- | --- |
| TollID |料金所を一意に識別する料金所 ID |
| ExitTime |車両が料金所ブースから出た日時 (UTC) |
| LicensePlate |車両のナンバー プレートの番号 |

### <a name="commercial-vehicle-registration-data"></a>商用車の登録データ
このソリューションでは、商用車登録データベースの静的なスナップショットを使用します。 このデータは Azure BLOB ストレージに JSON ファイルとして保存され、サンプルに含まれています。

| LicensePlate | RegistrationId | 有効期限切れ |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

次に、それぞれの列について簡単に説明します。

| 列 | 説明 |
| --- | --- |
| LicensePlate |車両のナンバー プレートの番号 |
| RegistrationId |車両の登録 ID |
| 有効期限切れ |車両登録のステータス: 車両登録が有効期間内である場合は 0、期限切れである場合は 1 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Azure Stream Analytics の環境の設定
このソリューションを完了するには、Microsoft Azure サブスクリプションが必要です。 Azure アカウントをお持ちでない場合は、 [無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/)してください。

Azure クレジットを最適に利用できるよう、この記事の最後にある Azure アカウントのクリーンアップに関するセクションの手順を忘れずに実行してください。

## <a name="deploy-the-sample"></a>サンプルのデプロイ
数回のクリックでリソース グループにまとめてデプロイできるさまざまなリソースがあります。 このソリューションの定義は、GitHub リポジトリ ([https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp)) でホストされています。

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Azure Portal で TollApp テンプレートをデプロイする
1. TollApp 環境を Azure にデプロイするには、この [TollApp Azure テンプレートの展開](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)に関するリンクを使用します。

2. メッセージが表示されたら、Azure Portal にサインインします。

3. さまざまなリソースの課金が行われるサブスクリプションを選択します。

4. 新しいリソース グループに一意の名前を指定します (例: `MyTollBooth`)。

5. Azure の場所を選択します。

6. **[間隔]** を秒数で指定します。 この値は、サンプル Web アプリで使用され、Event Hub にデータを送信する頻度を決定します。

7. 使用条件に同意して、**チェック ボックスをオン**にします。

8. 後でリソースを簡単に特定できるように、 **[ダッシュボードにピン留めする]** を選択します。

9. **[購入]** を選択して、サンプル テンプレートをデプロイします。

10. しばらく待つと、**デプロイが成功した**ことを確認する通知が表示されます。

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Azure Stream Analytics TollApp リソースの確認

1. Azure portal にサインインします

2. 前のセクションで名前を付けたリソース グループを見つけます。

3. 次のリソースがリソース グループに表示されていることを確認します。
   - 1 つの Cosmos DB アカウント
   - 1 つの Azure Stream Analytics ジョブ
   - 1 つの Azure Storage アカウント
   - 1 つの Azure Event Hub
   - 2 つの Web アプリ

## <a name="examine-the-sample-tollapp-job"></a>サンプル TollApp ジョブを調べる
1. 前のセクションのリソース グループから始めます。名前が **tollapp** で始まる Stream Analytics ストリーミング ジョブを選択します (名前には一意にするためのランダムな文字が含まれています)。

2. ジョブの **[概要]** ページで、 **[クエリ]** ボックスにクエリの構文が表示されていることに注目します。

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   このクエリの意図は、料金所ブースに入る車両の台数をカウントする必要があると言い換えることができます。 高速道路の料金所ブースには車両が次々と入ってくるため、これらの入り口イベントは、止まることがない流れに似ています。 この流れを定量化するには、測定する "期間" を定義する必要があります。 質問を "3 分間で料金所ブースに入る車両は何台あるか" に絞り込んでみましょう。 これを一般に "タンブリング カウント" といいます。

   ご覧のとおり、Azure Stream Analytics には SQL に似たクエリ言語が使用されており、さらに、時間に関連したクエリ要素を指定するための拡張機能がいくつか追加されています。  詳細については、クエリで使われる[時間管理](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics)と[時間枠](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics)コンストラクトをご覧ください。

3. TollApp サンプル ジョブの入力を調べます。 現在のクエリでは、EntryStream 入力だけが使用されています。
   - **EntryStream** 入力は、車両が高速道路の料金所ブースに入ったことを表すデータをキュー処理する Event Hub 接続です。 このイベントは、サンプルの一部である Web アプリによって作成され、そのデータが Event Hub でキュー処理されます。 この入力がストリーミング クエリの FROM 句でクエリ処理されることに注意してください。
   - **ExitStream** 入力は、車両が高速道路の料金所ブースから出たことを表すデータをキュー処理する Event Hub 接続です。 このストリーミング入力は、後述するさまざまなクエリの構文で使用されます。
   - **Registration** 入力は、Azure BLOB ストレージ接続であり、静的な registration.json ファイルを指し、必要に応じて検索で使用されます。 この参照データ入力は、後述するさまざまなクエリの構文で使用されます。

4. TollApp サンプル ジョブの出力を調べます。
   - **Cosmos DB** 出力は、出力シンク イベントを受信する Cosmos データベースのコンテナーです。 この出力は、ストリーミング クエリの INTO 句で使用されることに注意してください。

## <a name="start-the-tollapp-streaming-job"></a>TollApp ストリーミング ジョブを開始する
ストリーミング ジョブを開始するには、次の手順に従います。

1. ジョブの **[概要]** ページで、 **[開始]** を選択します。

2. **[ジョブの開始]** ウィンドウで、 **[Now]\(今すぐ\)** を選択します。

3. しばらくしてジョブが実行されたら、ストリーミング ジョブの **[概要]** ページで **[監視]** グラフを表示します。 グラフには、数千の入力イベントと数百の出力イベントが表示されます。

## <a name="review-the-cosmosdb-output-data"></a>CosmosDB 出力データを確認する
1. TollApp リソースが含まれるリソース グループを見つけます。

2. 名前のパターンが **tollapp\<random\>-cosmos** である Azure Cosmos DB アカウントを選択します。

3. **[データ エクスプローラー]** 見出しを選択して、[データ エクスプローラー] ページを開きます。

4. **tollAppDatabase** > **tollAppCollection** > **Documents** を展開します。

5. 出力が使用可能になると、ID の一覧に複数のドキュメントが表示されます。

6. 各 ID を選択して JSON ドキュメントを確認します。 それぞれの TollID、WindowEnd の時刻、およびその時間帯の車両数に注目します。

7. 3 分経過すると、別の 4 つのドキュメント (TollID ごとに 1 つのドキュメント) が利用可能になります。


## <a name="report-total-time-for-each-car"></a>各車両の合計時間を報告する
処理効率と顧客満足を評価できるよう、車両が料金所を通過するのにかかる平均時間を調べるとします。

合計時間を調べるためには、EntryTime ストリームと ExitTime ストリームを結合します。 TollId 列と LicencePlate 列が一致する 2 つの入力ストリームを結合します。 結合したイベントどうしの間隔として許容される時間差を **JOIN** 演算子で指定する必要があります。 **DATEDIFF** 関数を使用して、発生間隔が 15 分以内のイベントに限定することを指定します。 また、通行料金の徴収に費やされた実際の時間を計算するために、ExitTime と EntryTime にも **DATEDIFF** 関数を適用します。 **DATEDIFF** を **SELECT** ステートメントで使用する場合と **JOIN** 条件で使用する場合の使い方の違いに注目してください。

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>TollApp ストリーミング ジョブ クエリの構文を更新するには:

1. ジョブの **[概要]** ページで、 **[停止]** を選択します。

2. ジョブが停止したことを示す通知が表示されるまで、しばらく待ちます。

3. [ジョブ トポロジ] 見出しの下の **[< > クエリ]** を選択します。

4. 調整済みのストリームの SQL クエリを貼り付けます。

5. **[保存]** を選択してクエリを保存します。 **[はい]** を選択して変更を保存します。

6. ジョブの **[概要]** ページで、 **[開始]** を選択します。

7. **[ジョブの開始]** ウィンドウで、 **[Now]\(今すぐ\)** を選択します。

### <a name="review-the-total-time-in-the-output"></a>出力内の合計時間を確認する
前のセクションの手順を繰り返して、ストリーミング ジョブからの CosmosDB 出力データを確認します。 最新の JSON ドキュメントを確認します。

たとえば、次のドキュメントは、特定のナンバー プレートを持つ 1 台の車両の料金所ブースに入った日時 (entrytime)、ブースから出た日時 (exit time) と、DATEDIFF によって計算された 料金所ブースの滞在時間 (durationinminutes) が 2 分間であることを示しています。
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>登録が期限切れになっている車両を報告する
Azure Stream Analytics では、参照データの静的なスナップショットを使用して、テンポラル データ ストリームと結合できます。 この機能のデモとして、次のテストを行います。 Registration 入力は、ライセンス タグの有効期限を示す静的な BLOB json ファイルです。 ナンバー プレートと結合することで、参照データが料金所ブースを通過する各車両と比較されます。

料金徴収会社に登録されている商用車は、検査のために停車しなくても料金所ブースを通過することができます。 登録検索テーブルを使用して、登録の有効期限が切れているすべての商用車を識別します。

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. 前のセクションの手順を繰り返して、TollApp ストリーミング ジョブ クエリの構文を更新します。

2. 前のセクションの手順を繰り返して、ストリーミング ジョブからの CosmosDB 出力データを確認します。

出力例:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>ジョブをスケールアウトする
Azure Stream Analytics は、大量のデータ処理に対応するために、弾力的にスケーリングできるように設計されています。 Azure Stream Analytics クエリで **PARTITION BY** 句を使用して、この手順でスケールアウトすることをシステムに命令できます。**PartitionId** は、システムによって追加される特殊な列で、入力 (イベント ハブ) のパーティション ID と一致します。

クエリをパーティションにスケールアウトするには、次のコードのようにクエリの構文を編集します。
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

ストリーミング ジョブのストリーミング ユニットをスケールアップするには:

1. 現在のジョブを**停止**します。

2. **[< > クエリ]** ページでクエリの構文を更新し、変更を保存します。

3. ストリーミング ジョブの [構成] 見出しで、 **[スケーリング]** を選択します。

4. **[ストリーミング ユニット]** スライダーを 1 から 6 にスライドします。 ストリーミング ユニットは、ジョブが使用できる計算能力の量を定義します。 **[保存]** を選択します。

5. ストリーミング ジョブを**開始**して、スケールが追加されたことを確認します。 Azure Stream Analytics は、より多くのコンピューティング リソースに作業を分散させてスループットの向上を実現し、PARTITION BY 句に指定された列を使用してリソース間で作業をパーティション分割します。

## <a name="monitor-the-job"></a>ジョブの監視
**[監視]** 領域には、実行中のジョブに関する統計情報が表示されます。 ストレージ アカウントを同じリージョン内で使用する場合は、初回の構成が必要です (料金所ブースにこのドキュメントの他の箇所と同じ名前を付けます)。

![Azure Stream Analytics ジョブ監視](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

**アクティビティ ログ**には、ジョブのダッシュボードの **[設定]** 領域からもアクセスできます。

## <a name="clean-up-the-tollapp-resources"></a>TollApp リソースをクリーンアップする
1. Azure Portal で Stream Analytics ジョブを停止します。

2. TollApp テンプレートに関連する 8 つのリソースが含まれているリソース グループを見つけます。

3. **[リソース グループの削除]** を選択します。 リソース グループの名前を入力して削除することを確認します。

## <a name="conclusion"></a>まとめ
このソリューションでは、Azure Stream Analytics サービスについて取り上げました。 デモンストレーションでは、Stream Analytics ジョブの入力と出力の構成方法を説明しました。 イベント データをリアルタイムで扱ううえでの一般的な課題と、Azure Stream Analytics の SQL に似た単純なクエリを使ってそれを解決する方法について、料金所ブースのデータを例に説明しました。 このソリューションでは、テンポラル データを操作するための SQL 拡張機能コンストラクトについて説明しました。 データ ストリームを結合する方法や、データ ストリームの利用価値を静的な参照データで高める方法、クエリをスケールアウトしてスループットを高める方法についても紹介しました。

このソリューションで取り上げた内容はあくまで入門的な位置付けであり、ここでは紹介しきれない情報も数多くあります。 SAQL 言語を使ったその他のクエリ パターンについては、「 [一般的 Stream Analytics 使用状況パターンのクエリ例](stream-analytics-stream-analytics-query-patterns.md)」をご覧ください。
