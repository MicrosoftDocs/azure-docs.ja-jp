---
title: Azure Stream Analytics との Power BI ダッシュボードの統合
description: この記事では、リアルタイムの Power BI ダッシュボードを使って、Azure Stream Analytics ジョブからデータを視覚化する方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: c415bdecdaf55f3068dcd804ab34de402fe7a31f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612286"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics と Power BI:ストリーミング データのリアルタイム分析ダッシュボード

Azure Stream Analytics では、主要なビジネス インテリジェンス ツールの 1 つである [Microsoft Power BI](https://powerbi.com/) を利用することができます。 この記事では、Azure Stream Analytics ジョブの出力として Power BI を使ってビジネス インテリジェンス ツールを作成する方法について説明します。 リアルタイム ダッシュボードを作って使う方法についても説明します。

この記事は、Stream Analytics による[リアルタイムでの不正検出](stream-analytics-real-time-fraud-detection.md)に関するチュートリアルに続くものです。 この記事では、前のチュートリアルで作成したワークフローに Power BI の出力を追加し、Streaming Analytics ジョブによって検出された不正な電話を視覚化できるようにします。 

[このシナリオを紹介する動画](https://www.youtube.com/watch?v=SGUpT-a99MA)をご覧ください。


## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意してください。

* Azure アカウント。
* Power BI 用のアカウント。 職場アカウントまたは学校アカウントを使うことができます。
* 「[リアルタイムでの不正検出](stream-analytics-real-time-fraud-detection.md)」チュートリアルの完成バージョン。 このチュートリアルには、架空の通話メタデータを生成するアプリが含まれています。 このチュートリアルでは、Event Hub を作成し、ストリーミング通話データを Event Hub に送信します。 不正な呼び出し (別の場所で同時に同じ番号からの呼び出し) を検出するクエリを記述します。 


## <a name="add-power-bi-output"></a>Power BI の出力を追加する
リアルタイム不正検出チュートリアルでは、出力は Azure BLOB ストレージに送信されます。 このセクションでは、Power BI に情報を送信する出力を追加します。

1. 前に作成した Streaming Analytics ジョブを Azure Portal で開きます。 推奨される名前を使った場合、ジョブの名前は `sa_frauddetection_job_demo` になっています。

2. 左側のメニューで、 **[ジョブ トポロジ]** の下にある **[出力]** を選択します。 次に、 **[+ 追加]** を選択し、ドロップダウン メニューから **[Power BI]** を選択します。

3. **[+ 追加]**  >  **[Power BI]** の順に選択します。 次に、フォームに次の詳細を入力して、 **[承認する]** を選択します。

   |**設定**  |**推奨値**  |
   |---------|---------|
   |出力エイリアス  |  CallStream-PowerBI  |
   |データセットの名前  |   sa-dataset  |
   |テーブル名 |  fraudulent-calls  |

   ![Stream Analytics の出力を構成する](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に存在する場合は、既存のデータが上書きされます。
   > このデータセットとテーブルは Power BI アカウントに明示的に作成しないことをお勧めします。 Stream Analytics ジョブを開始すると自動的に作成され、Power BI への出力が開始されます。 ジョブ クエリで結果が返されない場合、データセットとテーブルは作成されません。
   >

4. **[承認する]** を選択すると、ポップアップ ウィンドウが開き、Power BI アカウントに対する認証のための資格情報を入力するよう求められます。 認可が成功したら、設定を**保存**します。

8. **Create** をクリックしてください。

データセットは、次の設定で作成されます。

* **defaultRetentionPolicy:BasicFIFO** - データは FIFO で、最大行数は 200,000 です。
* **defaultMode: pushStreaming** - データセットは、ストリーミング タイルと従来のレポートベース ビジュアル (プッシュとも呼ばれます) の両方をサポートしています。

現時点では、他のフラグでデータセットを作成することはできません。

Power BI データセットの詳細については、[Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) リファレンスを参照してください。


## <a name="write-the-query"></a>クエリを作成する

1. **[出力]** ブレードを閉じて、ジョブ ブレードに戻ります。

2. **[クエリ]** ボックスをクリックします。 

3. 次のクエリを入力します。 このクエリは、不正検出チュートリアルで作成した自己結合クエリに似ています。 違うのは、このクエリは作成した新しい出力 (`CallStream-PowerBI`) に結果を送信することです。 

    >[!NOTE]
    >不正検出チュートリアルで入力 `CallStream` の名前を指定しなかった場合は、クエリの **FROM** 句と **JOIN** 句の `CallStream` を実際の名前に置き換えます。

   ```SQL
   /* Our criteria for fraud:
   Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "CallStream-PowerBI"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

   /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
   ON CS1.CallingIMSI = CS2.CallingIMSI

   /* ...and date between CS1 and CS2 is between one and five seconds */
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

   /* Where the switch location is different */
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. **[Save]** をクリックします。


## <a name="test-the-query"></a>クエリをテストする

このセクションは省略可能ですが、実施することをお勧めします。 

1. TelcoStreaming アプリが現在動いていない場合は、次の手順で開始します。

    * コマンド プロンプトを開きます。
    * telcogenerator.exe ファイルと変更された telcodatagen.exe.config ファイルが存在するフォルダーに移動します。
    * 次のコマンドを実行します。

       `telcodatagen.exe 1000 .2 2`

2. ご利用の Stream Analytics ジョブの **[クエリ]** ページで、`CallStream` 入力の横の点をクリックしてから、 **[入力からのサンプル データ]** を選択します。

3. 3 分間分のデータを指定して、 **[OK]** をクリックします。 データがサンプリングされたことが通知されるまで待ちます。

4. **[テスト]** をクリックして結果を確認します。

## <a name="run-the-job"></a>ジョブを実行する

1. TelcoStreaming アプリが実行されていることを確認します。

2. ご利用の Stream Analytics ジョブの **[概要]** ページに移動して、 **[開始]** を選択します。

    ![Stream Analytics ジョブの開始](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

Streaming Analytics ジョブが、受信ストリームでの不正な呼び出しの検出を開始します。 また、ジョブは、Power BI にデータセットとテーブルを作成し、それらへの不正な呼び出しに関するデータの送信を開始します。


## <a name="create-the-dashboard-in-power-bi"></a>Power BI でのダッシュボードの作成

1. [Powerbi.com](https://powerbi.com) にアクセスして、職場または学校のアカウントでサインインします。 Stream Analytics ジョブ クエリで結果が出力された場合は、データセットが既に作成されていることがわかります。

    ![Power BI でのストリーミング データセットの場所](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. ワークスペースで **[+&nbsp;作成]** をクリックします。

    ![Power BI ワークスペースの [作成] ボタン](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. 新しいダッシュボードを作成し、名前を「`Fraudulent Calls`」にします。

    ![Power BI ワークスペースでダッシュボードを作成して名前を付ける](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. ウィンドウの上部にある **[タイルの追加]** をクリックし、 **[カスタム ストリーミング データ]** を選んで、 **[次へ]** をクリックします。

    ![Power BI でのカスタムのストリーミング データセットのタイル](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. **[データセット]** でデータセットを選んで、 **[次へ]** をクリックします。

    ![Power BI でのストリーミング データセット](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. **[視覚化タイプ]** で **[カード]** を選び、 **[フィールド]** の一覧で **[fraudulentcalls]** を選びます。

    ![新しいタイルの視覚化の詳細](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. **[次へ]** をクリックします。

8. タイトルやサブタイトルなどのタイルの詳細を入力します。

    ![新しいタイルのタイトルとサブタイトル](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. **[Apply]** をクリックします。

    不正行為カウンターができました。

    ![Power BI ダッシュボードの不正行為カウンター](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. 手順 4 以降を繰り返してタイルを追加します。 今度は次のようにします。

    * **[視覚化タイプ]** では、 **[折れ線グラフ]** を選びます。 
    * 軸を追加し、 **[windowend]** を選びます。 
    * 値を追加し、 **[fraudulentcalls]** を選びます。
    * **[表示する時間枠]** で、過去 10 分間を選びます。

      ![Power BI で折れ線グラフのタイルを作成する](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. **[次へ]** をクリックし、タイトルとサブタイトルを追加して、 **[適用]** をクリックします。

     Power BI ダッシュボードに、ストリーミング データで検出された不正な呼び出しに関するデータの 2 つのビューが表示されるようになりました。

     ![不正な呼び出しに関する 2 つのタイルが表示されている完成した Power BI ダッシュボード](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>Power BI についての詳細情報

このチュートリアルで紹介しているのは、データセットに関するほんの数種類の視覚エフェクトの作成方法です。 Power BI は、組織の他の顧客のビジネス インテリジェンス ツールを作成するのに役立ちます。 他のアイデアについては、次のリソースを参照してください。

* Power BI ダッシュボードの別の例については、[Power BI の概要](https://youtu.be/L-Z_6P56aas?t=1m58s)ビデオをご覧ください。
* Power BI に対する Streaming Analytics ジョブ出力の構成および Power BI グループの使用について詳しくは、「[Stream Analytics の出力](stream-analytics-define-outputs.md)」記事の「[Power BI](stream-analytics-define-outputs.md#power-bi)」セクションをご覧ください。 
* Power BI の一般的な使い方については、「[Power BI サービスのダッシュボード](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)」をご覧ください。


## <a name="learn-about-limitations-and-best-practices"></a>制限事項とベスト プラクティスについて
現在、Power BI は、およそ 1 秒に 1 回呼び出すことができます。 ストリーミング ビジュアルでは、15 KB のパケットがサポートされます。 これを超えると、ストリーミング ビジュアルは失敗します (ただし、プッシュは動作し続けます)。 このような制限から必然的に、Power BI は、Azure Stream Analytics で大幅なデータ負荷の低減が見られるケースへと落ち着きます。 1 秒あたりのデータ プッシュを 1 回以内に抑え、かつスループットの要件の範囲内にクエリを抑えるために、タンブリング ウィンドウやホッピング ウィンドウの使用をお勧めします。

必要な期間 (秒) の値は、次の数式を使用して計算することができます。

![必要な期間 (秒) の値を計算するための数式](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

例:

* 1 秒間隔でデータを送信するデバイスが 1,000 台。
* 1 時間あたり 1,000,000 行をサポートする Power BI Pro SKU を使用。
* デバイスごとの平均データ量を Power BI に発行する。

これを式に当てはめると次のようになります。

![例の条件に基づく式](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

この構成では、最初のクエリを次のように変更できます。

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>承認の更新
ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Power BI アカウントを再認証する必要があります。 また、Azure Active Directory (Azure AD) テナント上で Azure Multi-Factor Authentication が構成されている場合は、Power BI の承認を 2 週間ごとに更新する必要があります。 更新しなかった場合、ジョブが出力されなかったり、操作ログに "`Authenticate user error`" が記録されたりする現象が生じる可能性があります。

同様に、トークンの期限が切れた後でジョブが開始すると、エラーが発生し、ジョブは失敗します。 この問題を解決するには、実行中のジョブを停止し、Power BI 出力に移動します。 データの損失を避けるには、 **[承認の更新]** リンクを選択し、 **[最後に停止した時刻]** からジョブを再開します。

Power BI で承認が更新されると、承認の領域に緑色のアラートが表示され、問題が解決されたことがわかります。

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics クエリ言語リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics の管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)
