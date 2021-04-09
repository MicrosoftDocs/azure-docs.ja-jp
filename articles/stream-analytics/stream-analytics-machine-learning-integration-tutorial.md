---
title: Azure Stream Analytics と Azure Machine Learning Studio (classic) の統合
description: この記事では、ユーザー定義関数を使用して、Azure Machine Learning Studio (classic) を統合する単純な Azure Stream Analytics ジョブをすばやく設定する方法について説明します。
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 1ebe62c1b90e09b36dd75b5bda4054cca08d5759
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441212"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Azure Stream Analytics と Azure Machine Learning Studio (classic) を使用した感情分析の実行

この記事では、感情分析に Azure Machine Learning Studio (classic) を使用する単純な Azure Stream Analytics ジョブを設定する方法について説明します。 Cortana Intelligence Gallery の Studio (classic) の感情分析モデルを利用して、ストリーミング テキスト データを分析し、センチメント スコアを決定します。

> [!TIP]
> パフォーマンスと信頼性を向上させるために、Azure Machine Learning Studio (classic) UDF の代わりに [Azure Machine Learning UDF](machine-learning-udf.md) を使用することを強くお勧めします。

この記事で学ぶ内容は、次のようなシナリオに適用できます。

* ストリーミング Twitter データに関するリアルタイムの感情分析。
* サポート スタッフと顧客とのチャット レコード分析。
* フォーラム、ブログ、ビデオに対するコメントの評価。
* その他多くのリアルタイムの予測スコアリング シナリオ。

作成する Stream Analytics ジョブは、BLOB ストアのサンプル テキスト データに対して、ユーザー定義関数 (UDF) として感情分析モデルを適用します。 出力 (感情分析の結果) は、同じ BLOB ストアの別の CSV ファイル内に書き込まれます。 

## <a name="prerequisites"></a>前提条件

始める前に、以下のものを用意してください。

* 有効な Azure サブスクリプション

* ある程度の Twitter データが入力された CSV ファイル。 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) からサンプル ファイルをダウンロードすることも、独自のファイルを作成することもできます。 現実のシナリオなら、Twitter データ ストリームから直接データを取得するでしょう。

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Storage コンテナーを作成して CSV 入力ファイルをアップロードする

この手順では、CSV ファイルをストレージ コンテナーにアップロードします。

1. Azure portal で、 **[リソースの作成]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** を選択します。

