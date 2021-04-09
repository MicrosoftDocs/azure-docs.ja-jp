---
title: サンプル データを利用した Azure Stream Analytics ジョブのテスト
description: この記事では､Azure ポータルからサンプル入力を利用して Azure Stream Analytics ジョブをテストし､サンプル データをアップロードする方法を説明しています｡
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020045"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>ポータルで Azure Stream Analytics ジョブをテストする

Azure Stream Analytics では、ジョブを開始または停止せずに、クエリをテストできます。 ストリーミング ソースからの受信データ、または Azure portal でローカル ファイルからアップロードしたサンプル データに対するクエリをテストできます。 [Visual Studio](stream-analytics-live-data-local-testing.md) と [Visual Studio Code](visual-studio-code-local-run-live-input.md) でローカル サンプル データまたはライブ データからクエリをローカルでテストすることもできます。

## <a name="automatically-sample-incoming-data-from-input"></a>入力から受信データを自動的にサンプリングする

Azure Stream Analytics では、ストリーミング入力からイベントを自動的にフェッチします。 既定のサンプルに対してクエリを実行したり、サンプルの特定の期間を設定したりすることができます。

1. Azure portal にサインインします。

2. 既存の Stream Analytics ジョブを検索して選択します。

3. [Stream Analytics ジョブ] ページの **ジョブ トポロジ** の見出しの部分から **クエリ** を選択し､クエリ エディター ウィンドウを開きます。 

