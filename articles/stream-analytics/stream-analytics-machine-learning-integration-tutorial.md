---
title: "Azure Stream Analytics と Machine Learning の統合 | Microsoft Docs"
description: "Stream Analytics ジョブで、ユーザー定義関数および Machine Learning を使用する方法"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d1ffb9aba0eb1e17d1efd913f536f6f3997fccbb
ms.openlocfilehash: 7b635b1810536f5b3eb1371d687e9c355e604e41
ms.lasthandoff: 02/14/2017

---

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Azure Stream Analytics と Azure Machine Learning を使用したセンチメント分析
この記事は、単純な Azure Stream Analytics ジョブを Azure Machine Learning とすばやく統合できるように設計されています。 ここでは、Cortana Intelligence ギャラリーのセンチメント分析 Machine Learning モデルを利用して、ストリーミング テキスト データを分析し、リアルタイムでセンチメント スコアを決定します。 ストリーミング Twitter データに関するリアルタイムのセンチメント分析、サポート スタッフと顧客とのチャット レコード分析、フォーラムやブログ、ビデオに対するコメントの評価などのシナリオに加えて、その他多くのリアルタイムの予測スコアリング シナリオを理解するには、この記事の情報が役立ちます。

この記事では、次の図に示すような、Azure Blob Storage への入力として、テキストを含む CSV ファイルの例を提供します。 このジョブは、BLOB ストアのサンプル テキスト データに対して、ユーザー定義関数 (UDF) としてセンチメント分析モデルを適用します。 最終的な結果は、同じ BLOB ストア内の別の CSV ファイルに出力されます。 

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