2. *[基本]* タブに次の詳細情報を入力し、残りのフィールドの既定値はそのままにします。

   |フィールド  |値  |
   |---------|---------|
   |サブスクリプション|サブスクリプションを選択します。|
   |Resource group|リソース グループを選択します。|
   |ストレージ アカウント名|ストレージ アカウントの名前を入力します。 この名前は Azure 全体で一意である必要があります。|
   |場所|場所を選択します。 すべてのリソースで同じ場所を使用する必要があります。|
   |アカウントの種類|BlobStorage|

   ![ストレージ アカウントの詳細を入力する](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. **[確認および作成]** を選択します。 次に、 **[作成]** を選択してストレージ アカウントをデプロイします。

4. デプロイが完了したら、ご自分のストレージ アカウントに移動します。 **[Blob service]** で **[コンテナー]** を選択します。 次に、 **[+ コンテナー]** を選択して、新しいコンテナーを作成します。

   ![入力用の BLOB ストレージ コンテナーを作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. コンテナーの名前を指定し、 **[パブリック アクセス レベル]** が **[非公開]** に設定されていることを確認します。 完了したら **[作成]** を選択します。

   ![BLOB コンテナーの詳細を指定する](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. 新しく作成したコンテナーに移動し、 **[アップロード]** を選択します。 先ほどダウンロードした **sampleinput.csv** ファイルをアップロードします。

   ![コンテナーの [アップロード] ボタン](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーからセンチメント分析モデルを追加する

サンプル データが BLOB に含まれたので、Cortana Intelligence ギャラリーの感情分析モデルを有効にできます。

1. Cortana Intelligence ギャラリーの[予測感情分析モデル](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)のページに進みます。  

2. **[Open in Studio (classic)]\(Studio (classic) で開く\)** を選択します。  
   
   ![Stream Analytics Azure Machine Learning Studio (classic)、Studio (classic) を開く](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. サインインしてワークスペースを開きます。 場所を選択します。

4. ページの下部にある **[実行]** を選択します。 プロセスが実行され、約 1 分かかります。

   ![Studio (classic) で実験を実行する](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. プロセスが正常に実行されたら、ページの下部にある **[Web サービスのデプロイ]** を選択します。

   ![Web サービスとして Studio (classic) で実験をデプロイする](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 感情分析モデルが使用できる状態か検証するには、 **[テスト]** ボタンを選択します。 「I love Microsoft」などのテキストを入力します。

   ![Studio (classic) でのテスト実験](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   テストが正常に機能すると、次の例のような結果が表示されます。

   ![Studio (classic) でのテスト結果](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. **[アプリ]** 列で、 **[Excel 2010 or earlier workbook]\(Excel 2010 以前のブック\)** リンクを選択して、Excel ブックをダウンロードします。 このブックには、後で Stream Analytics ジョブをセットアップする際に必要になる API キーと URL が含まれています。

    ![Stream Analytics Azure Machine Learning Studio (classic)、概要](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>Studio (classic) モデルを使用する Stream Analytics ジョブを作成する

続いて、サンプル ツイートを BLOB ストレージ内の CSV ファイルから読み取る Stream Analytics ジョブを作成できます。

### <a name="create-the-job"></a>ジョブを作成する

[Azure portal](https://portal.azure.com) にアクセスして、Stream Analytics ジョブを作成します。 このプロセスに慣れていない場合は、「[Azure portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)」を参照してください。

### <a name="configure-the-job-input"></a>ジョブの入力を構成する

このジョブは、以前に BLOB ストレージにアップロードした CSV ファイルから入力を取得します。

1. Stream Analytics ジョブに移動します。 **[ジョブ トポロジ]** で、 **[入力]** オプションを選択します。 **[ストリーム入力の追加]**  > **[BLOB ストレージ]** を選択します。

2. **[BLOB ストレージ]** の詳細に以下の値を入力します。

   |フィールド  |値  |
   |---------|---------|
   |入力のエイリアス|入力に名前を付けます。 このエイリアスは、クエリを記述するときに使用します。|
   |サブスクリプション|サブスクリプションを選択します。|
   |ストレージ アカウント|前の手順で作成したストレージ アカウントを選択します。|
   |コンテナー|前の手順で作成したコンテナーを選択します。|
   |イベントのシリアル化の形式|CSV|

3. **[保存]** を選択します。

### <a name="configure-the-job-output"></a>ジョブの出力を構成する

このジョブは、入力を取得したのと同じ BLOB ストレージに結果を送信します。

1. Stream Analytics ジョブに移動します。 **[ジョブ トポロジ]** で、 **[出力]** オプションを選択します。 **[追加]**  >  **[BLOB ストレージ]** を選択します。

2. **[BLOB ストレージ]** フォームに以下の値を入力します。

   |フィールド  |値  |
   |---------|---------|
   |入力のエイリアス|入力に名前を付けます。 このエイリアスは、クエリを記述するときに使用します。|
   |サブスクリプション|サブスクリプションを選択します。|
   |ストレージ アカウント|前の手順で作成したストレージ アカウントを選択します。|
   |コンテナー|前の手順で作成したコンテナーを選択します。|
   |イベントのシリアル化の形式|CSV|

3. **[保存]** を選択します。

### <a name="add-the-studio-classic-function"></a>Studio (classic) 関数を追加する

前の手順では、Studio (classic) モデルを Web サービスに発行しました。 このシナリオでは、Stream Analytics ジョブの実行時に、各サンプル ツイートが感情分析のために入力から Web サービスに送信されます。 Studio (classic) Web サービスにより、センチメント (`positive`、`neutral`、`negative`) と、ツイートが positive になる確率が返されます。

このセクションでは、Stream Analysis ジョブに関数を定義します。 この関数を呼び出すと、ツイートを Web サービスに送信し、返された応答を取得できます。

1. 以前ダウンロードした Web サービス URL と API キーが Excel ブックにあることをご確認ください。

2. Stream Analytics ジョブに移動します。 次に、 **[関数]**  >  **[+ 追加]**  >  **[Azure ML Studio]** を選択します

3. **[Azure Machine Learning 関数]** フォームに以下の値を入力します。

   |フィールド  |値  |
   |---------|---------|
   | 関数のエイリアス | 名前 `sentiment` を使用して、 **[手動で Azure Machine Learning 関数設定を指定します]** を選択すると、URL とキーを入力できるようになります。      |
   | URL| Web サービス URL を貼り付けます。|
   |キー | API キーを貼り付けます。 |

4. **[保存]** を選択します。

### <a name="create-a-query-to-transform-the-data"></a>データを変換するためのクエリを作成する

Stream Analytics は、SQL ベースの宣言型クエリを使用し、入力を確認して処理します。 このセクションでは、入力から各ツイートを読み取ってから、Studio (classic) 関数を呼び出して感情分析を実行するクエリを作成します。 それからこのクエリは、定義した出力 (BLOB ストレージ) に結果を送信します。

1. Stream Analytics ジョブの概要に戻ります。

2. **[ジョブ トポロジ]** で、 **[クエリ]** を選択します。

3. 次のクエリを入力します。

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   このクエリにより、以前に作成した関数 (`sentiment`) が呼び出され、入力内の各ツイートに対して感情分析が実行されます。

4. **[保存]** を選択してクエリを保存します。

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics ジョブを開始して出力をチェックする

続いて、Stream Analytics ジョブを開始できます。

### <a name="start-the-job"></a>ジョブの開始

1. Stream Analytics ジョブの概要に戻ります。

2. ページの最上部にある **[開始]** を選択します。

3. **[ジョブの開始]** で **[カスタム]** を選択し、CSV ファイルを BLOB ストレージにアップロードした時刻の 1 日前を選びます。 設定が終了したら、**[開始]** を選択します。  

### <a name="check-the-output"></a>出力をチェックする

1. **[監視]** ボックスにアクティビティが表示されるまで、数分間ジョブを実行します。

2. BLOB ストレージのコンテンツの確認に通常使用しているツールがある場合は、そのツールを使用してコンテナーを確認します。 別の方法として、Azure Portal で次の手順を実行します。

      1. Azure portal でストレージ アカウントを検索し、このアカウント内でコンテナーを検索します。 このコンテナー内には 2 つのファイルがあります。サンプル ツイートが含まれているファイルと、Stream Analytics ジョブで生成された CSV ファイルです。
      2. 生成されたファイルを右クリックし、**[ダウンロード]** を選択します。

3. 生成された CSV ファイルを開きます。 次の例のように表示されます。  

   ![Stream Analytics Azure Machine Learning Studio (classic)、CSV ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>メトリックを表示する

また、Studio (classic) 関数に関連するメトリックを表示することもできます。 ジョブ概要の **[監視]** ボックスに、次の関数に関連するメトリックが表示されます。

* **[関数要求]** には、Studio (classic) Web サービスに対して送信された要求数が表示されます。  
* **[関数イベント]** は、要求内のイベントの数を示します。 既定では、Studio (classic) Web サービスに対する各要求には、最大 1,000 個のイベントが含まれます。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API と Machine Learning Studio (classic) を統合する](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)