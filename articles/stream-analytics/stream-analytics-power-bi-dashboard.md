---
title: "Azure Stream Analytics の Power BI ダッシュボード | Microsoft Docs"
description: "リアルタイム ストリーミング Power BI ダッシュボードを使用して、ビジネス インテリジェンスを収集して Stream Analytics ジョブからの大量のデータを分析します。"
keywords: "分析ダッシュボード、リアルタイム ダッシュボード"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3dbc49d35def6d7b12ade529d1dd1156dee9d75b
ms.openlocfilehash: 09c54f8cce119c1cbe6a08e969236612447d9e17
ms.lasthandoff: 02/27/2017


---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics と Power BI: ストリーミング データのリアルタイム分析ダッシュボード
Azure Stream Analytics では、主要なビジネス インテリジェンス ツールの&1; つである Microsoft Power BI を利用することができます。 ここでは、Azure Stream Analytics を使用して、大量のストリーミング データを分析し、リアルタイム Power BI 分析ダッシュボードで確認する方法を説明します。

[Microsoft Power BI](https://powerbi.com/) を使用すると、ライブ ダッシュボードがすぐに作成されます。 [このシナリオを紹介する動画](https://www.youtube.com/watch?v=SGUpT-a99MA)をご覧ください。

この記事では、Azure Stream Analytics ジョブの出力として Power BI を使用して独自のカスタム ビジネス インテリジェンス ツールを作成する方法について説明します。 リアルタイム ダッシュボードを利用する方法についても説明します。

## <a name="prerequisites"></a>前提条件
* Microsoft Azure アカウント。
* Power BI の職場アカウントまたは学校アカウント。
* 「[リアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)」の作業を実施済みであること。 本記事は、「[リアルタイムの不正行為の検出](stream-analytics-real-time-fraud-detection.md)」で説明されているワークフローをベースとして、Power BI のストリーミング データセット出力に関する情報を補足する形式で執筆されています。

## <a name="add-power-bi-output"></a>Power BI 出力の追加
ジョブの入力が存在するため、Power BI への出力を定義できます。

1. ジョブ ダッシュボードの中央にある **[出力]** ボックスを選択します。 次に、**[+ 追加]** を選択して目的の出力を作成します。

    ![出力を追加する](./media/stream-analytics-power-bi-dashboard/create-pbi-output.png)

2. **[出力のエイリアス]** を指定します。 参照しやすいように出力のエイリアスを設定できます。 この出力のエイリアスは、ジョブに複数出力を設定する場合に便利です。 その場合、クエリ内でこの出力を参照することができます。 たとえば、出力エイリアス値 = "StreamAnalyticsRealTimeFraudPBI" を使用しましょう。

3. **[承認]** を選択します。

    ![承認を追加する](./media/stream-analytics-power-bi-dashboard/pbi-authorize.png)

4. Azure の資格情報 (職場または学校アカウント) を指定するためのウィンドウが開きます。 この資格情報によって、Power BI 領域へのアクセス権が Azure ジョブにも付与されます。

    ![フィールドを承認する](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

5. 必要な情報を指定すると承認画面が消えます。 **[新規出力]** 領域には、**[データセット名]** フィールドと **[テーブル名]** フィールドがあります。

    ![PBI ワークスペース](./media/stream-analytics-power-bi-dashboard/pbi-workspace.png)

6. フィールドは次のように定義します。
    * **[グループ ワークスペース]**: データセットの作成先となる Power BI テナントのワークスペースを選択します。
    * **[データセット名]**: Power BI 出力に設定するデータセット名を入力します。 たとえば、"StreamAnalyticsRealTimeFraudPBI" を使用しましょう。
    * **[テーブル名]**: Power BI 出力のデータセットの下にテーブル名を入力します。 たとえば、"StreamAnalyticsRealTimeFraudPBI" を使用しましょう。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に&1; つのテーブルのみを保持できます。

7. **[作成]**を選択します。 出力の構成はこれで完了です。

> [!WARNING]
> この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされます。
> なお、このデータセットとテーブルは Power BI アカウントに明示的に作成しないことをお勧めします。 Stream Analytics ジョブを開始すると自動的に作成され、Power BI への出力が開始されます。 ジョブ クエリで結果が返されない場合、データセットとテーブルは作成されません。
>
>

データセットは、次の設定で作成されます。
* **defaultRetentionPolicy: BasicFIFO**: データは FIFO で、最大行数は 200,000 です。
* **defaultMode: pushStreaming**: ストリーミング タイルと従来のレポートベース ビジュアル (プッシュ) の両方をサポートしています。

現時点では、他のフラグでデータセットを作成することはできません。

Power BI データセットの詳細については、[Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) リファレンスを参照してください。


## <a name="write-query"></a>クエリの記述
ジョブの **[クエリ]** タブに移動します。 Power BI で目的の出力を得るためのクエリを記述します。 たとえば、次の SQL クエリのようなクエリを記述して、電気通信業界で SIM 不正行為をキャッチします。


```
/* Our criteria for fraud:
 Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

 SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
 INTO "StreamAnalyticsRealTimeFraudPBI"
 FROM "StreamAnalyticsRealTimeFraudInput" CS1 TIMESTAMP BY CallRecTime
 JOIN "StreamAnalyticsRealTimeFraudInput" CS2 TIMESTAMP BY CallRecTime

/* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
 ON CS1.CallingIMSI = CS2.CallingIMSI

/* ...and date between CS1 and CS2 is between one and five seconds */
 AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

/* Where the switch location is different */
 WHERE CS1.SwitchNum != CS2.SwitchNum
 GROUP BY TumblingWindow(Duration(second, 1))
```

## <a name="create-the-dashboard-in-power-bi"></a>Power BI でのダッシュボードの作成

1. [Powerbi.com](https://powerbi.com) にアクセスして、職場または学校のアカウントでサインインします。 Stream Analytics ジョブ クエリで結果が出力された場合は、データセットが既に作成されていることがわかります (下図参照)。

    ![ストリーミング データセット](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. **[タイルの追加]** を選択し、カスタム ストリーミング データを選択します。

    ![カスタム ストリーミング データセット](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

3. 次に、一覧からデータセットを選択します。

    ![カスタム ストリーミング データセット](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

4. 視覚化カードを作成します。 そのうえで **[fraudulentcalls]** フィールドを選択してください。

    ![不正行為を追加する](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

    不正行為カウンターができました。

    ![不正行為カウンター](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

5. タイルを再度追加します。 ただし今回選択するのは折れ線グラフです。 値として **[fraudulentcalls]** を、軸として **[windowend]** を追加します。 次のスクリーンショットに示したように、ここでは過去 10 分を選択しました。

![不正行為の呼び出し](./media/stream-analytics-power-bi-dashboard/fraud-calls.png)


このチュートリアルで紹介している方法では、1 つのデータセットに対して作成しているグラフは&1; 種類だけです。 Power BI は、組織の他の顧客のビジネス インテリジェンス ツールを作成するのに役立ちます。 Power BI ダッシュボードの別の例については、[Power BI の概要](https://youtu.be/L-Z_6P56aas?t=1m58s)ビデオをご覧ください。

Power BI 出力の構成と Power BI グループの利用の詳細については、[Stream Analytics 出力について](stream-analytics-define-outputs.md "Stream Analytics 出力について")のページにある「[Power BI](stream-analytics-define-outputs.md#power-bi)」セクションをご覧ください。 また、「[Power BI のダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)」もぜひ参考にしてください。

## <a name="learn-about-limitations-and-best-practices"></a>制限事項とベスト プラクティスについて
Power BI には、同時実行性とスループットに関して制約があります。[Power BI に関するこちらのページ](https://powerbi.microsoft.com/pricing "Power BI の価格")をご覧ください。

現在、Power BI は、およそ&1; 秒に&1; 回呼び出すことができます。 ストリーミング ビジュアルでは、15 KB のパケットがサポートされます。 これを超えると、ストリーミング ビジュアルは失敗します (ただし、プッシュは動作し続けます)。

このような制限から必然的に、Power BI は、Azure Stream Analytics で大幅なデータ負荷の低減が見られるケースへと落ち着きます。
1 秒あたりのデータ プッシュを&1; 回以内に抑え、かつスループットの要件の範囲内にクエリを抑えるために、タンブリング ウィンドウやホッピング ウィンドウの使用をお勧めします。

必要な期間 (秒) の値は、次の数式を使用して計算することができます。

![式&1;](./media/stream-analytics-power-bi-dashboard/equation1.png)  

For example:
- 1 秒間隔でデータを送信するデバイスが 1,000 台。
- 1 時間あたり 1,000,000 行をサポートする Power BI Pro SKU を使用。
- デバイスごとの平均データ量を Power BI に発行する。

これを式に当てはめると次のようになります。

![式&2;](./media/stream-analytics-power-bi-dashboard/equation2.png)  

つまり、最初のクエリは次のように変更できます。

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl


### <a name="renew-authorization"></a>承認の更新
ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Power BI アカウントを再認証する必要があります。 また、Azure Active Directory (Azure AD) テナント上で Azure Multi-Factor Authentication が構成されている場合は、Power BI の承認を&2; 週間ごとに更新する必要があります。 更新しなかった場合、ジョブが出力されなかったり、操作ログに "ユーザー認証エラー" が記録されたりする現象が生じる可能性があります。

同様に、トークンの期限が切れた後に、ジョブを開始しようとすると、エラーが発生し、ジョブは開始できません。 この問題を解決するには、実行中のジョブを停止し、Power BI 出力に移動します。 データの損失を避けるには、**[承認の更新]** リンクを選択し、**[最後に停止した時刻]** からジョブを再開します。

Power BI で承認が更新されると、承認の領域に緑色のアラートが表示され、問題が解決されたことがわかります。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics の管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)