次の図に、この構成を示します。 さらに現実的なシナリオとしては、Blob Storage を Azure Event Hub 入力からのストリーミング Twitter データに置き換えることができます。 また、集計センチメントの [Microsoft Power BI](https://powerbi.microsoft.com/) リアルタイム視覚化を構築することもできます。    

![Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>前提条件
この記事で紹介されているタスクを完了するための前提条件は次のとおりです。

* 有効な Azure サブスクリプション
* ある程度のデータが入力された CSV ファイル。 図 1 に示すようなファイルを [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv) からダウンロードすることも、独自のファイルを作成することもできます。 この記事では、GitHub のダウンロードで提供されるファイルを使用することを前提としています。

大まかに言えば、この記事で説明するタスクを完了するには、以下の手順を実行します。

1. CSV の入力ファイルを Azure Blob Storage にアップロードします。
2. Cortana Intelligence ギャラリーのセンチメント分析モデルを Azure Machine Learning ワークスペースに追加します。
3. このモデルを Web サービスとして Machine Learning ワークスペースにデプロイします。
4. この Web サービスを、テキスト入力のセンチメントを決定する関数として呼び出す Stream Analytics ジョブを作成します。
5. Stream Analytics ジョブを開始し、出力を確認します。

## <a name="create-a-storage-blob-and-upload-the-csv-input-file"></a>ストレージ BLOB の作成と CSV 入力ファイルのアップロード
この手順では、GitHub からダウンロードできるものとして既に指定されているファイルなど、すべての CSV ファイルを使用できます。 csv ファイルのアップロードは、ストレージ BLOB の作成に含まれているオプションであるため簡単です。

このチュートリアルでは、**[新規]** をクリックし、'ストレージ アカウント' を検索してから、結果として表示されるストレージ アカウントのアイコンを選択し、アカウントの作成の詳細情報を指定することで、新しいストレージ アカウントを作成します。 **[名前]** (azuresamldemosa in my example) を指定し、既存の**リソース グループ**を使用するか新規に作成し、**[場所]** を指定します (場所については、このデモで作成されるすべてのリソースが可能な限り同じ場所を使用することが重要です)。

![ストレージ アカウントの作成](./media/stream-analytics-machine-learning-integration-tutorial/create-sa.png)

完了したら、Blob service をクリックし、BLOB コンテナーを作成できます。

![BLOB コンテナーを作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

コンテナーの **[名前]** (この例では azuresamldemoblob) を指定し、**[アクセスの種類]** が 'Blob' に設定されていることを確認します。

![BLOB のアクセスの種類を作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

ここで、BLOB にデータを設定できます。 **[ファイル]** を選択し、GitHub からダウンロードしたローカル ドライブ上のファイルを選びます。 ブロック BLOB を選び、サイズとして 4 MB を選びました。このデモでは、この条件で問題ありません。 次に、**[アップロード]** を選択すると、ポータルによってテキストのサンプルを含む BLOB が作成されます。

![BLOB のアップロード ファイルを作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

サンプル データが BLOB に含まれたので、Cortana Intelligence ギャラリーの感情分析モデルを有効にします。

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーからセンチメント分析モデルを追加する
1. Cortana Intelligence ギャラリーから [予測センチメント分析モデル](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) をダウンロードします。  
2. Machine Learning Studio で **[Studio で開く]** を選択します。  
   
   ![Stream Analytics Machine Learning, Machine Learning Studio を開く](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. サインインしてワークスペースを開きます。 最適な場所を選択します。
4. ページの下部にある **[実行]** をクリックします。  
5. プロセスが正常に実行されたら、**[Web サービスのデプロイ]** を選択します。
6. センチメント分析モデルを使用できるようになりました。 検証するには、**[テスト]** をクリックし、「I love Microsoft.」などのテキスト入力を行います。 テストにより、次のような応答が返されます。

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Stream Analytics Machine Learning, 分析データ](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

**[アプリ]** 列で、**Excel 2010 以前のブック**のリンクをクリックして、後で Stream Analytics ジョブを設定する際に必要になる API キーと URL を取得します。 (この手順は、別の Azure アカウントのワークスペースから Machine Learning モデルを利用する場合にのみ必要です。 この記事では、このシナリオに対応する場合を想定しています)。  

以下のように、ダウンロードした Excel ファイルの Web サービス URL とアクセス キーを書き留めます。  

![Stream Analytics Machine Learning, 概要](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Machine Learning モデルを使用する Stream Analytics ジョブを作成する
1. [Azure ポータル](https://portal.azure.com)にアクセスします。  
2. **[新規]** > **[インテリジェンス + 分析]** > **[Stream Analytics]** の順にクリックします。 ジョブの名前を **[ジョブ名]** に入力し、既存のリソース グループを指定するか、必要に応じて作成し、ジョブの適切な場所を **[場所]** フィールドに入力します。    
   
   ![ジョブを作成する](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   
3. ジョブが作成された後、**[入力]** タブで **[入力の追加]** を選択します。  
   
   ![Stream Analytics Machine Learning, Machine Learning 入力の追加](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

4. **[追加]** を選択し、**[入力のエイリアス]** を指定して **[データ ストリーム]** を選択し、入力として **[Blob Storage]** を選択し、**[次へ]** を選択します。  
5. ウィザードの **[BLOB ストレージの設定]** ページで、データをアップロードしたときに定義したストレージ アカウント BLOB コンテナーを指定します。 **[次へ]**をクリックします。 **[イベントのシリアル化の形式]** で、**[CSV]** を選択します。 **[シリアル化の設定]** ページのその他の項目は既定値のままにします。 **[OK]**をクリックします。  
   
   ![入力 BLOB コンテナーを追加する](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-blob.png)

6. **[出力]** タブで **[出力の追加]** を選択します。  
   
   ![Stream Analytics Machine Learning, 出力の追加](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

7. **[BLOB 記憶域]**をクリックして、コンテナーを除き同じパラメーターを入力します。 **[入力]** の値は、**CSV** ファイルがアップロードされた "test" というコンテナーから読み取るように構成されました。 **[出力]**に「testoutput」と入力します。
8. 出力の **[シリアル化の設定]** が **[CSV]** に設定されていることを確認し、**[OK]** をクリックします。
   
   ![Stream Analytics Machine Learning, 出力の追加](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

9. **[関数]** タブで **[Machine Learning 関数の追加]** を選択します。  
   
   ![Stream Analytics Machine Learning, Machine Learning 関数の追加](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  

10. **[Machine Learning Web サービス設定]** ページで、Machine Learning ワークスペース、Web サービス、既定のエンドポイントを確認します。 この記事では、URL を知っていて API キーを持っている場合は、ワークスペースに合わせた Web サービス構成に慣れるように、手動で設定を適用します。 エンドポイントの **[URL]** と **[API キー]** を入力します。 **[OK]**をクリックします。 **[関数のエイリアス]** は 'センチメント' です。  
    
    ![Stream Analytics Machine Learning, Machine Learning Web サービス](./media/stream-analytics-machine-learning-integration-tutorial/add-function-endpoints.png)    

11. **[クエリ]** タブで、次のようにクエリを変更します。    
    
    ```
    WITH sentiment AS (  
      SELECT text, sentiment(text) as result from datainput  
    )  
    
    Select text, result.[Scored Labels]  
    Into testoutput  
    From sentiment  
    ```    

12. **[保存]** をクリックしてクエリを保存します。

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Stream Analytics ジョブを開始し、出力を確認します
1. ジョブのページの上部にある **[開始]** をクリックします。
2. **[クエリの開始]** ダイアログで **[ユーザー設定時刻]** を選択し、CSV を Blob Storage にアップロードする時刻の&1; 日前を選びます。 **[OK]**をクリックします。  
3. CSV ファイルのアップロードに使用したツール (Visual Studio など) を使用して、Blob Storage に移動します。
4. ジョブが開始されて数分後に、出力コンテナーが作成され、CSV ファイルがアップロードされます。  
5. CSV の既定のエディターでファイルを開きます。 次のような内容が表示されます。  
   
   ![Stream Analytics Machine Learning, CSV ビュー](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>まとめ
この記事では、ストリーミングのテキスト データを読み取り、そのデータにセンチメント分析をリアルタイム適用する Stream Analytics ジョブを作成する方法を示します。 このすべての処理は、センチメント分析モデルを構築する場合の複雑な作業を心配することなく実行できます。 これが Cortana Intelligence Suite の利点の&1; つです。

Azure Machine Learning 関数に関連するメトリックも表示できます。 そのためには、**[監視]** タブを選択します。 関数に関連する&3; つのメトリックが表示されます。  

* **[関数要求]** は、Machine Learning Web サービスに対して送信された要求数を示します。  
* **[関数イベント]** は、要求内のイベントの数を示します。 既定では、Machine Learning Web サービスへの各要求には、最大 1,000 件のイベントが含まれています。  
  
    ![Stream Analytics Machine Learning, Machine Learning モニター ビュー](./media/stream-analytics-machine-learning-integration-tutorial/job-monitor.png)  


