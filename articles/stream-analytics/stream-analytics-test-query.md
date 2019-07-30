---
title: サンプル データを利用した Azure Stream Analytics ジョブのテスト
description: この記事では､Azure ポータルからサンプル入力を利用して Azure Stream Analytics ジョブをテストし､サンプル データをアップロードする方法を説明しています｡
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: ad0e0ca75bf3d3a8d9d1029d42f8609b3c4c627b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620827"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>サンプル データを利用した Stream Analytics クエリのテスト

Azure Stream Analytics を使用すると、ジョブの起動や停止をすることなく、入力からデータをサンプリングしたり、サンプル データをアップロードして Azure portal でクエリをテストしたりすることができます。

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>ライブ ソースのデータをアップロードまたはサンプリングしてクエリをテストする

1. Azure ポータルにサインインします。 

2. Stream Analytics ジョブがある場所を探し､ジョブを選択します｡

3. [Stream Analytics ジョブ] ページの **ジョブ トポロジ** の見出しの部分から **クエリ** を選択し､クエリ エディター ウィンドウを開きます。 

4. クエリをテストするには、ライブ入力からデータをサンプリングするか、ファイルからアップロードします。 データは、JSON、CSV または AVRO でシリアル化する必要があります。 サンプル入力は、UTF-8 でエンコードされていて、圧縮されていない必要があります。 ポータルでの CSV 入力のテストでは、コンマ (,) 区切り記号のみがサポートされています。

    1. ライブ入力を使用する: 入力を右クリックします。 次に、 **[入力からのサンプル データ]** を選択します。 次の画面では、サンプルの期間を設定できます。 ライブ ソースからイベントをサンプリングすると、最大 1000 イベントまたは 1 MB (どちらか早い方) まで取得されるので、サンプリングされたデータは、指定した全期間を表していない可能性があります。

    1. ファイルを使用する: 入力のいずれかを右クリックします。 **ファイルからサンプルデータをアップロード** を選択します｡ 

    ![Stream Analytics クエリ エディターでクエリをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. サンプリングまたはアップロードが完了したら、 **[テスト]** を選択して、用意したサンプル データでこのクエリをテストします。

    ![Stream Analytics クエリ エディターでサンプル データをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. テストの出力を保存して後で利用できるよう、ブラウザーに表示されるクエリの出力には、ダウンロード結果へのリンクが含まれています｡ 

7. クエリの変更とテストを繰り返して､出力がどのように変化するかを確認します｡

   ![Stream Analytics クエリ エディターのサンプル出力](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   1 つのクエリで複数の出力を使用すると､結果は出力ごとに別のタブに表示されますが､タブは簡単に切り替えることができます｡

8. ブラウザで結果を確認したら､クエリを **保存** します｡ ジョブを **Start** します｡外部からのイベントがジョブによって処理されます｡

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
