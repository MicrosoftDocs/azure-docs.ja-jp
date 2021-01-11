---
title: デザイナー (プレビュー) のパイプラインとデータセットのサンプル
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーのサンプルを使用することで、機械学習パイプラインをすぐに開始できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.custom: designer
ms.openlocfilehash: c4fa5a2554f10a720c0f2241e17514e798cf7f4c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88812939"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナー (プレビュー) のパイプラインとデータセットのサンプル

Azure Machine Learning デザイナーの組み込みサンプルを使用すると、独自の機械学習パイプラインの構築をすぐに開始できます。 Azure Machine Learning デザイナーの [GitHub リポジトリ](https://github.com/Azure/MachineLearningDesigner)には、一般的な機械学習のシナリオを理解するのに役立つ詳細なドキュメントが含まれています。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。
* Enterprise SKU の Azure Machine Learning ワークスペース。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>サンプル パイプラインを使用する

デザイナーによって、サンプル パイプラインのコピーが Studio ワークスペースに保存されます。 ニーズに合うようにパイプラインを編集し、独自のものとして保存できます。 これらをプロジェクトを開始するための出発点として使用します。

デザイナーのサンプルを使用する方法を次に示します。

1. <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> にサインインし、使用するワークスペースを選択します。

1. **[デザイナー]** を選択します。

1. **[新しいパイプライン]** セクションでサンプル パイプラインを選択します。

    サンプルの完全な一覧については、 **[Show more samples]\(その他のサンプルを表示する\)** を選択してください。

1. パイプラインを実行するには、最初に、パイプラインを実行する既定のコンピューティング先を設定する必要があります。

   1. キャンバスの右側にある **[設定]** ペインで、 **[コンピューティング先を選択]** を選択します。

   1. 表示されたダイアログで、既存のコンピューティング先を選択するか、新しく作成します。 **[保存]** を選択します。

   1. キャンバス上部の **[送信]** を選択して、パイプラインの実行を送信します。

   サンプル パイプラインとコンピューティング設定によっては、実行が完了するまでに時間がかかることがあります。 既定のコンピューティング設定の最小ノード サイズは 0 です。これは、アイドル状態になった後に、デザイナーによってリソースが割り当てられる必要があることを意味します。 コンピューティング リソースが既に割り当てられているため、パイプラインの反復実行にかかる時間は短くなります。 さらにデザイナーでは、各モジュール用にキャッシュされた結果を使用して、効率を向上させます。


1. パイプラインの実行が完了したら、パイプラインをレビューし、各モジュールの出力を表示して詳細を確認できます。 モジュールの出力を表示するには、次の手順を使用します。

   1. 出力を表示するキャンバス内のモジュールを右クリックします。
   1. **[可視化]** を選択します。


   このサンプルは、最も一般的な機械学習シナリオの一部の出発点として使用してください。

## <a name="regression"></a>回帰

以下の組み込みの回帰のサンプルを確認します。

| サンプル タイトル | 説明 | 
| --- | --- |
| [サンプル 1: 回帰 - 自動車価格の予測 (基本)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | 線形回帰を使用して自動車の価格を予測します。 |
| [サンプル 2: 回帰 - 自動車価格の予測 (詳細)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | デシジョン フォレストとブースト デシジョンツリー リグレッサーを使用して、自動車の価格を予測します。 モデルを比較して、最適なアルゴリズムを見つけてください。

## <a name="classification"></a>分類

以下の組み込みの分類のサンプルを確認します。 ドキュメント リンクのないサンプルの詳細については、代わりにサンプルを開いてモジュールのコメントをご確認ください。

| サンプル タイトル | 説明 | 
| --- | --- |
| [サンプル 3: 特徴の選択による二項分類 - 収入の予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | 2 クラスのブースト デシジョン ツリーを使用して、収入を高または低として予測します。 ピアソンの相関関係を使用して、特徴を選択します。
| [サンプル 4: カスタム Python スクリプトを使用した二項分類 - 信用リスクの予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | クレジット申込書を高リスクまたは低リスクとして分類します。 Python スクリプトの実行モジュールを使用して、データを重み付けします。
| [サンプル 5: 二項分類 - 顧客関係の予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | 2 クラス ブースト デシジョン ツリーを使用して、顧客離反を予測します。 偏りのあるデータをサンプリングするには、SMOTE を使用します。
| [サンプル 7: テキスト分類 - Wikipedia SP 500 データセット](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | 多クラス ロジスティック回帰を使用して、Wikipedia の記事から会社の種類を分類します。 |
| サンプル 12: 多クラス分類 - 文字認識 | 一連の二項分類器を作成して、手書きの文字を分類します。 |

## <a name="recommender"></a>レコメンダー

以下の組み込みのレコメンダーのサンプルを確認します。 ドキュメント リンクのないサンプルの詳細については、代わりにサンプルを開いてモジュールのコメントをご確認ください。

| サンプル タイトル | 説明 | 
| --- | --- |
| サンプル 10: レコメンデーション - 映画の評価ツイート | 映画のタイトルと評価から映画レコメンダー エンジンを作成します。 |

## <a name="utility"></a>ユーティリティ

機械学習のユーティリティと機能を示すサンプルの詳細を確認します。 ドキュメント リンクのないサンプルの詳細については、代わりにサンプルを開いてモジュールのコメントをご確認ください。

| サンプル タイトル | 説明 | 
| --- | --- |
| [サンプル 6: カスタム R スクリプトの使用 - フライトの遅延予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| サンプル 8: 二項分類のクロス検証 - 成人の収入予測 | クロス検証を使用して、成人の収入予測のための二項分類器を作成します。
| サンプル 9: 順列の特徴量の重要度 | 順列の特徴量の重要度を使用して、テスト データセットの重要度スコアを計算します。 
| サンプル 11: 二項分類のパラメーターの調整 - 成人の収入予測 | モデルのハイパーパラメーターの調整を使用して、バイナリ分類器の作成に最適なハイパーパラメーターを見つけます。 |

## <a name="datasets"></a>データセット

Azure Machine Learning デザイナーで新しいパイプラインを作成する場合、さまざまなサンプル データセットが既定で用意されています。 これらのサンプル データセットは、デザイナーのホームページのサンプル パイプラインで使用されます。 

サンプル データセットは、 **[データセット]** - **[サンプル]** カテゴリの下にあります。 これは、デザイナーのキャンバスの左側にあるモジュール パレットにあります。 これらのいずれのデータセットも、キャンバスにドラッグすることにより独自のパイプラインで使用できます。

| データセット名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| データセットの説明 |
|-------------|:--------------------|
| 米国国勢調査局提供の、成人収入に関する二項分類データセット | 調整後の所得指数が 100 を超える就労成人男性 (16 歳以上) を対象とした、米国国勢調査局のデータベース (1994 年) のサブセットです。<br/>**[使用状況]** :人口統計データを使用して対象の人々を分類し、個人が 1 年間に 50,000 ドル以上の年収を得られるかどうかを予測します。<br/> **関連の研究**: Kohavi, R.、Becker, B. (1996 年)。 [UCI Machine Learning リポジトリ](https://archive.ics.uci.edu/ml)。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部|
|自動車価格データ (生データ)|メーカー/モデル別にまとめた自動車の情報です。価格、シリンダー数、燃費、保険リスク スコアなどの情報が含まれます。<br/> リスク スコアはまず車体価格に関連付けられます。 その後、アクチュアリー (保険数理士) の間でシンボリングと呼ばれるプロセスによって、実際のリスクに対して調整されます。 +3 は自動車のリスクが高く、-3 は高い安全性が見込まれることを示しています。<br/>**使用方法**:</b>回帰または多変量分類を使用して、機能別のリスク スコアを予測します。<br/>**関連の研究**:</b>Schlimmer, J.C. (1987 年)。 [UCI Machine Learning リポジトリ](https://archive.ics.uci.edu/ml)。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部。 |
| CRM 強い欲求ラベルの共有 |KDD Cup 2009 顧客間関係の予測に関する課題のラベル ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels))。|
|CRM 離反ラベルの共有|KDD Cup 2009 顧客間関係の予測に関する課題のラベル ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels))。|
|CRM データセットの共有 | このデータは、KDD Cup 2009 顧客関係の予測に関する課題から取得しています ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip))。 <br/>データセットには、French Telecom company Orange の顧客 50,000 人のデータが含まれます。 各顧客には匿名化された特徴が 230 あり、その中の 190 が数値で、40 がカテゴリです。 特徴はきわめて疎です。 |
|CRM アップセリング ラベルの共有|KDD Cup 2009 顧客間関係の予測に関する課題のラベル ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|フライト遅延データ|米国運輸省の TranStats データ コレクションから取得した旅客機の定時運航データ ([定時](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time))。<br/>データセットには、2013 年 4 月から 10 月までの期間のデータが含まれます。 デザイナーにアップロードする前に、データセットは次のように処理されました。 <br/>-    米国本土の混雑度が上位 70 までの空港を含めるように、データセットをフィルター処理しました <br/>- キャンセルされたフライトは 15 分超の遅延として分類しました <br/>- 迂回したフライトをフィルターで除外しました <br/>- 次の列が選択されました。Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Cancelled|
|ドイツのクレジット カード UCI データセット|german.data ファイルを使用した、UCI Statlog (ドイツのクレジット カード) データセット ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)))。<br/>データセットは、低信用リスクまたは高信用リスクとして属性のセットで表現された人々を分類します。 サンプルはそれぞれ人を表します。 20 の特徴があり、数値とカテゴリの両方と、二項のラベル (信用リスク値) で構成されます。 高信用リスクのエントリにはラベル 2、低信用リスクのエントリにはラベル 1 が付きます。 低信用リスクのサンプルを高信用リスクとして誤って分類した場合のコストは 1 ですが、高信用リスクのサンプルを低信用リスクとして誤って分類した場合のコストは 5 です。|
|IMDB 映画のタイトル|このデータセットには、Twitter のツイートで評価された映画に関する情報として、IMDB 登録 ID、映画の名称、ジャンル、および制作年が含まれます。 約 17,000 件の映画の情報があります。 データセットは、S. Dooms, T. De Pessemier and L. Martens による論文 「MovieTweetings: a Movie Rating Dataset Collected From Twitter。 Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013」で紹介されました。|
|映画の評価|このデータセットは、ムービー ツイート データセットの拡張バージョンです。 Twitter で適切に構成されたツイートから抽出した、約 170,000 件の評価が含まれます。 各インスタンスは 1 つのツイートを表し、ユーザー ID、IMDB 登録 ID、評価、タイプスタンプ、このツイートのお気に入りの数、そして、このツイートのリツイートの数を組み合わせたタプルです。 データセットは、A. Said、S. Dooms、B. Loni、D. Tikk によって Recommender Systems Challenge 2014 のために提供されました。|
|天候データセット|NOAA の 1 時間ごとの陸上の気象観測 ([2013 年 4 月から 2013 年 10 月までのデータをマージ](https://az754797.vo.msecnd.net/data/WeatherDataset.csv))。<br/>気象データには、2013 年 4 月から 10 月までの期間の航空気象観測所での観測が含まれます。 デザイナーにアップロードする前に、データセットは次のように処理されました。    <br/> -    気象観測所 ID を、対応する空港 ID にマッピングしました    <br/> -    混雑度が上位 70 の空港に関連付けられていない気象観測所をフィルターで除外しました    <br/> -    Date 列を Year、Month、Day の各列に分割しました    <br/> - 次の列が選択されました。AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter|
|Wikipedia SP 500 データセット|データは、S&P 500 各企業に関する記事に基づいて Wikipedia (https://www.wikipedia.org/) から派生され、XML データとして格納されています。    <br/>デザイナーにアップロードする前に、データセットは次のように処理されました。    <br/> - 特定の各企業のテキスト コンテンツを抽出しました    <br/> -    Wiki の書式設定を削除しました    <br/> -    英数字以外の文字を削除しました    <br/> -    すべてのテキストを小文字に変換しました    <br/> -    既知の会社のカテゴリを追加しました    <br/>いくつかの企業の記事が見つからないため、レコード数は 500 未満であることに注意してください。|


## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次のステップ

予測分析と機械学習の基本について学習する: [チュートリアル: デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)

