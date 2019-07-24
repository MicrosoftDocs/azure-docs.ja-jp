---
title: サンプル データを利用した Azure Stream Analytics ジョブのテスト
description: この記事では､Azure ポータルからサンプル入力を利用して Azure Stream Analytics ジョブをテストし､サンプル データをアップロードする方法を説明しています｡
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fca76b632e9bcc27ed762886eaea696a5696ad3f
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557634"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>サンプル データを利用した Stream Analytics クエリのテスト

Azure Stream Analytics を使用すると、ジョブの起動や停止をすることなく、Azureポータルでサンプル データのアップロードと クエリのテストができます。

## <a name="upload-sample-data-and-test-the-query"></a>サンプル データのアップロードとクエリのテスト

1. Azure ポータルにサインインします。 

2. Stream Analytics ジョブがある場所を探し､ジョブを選択します｡

3. [Stream Analytics ジョブ] ページの **ジョブ トポロジ** の見出しの部分から **クエリ** を選択し､クエリ エディター ウィンドウを開きます。 

4. サンプルの入力データを使ってクエリをテストするには､入力のいずれかを右クリックします｡  **ファイルからサンプルデータをアップロード** を選択します｡ データは、JSON、CSV または AVRO でシリアル化する必要があります。 サンプル入力は、UTF-8 でエンコードされていて、圧縮されていない必要があります。 ポータルでの CSV 入力のテストでは、コンマ (,) 区切り記号のみがサポートされています。

    ![Stream Analytics クエリ エディターでクエリをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. アップロードが完了したら、**[テスト]** を選択して、用意したサンプル データでこのクエリをテストします。

    ![Stream Analytics クエリ エディターでサンプル データをテストする](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. テストの出力を保存して後で利用できるよう、ブラウザーに表示されるクエリの出力には、ダウンロード結果へのリンクが含まれています｡ 

7. クエリの変更とテストを繰り返して､出力がどのように変化するかを確認します｡

   ![Stream Analytics クエリ エディターのサンプル出力](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   1 つのクエリで複数の出力を使用すると､結果は出力ごとに別のタブに表示されますが､タブは簡単に切り替えることができます｡

8. ブラウザで結果を確認したら､クエリを **保存** します｡ ジョブを **Start** します｡外部からのイベントがジョブによって処理されます｡

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