4. 受信イベントのサンプル一覧を表示するには、ファイルを含む入力アイコンを選択すると、サンプル イベントが **入力プレビュー** に自動的に表示されます。

   a. ご利用のデータに対するシリアル化の種類では、JSON と CSV のいずれであるかが自動的に検出されます。 ドロップダウン メニューのオプションを変更することで、これを JSON、CSV、AVRO に手動で変更することもできます。
    
   b. セレクターを使用して、ご利用のデータを **表** または **未加工** の形式で表示します。
    
   c. 表示されたデータが最新でない場合、 **[更新]** を選択して最新のイベントを表示します。

   次の表は、**表形式** でのデータの例です。

   ![表形式の Azure Stream Analytics のサンプル入力](./media/stream-analytics-test-query/asa-sample-table.png)

   次の表は、**未加工の形式** でのデータの例です。

   ![未加工の形式の Azure Stream Analytics のサンプル入力](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 受信データを使用してクエリをテストするには、 **[クエリのテスト]** を選択します。 **[テスト結果]** タブに結果が表示されます。 **[ダウンロードの結果]** を選択して、結果をダウンロードすることもできます。

   ![Azure Stream Analytics サンプルのテスト クエリの結果](./media/stream-analytics-test-query/asa-test-query.png)

6. 特定の時間範囲の受信イベントに対してクエリをテストするには、 **[時間範囲の選択]** を選択します。
   
   ![Azure Stream Analytics の受信サンプル イベントに対する時間の範囲](./media/stream-analytics-test-query/asa-select-time-range.png)

7. クエリのテストに使用するイベントの時間範囲を設定し、 **[サンプル]** を選択します。 この期間内に、最大 1000 個のイベントまたは 1 MB まで取得できます (どちらか先の方)。

   ![Azure Stream Analytics の受信サンプル イベントに対する時間の範囲の設定](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 選択した時間範囲のイベントがサンプリングされると、 **[入力のプレビュー]** タブに表示されます。

   ![Azure Stream Analytics ビューのテスト結果](./media/stream-analytics-test-query/asa-view-test-results.png)

9. 受信イベントのサンプル一覧を表示するには、 **[リセット]** を選択します。 **[リセット]** を選択すると、時間範囲の選択は失われます。 **[クエリのテスト]** を選択してクエリをテストし、 **[テスト結果]** タブで結果を確認します。

10. クエリに変更を加える場合は、 **[クエリの保存]** を選択して新しいクエリ ロジックをテストします。 これにより、クエリを繰り返し変更して、もう一度テストし、出力がどのように変化するかを確認することができます。

11. ブラウザに示された結果を確認したら、ジョブを **開始** できます。

## <a name="upload-sample-data-from-a-local-file"></a>ローカル ファイルからサンプル データをアップロードする

ライブ データを使用する代わりに、ローカル ファイルのサンプル データを使用して、Azure Stream Analytics クエリをテストできます。

1. Azure portal にサインインします。
   
2. Stream Analytics ジョブがある場所を探し､ジョブを選択します｡

3. [Stream Analytics ジョブ] ページの **ジョブ トポロジ** の見出しの部分から **クエリ** を選択し､クエリ エディター ウィンドウを開きます。

4. ローカル ファイルでクエリをテストするには、 **[入力のプレビュー]** タブで **[Upload sample input]\(サンプル入力のアップロード\)** を選択します。 

   ![スクリーンショットに、[サンプル データのアップロード] 入力オプションが示されています。](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. ローカル ファイルをアップロードしてクエリをテストします。 ファイルは JSON、CSV、または AVRO 形式でのみアップロードできます。 **[OK]** を選択します。

   ![スクリーンショットに、[サンプル データのアップロード] ダイアログ ボックスが示され、ここでファイルを選択できます。](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. ファイルをアップロードするとすぐに、フォーム内のファイルの内容を表形式または未加工の形式で表示することもできます。 **[リセット]** を選択した場合、サンプル データは前のセクションで説明した受信した入力データに戻ります。 他のファイルをアップロードして、いつでもクエリをテストできます。

7. アップロードされたサンプル ファイルに対してクエリをテストするには、 **[クエリのテスト]** を選択します。

8. テスト結果は、クエリに基づいて表示されます。 クエリを変更して **[クエリの保存]** を選択すると、新しいクエリ ロジックをテストできます。 これにより、クエリを繰り返し変更して、もう一度テストし、出力がどのように変化するかを確認することができます。

9. クエリで複数の出力を使用すると、選択した出力に基づいて結果が表示されます。 

   ![Azure Stream Analytics で選択した出力](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. ブラウザに示された結果を確認したら、ジョブを **開始** できます。

## <a name="limitations"></a>制限事項

1.  タイム ポリシーは、以下でテストするポータルではサポートされていません。

    * 順不同: すべての受信イベントは順序付けられます。
    * 到着遅延:Stream Analytics は既存のデータのみをテストに使用できるため、到着遅延イベントは発生しません。
   
2.  C# UDF はサポートされていません。

3.  すべてのテストは、1 つのストリーミング ユニットを持つジョブにより実行されます。

4.  タイムアウト サイズは 1 分です。 したがって、ウィンドウ サイズが 1 分を超えるクエリではデータを取得できません。

5.  機械学習はサポートされていません。

6. サンプル データ API は、15 分のウィンドウで 5 つの要求が行われた後にスロットルされます。 15 分のウィンドウが終了すると、さらに多くのサンプル データ要求を行うことができます。 この制限は、サブスクリプション レベルで適用されます。

## <a name="troubleshooting"></a>トラブルシューティング

1.  エラー “結果を取り込むときにネットワーク接続の問題が発生しました。 ネットワークとファイアウォールの設定を確認してください。” が発生した場合は、次の手順に従ってください。

  * サービスへの接続を確認するには、ブラウザーで [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) を開きます。 このリンクを開くことができない場合は、ファイアウォールの設定を更新してください。
  
2. エラー "要求サイズが大きすぎます。 入力データ サイズを減らして、再試行してください。" が発生した場合は、次の手順に従ってください。

  * 入力サイズを減らす – 小さいサイズのサンプル ファイル、または短い時間範囲でクエリをテストします。
  * クエリ サイズを減らす – クエリの選択をテストするには、クエリの一部を選択し、 **[Test selected query]\(選択したクエリをテストする\)** をクリックします。


## <a name="next-steps"></a>次のステップ
* [Stream Analytics を使って IoT ソリューションを構築する](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): このチュートリアルでは、料金所での交通情報をシミュレートするデータ ジェネレーターを使用してエンドツーエンドのソリューションを構築する方法について説明します。

* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)

* [一般的 Stream Analytics 使用状況パターンのクエリ例](stream-analytics-stream-analytics-query-patterns.md)

* [Azure Stream Analytics の入力について](stream-analytics-add-inputs.md)

* [Azure Stream Analytics からの出力を理解する](stream-analytics-define-outputs.md)
