---
title: "Azure Stream Analytics と Machine Learning の統合 | Microsoft Docs"
description: "Stream Analytics ジョブで、ユーザー定義関数および Machine Learning を使用する方法"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/06/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 243ee799d2cddb1baf5b8046eee6eaf182463d2e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Azure Stream Analytics と Azure Machine Learning を使用した感情分析の実行
この記事では、Azure Machine Learning を統合する単純な Azure Stream Analytics ジョブをすばやくセットアップする方法について説明します。 ここでは、Cortana Intelligence ギャラリーの Machine Learning 感情分析モデルを利用して、ストリーミング テキスト データを分析し、リアルタイムでセンチメント スコアを決定します。 Cortana Intelligence Suite を使用すると、感情分析モデルを構築する複雑な作業を心配することなくこのタスクを実行できます。

この記事で学ぶ内容は、次のようなシナリオに適用できます。

* ストリーミング Twitter データに関するリアルタイムの感情分析。
* サポート スタッフと顧客とのチャット レコード分析。
* フォーラム、ブログ、ビデオに対するコメントの評価。 
* その他多くのリアルタイムの予測スコアリング シナリオ。

現実のシナリオなら、Twitter データ ストリームから直接データを取得するでしょう。 チュートリアルを簡単にするために、Stream Analytics ジョブが Azure Blob Storage 内の CSV ファイルからツイートを取得できるよう、こちらで既に準備しています。 独自の CSV ファイルを作成することも、次の図のようなサンプル CSV ファイルを使用することもできます。

![CSV ファイルのサンプル ツイート](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

作成する Stream Analytics ジョブは、BLOB ストアのサンプル テキスト データに対して、ユーザー定義関数 (UDF) として感情分析モデルを適用します。 出力 (感情分析の結果) は、同じ BLOB ストアの別の CSV ファイル内に書き込まれます。 

次の図に、この構成を示します。 前述のように、さらに現実的なシナリオとしては、BLOB ストレージを Azure Event Hub 入力からのストリーミング Twitter データに置き換えることができます。 また、集計センチメントの [Microsoft Power BI](https://powerbi.microsoft.com/) リアルタイム視覚化を構築することもできます。    

![Stream Analytics と Machine Learning の統合の概要](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>前提条件
始める前に、以下のものを用意してください。

* 有効な Azure サブスクリプション
* ある程度のデータが入力された CSV ファイル。 前述のファイルを [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) からダウンロードすることも、独自のファイルを作成することもできます。 この記事では、GitHub からのファイルを使用することを前提としています。

大まかに言えば、この記事で説明するタスクを完了するには、以下の手順を実行します。

1. Azure ストレージ アカウントと BLOB ストレージ コンテナーを作成し、そのコンテナーに CSV 形式の入力ファイルをアップロードします。
3. Cortana Intelligence ギャラリーの感情分析モデルを Azure Machine Learning ワークスペースに追加し、このモデルを Web サービスとして Machine Learning ワークスペースにデプロイします。
5. この Web サービスを、テキスト入力のセンチメントを決定するための関数として呼び出す Stream Analytics ジョブを作成します。
6. Stream Analytics ジョブを開始し、出力をチェックします。

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Storage コンテナーを作成して CSV 入力ファイルをアップロードする
この手順では、GitHub から入手できるファイルなど、あらゆる CSV ファイルを使用できます。

1. Azure Portal で、**[新規]** &gt; **[ストレージ]** &gt; **[ストレージ アカウント]** の順にクリックします。

   ![新しいストレージ アカウントを作成する](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-create-storage-account.png)

2. 名前 (この例では `samldemo`) を指定します。 名前には小文字と数字だけを使用できます。名前は Azure 全体で一意である必要があります。 

3. 既存のリソース グループを指定し、場所を指定します。 場所については、このチュートリアルで作成するすべてのリソースで同じ場所を使用することをお勧めします。

    ![ストレージ アカウントの詳細を入力する](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. Azure Portal で、ストレージ アカウントを選択します。 ストレージ アカウント ブレードで、**[コンテナー]** をクリックしてから **[+&nbsp;コンテナー]** をクリックして、BLOB ストレージを作成します。

    ![BLOB コンテナーを作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. コンテナーの名前 (この例では `azuresamldemoblob`) を指定し、**[アクセスの種類]** が **[BLOB]** に設定されていることを確認します。 完了したら **[OK]**をクリックします。

    ![BLOB コンテナーの詳細を指定する](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. **[コンテナー]** ブレードで新しいコンテナーを選択します。そのコンテナーのブレードが開きます。

7. **[アップロード]**をクリックします。

    ![コンテナーの [アップロード] ボタン](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. **[BLOB のアップロード]** ブレードで、このチュートリアルで使用する CSV ファイルを指定します。 **[BLOB の種類]** には **[ブロック BLOB]** を選択し、ブロック サイズを 4 MB に設定します。このチュートリアルにはこれで十分です。

    ![BLOB ファイルをアップロードする](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

9. ブレードの下部にある **[アップロード]** ボタンをクリックします。

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーからセンチメント分析モデルを追加する

サンプル データが BLOB に含まれたので、Cortana Intelligence ギャラリーの感情分析モデルを有効にできます。

1. Cortana Intelligence ギャラリーの[予測感情分析モデル](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)のページに進みます。  

2. **[Studio で開く]** をクリックします。  
   
   ![Stream Analytics Machine Learning, Machine Learning Studio を開く](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. サインインしてワークスペースを開きます。 場所を選択します。

4. ページの下部にある **[実行]** をクリックします。 プロセスが実行され、約 1 分かかります。

   ![Machine Learning Studio で実験を実行する](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. プロセスが正常に実行されたら、ページの下部にある **[Web サービスのデプロイ]** を選択します。

   ![Machine Learning Studio で実験を Web サービスとしてデプロイする](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 感情分析モデルが使用できる状態か検証するには、**[テスト]** ボタンをクリックします。 「I love Microsoft」などのテキストを入力します。 

   ![Machine Learning Studio で実験をテストする](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    テストが正常に機能すると、次の例のような結果が表示されます。

   ![Machine Learning Studio のテスト結果](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. **[アプリ]** 列で、**[Excel 2010 or earlier workbook]\(Excel 2010 以前のブック)** リンクをクリックして、Excel ブックをダウンロードします。 このブックには、後で Stream Analytics ジョブをセットアップする際に必要になる API キーと URL が含まれています。

    ![Stream Analytics Machine Learning, 概要](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Machine Learning モデルを使用する Stream Analytics ジョブを作成する

続いて、サンプル ツイートを BLOB ストレージ内の CSV ファイルから読み取る Stream Analytics ジョブを作成できます。 

### <a name="create-the-job"></a>ジョブを作成する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。  

2. **[新規]** > **[モノのインターネット]** > **[Stream Analytics ジョブ]** の順にクリックします。 

   ![新しい Stream Analytics ジョブに至る Azure Portal のパス](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-new-iot-sa-job.png)
   
3. ジョブに `azure-sa-ml-demo` と名前を付け、サブスクリプションを指定し、既存のリソース グループを指定するか新しく作成し、ジョブの場所を選択します。

   ![新しい Stream Analytics ジョブの設定を指定する](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>ジョブの入力を構成する
このジョブは、以前に BLOB ストレージにアップロードした CSV ファイルから入力を取得します。

1. ジョブの作成後に、ジョブ ブレード内の **[ジョブ トポロジ]** の下の **[入力]** ボックスをクリックします。  
   
   ![Stream Analytics ジョブ ブレードの [入力] ボックス](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

2. **[入力]** ブレードで、**[+ 追加]** をクリックします。

   ![Stream Analytics ジョブに入力を追加するための [追加] ボタン](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-button.png)  

3. **[新しい入力]** ブレードに次の値を記入します。

    * **[入力のエイリアス]**: `datainput` という名前を使います。
    * **[ソースの種類]**: **[データ ストリーム]** を選択します。
    * **[ソース]**: **[Blob Storage]** を選択します。
    * **[インポート オプション]**: **[現在のサブスクリプションの BLOB ストレージを使う]** を選択します。 
    * **[ストレージ アカウント]**:  以前に作成したストレージ アカウントを選択します。
    * **[コンテナー]**。 以前に作成したコンテナー (`azuresamldemoblob`) を選択します。
    * **[イベントのシリアル化の形式]**。 **[CSV]** を選択します。

    ![新しいジョブ入力の設定](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. ページの下部にある **[Create]**」を参照してください。

### <a name="configure-the-job-output"></a>ジョブの出力を構成する
このジョブは、入力を取得したのと同じ BLOB ストレージに結果を送信します。 

1. **[ジョブ トポロジ]** ジョブ ブレードの下の **[出力]** ボックスをクリックします。  
  
   ![Stream Analytics ジョブの新しい出力を作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

2. **[出力]** ブレードで、**[+ 追加]** をクリックしてから、別名 `datamloutput` の出力を追加します。 

3. **[シンク]**については、**[Blob Storage]** を選択します。 次のように、入力に使用したのと同じ BLOB ストレージの値を使用して、残りの出力設定を記入します。

    * **[ストレージ アカウント]**。 以前に作成したストレージ アカウントを選択します。
    * **[コンテナー]**。 以前に作成したコンテナー (`azuresamldemoblob`) を選択します。
    * **[イベントのシリアル化の形式]**。 **[CSV]** を選択します。

   ![新しいジョブ出力の設定](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. ページの下部にある **[Create]**」を参照してください。   


### <a name="add-the-machine-learning-function"></a>Machine Learning 関数を追加する 
以前に、Machine Learning モデルを Web サービスに発行しました。 このシナリオでは、Stream Analytics ジョブの実行時に、各サンプル ツイートが感情分析のために入力から Web サービスに送信されます。 Machine Learning Web サービスにより、センチメント (`positive`、`neutral`、`negative`) と、ツイートが positive になる確率が返されます。 

チュートリアルのこのセクションでは、Stream Analytics ジョブ内の関数を定義します。 この関数を呼び出すと、ツイートを Web サービスに送信し、返された応答を取得できます。 

1. 以前ダウンロードした Web サービス URL と API キーが Excel ブックにあることをご確認ください。

2. ジョブ概要ブレードに戻ります。

3. **[設定]** の下で、**[関数]** を選択してから、**[+ 追加]** をクリックします。

   ![Stream Analytics ジョブに関数を追加する](./media/stream-analytics-machine-learning-integration-tutorial/create-function1.png) 

4. 関数の別名として `sentiment` を入力し、次の値を使用してブレードの残りの部分に記入します。

    * **[関数の種類]**: **[Azure ML]** を選択します。
    * **[インポート オプション]**: **[別のサブスクリプションからインポートする]** を選択します。 URL とキーを入力できるようになります。
    * **[URL]**: Web サービスの URL を貼り付けます。
    * **[キー]**: API キーを貼り付けます。
  
    ![Machine Learning 関数を Stream Analytics ジョブに追加するための設定](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
5. ページの下部にある **[Create]**」を参照してください。

### <a name="create-a-query-to-transform-the-data"></a>データを変換するためのクエリを作成する

Stream Analytics は、SQL ベースの宣言型クエリを使用し、入力を確認して処理します。 このセクションでは、入力から各ツイートを読み取ってから、Machine Learning 関数を呼び出して感情分析を実行するクエリを作成します。 それからこのクエリは、定義した出力 (BLOB ストレージ) に結果を送信します。

1. ジョブ概要ブレードに戻ります。

2.  **[ジョブ トポロジ]** の下の **[クエリ]** ボックスをクリックします。

    ![Stream Analytics ジョブに関するクエリを作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-query.png)  

3. 次のクエリを入力します。

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    このクエリは、入力内の各ツイートに対して感情分析を実行するために、以前に作成した関数 (`sentiment`) を呼び出します。 

4. **[保存]** をクリックしてクエリを保存します。


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics ジョブを開始して出力をチェックする

続いて、Stream Analytics ジョブを開始できます。

### <a name="start-the-job"></a>ジョブの開始
1. ジョブ概要ブレードに戻ります。

2. ブレードの上部にある **[開始]** をクリックします。

    ![Stream Analytics ジョブに関するクエリを作成する](./media/stream-analytics-machine-learning-integration-tutorial/start-job.png)  

3. **[ジョブの開始]** で **[カスタム]** を選択し、CSV ファイルを BLOB ストレージにアップロードした時刻の 1 日前を選びます。 終了したら **[開始]**をクリックします。  


### <a name="check-the-output"></a>出力をチェックする
1. **[監視]** ボックスにアクティビティが表示されるまで、数分間ジョブを実行します。 

2. BLOB ストレージのコンテンツの確認に通常使用しているツールがある場合は、そのツールを使用して `azuresamldemoblob` コンテナーを確認します。 別の方法として、Azure Portal で次の手順を実行します。

    1. Portal で `samldemo` ストレージ アカウントを検索し、このアカウント内で `azuresamldemoblob` コンテナーを検索します。 このコンテナー内には 2 つのファイルがあります。サンプル ツイートが含まれているファイルと、Stream Analytics ジョブで生成された CSV ファイルです。
    2. 生成されたファイルを右クリックし、**[ダウンロード]** を選択します。 

   ![Blob Storage から CSV ジョブ出力をダウンロードする](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. 生成された CSV ファイルを開きます。 次の例のように表示されます。  
   
   ![Stream Analytics Machine Learning, CSV ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>メトリックを表示する
Azure Machine Learning 関数に関連するメトリックも表示できます。 ジョブ ブレード内の **[監視]** ボックスに、次の関数に関連するメトリックが表示されます。

* **[関数要求]** は、Machine Learning Web サービスに対して送信された要求数を示します。  
* **[関数イベント]** は、要求内のイベントの数を示します。 既定では、Machine Learning Web サービスへの各要求には、最大 1,000 件のイベントが含まれています。  


## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics への Machine Learning の統合](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)




