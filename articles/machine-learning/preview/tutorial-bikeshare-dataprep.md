---
title: "自転車シェアのチュートリアル - Azure Machine Learning Workbench による詳細データの準備"
description: "および Azure Machine Learning Workbench を使用したエンド ツー エンド データの準備に関するチュートリアル"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: article
ms.date: 09/21/2017
ms.openlocfilehash: 722657c9bbae23a051a63972a8800d3cc40e7e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>自転車シェアのチュートリアル: Azure Machine Learning Workbench による詳細データの準備
Azure Machine Learning サービス (プレビュー) は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

ここでは、Azure Machine Learning サービス (プレビュー) を使って次の作業を行う方法について説明します。
> [!div class="checklist"]
> * Azure Machine Learning データ準備ツールを使用して対話形式でデータを準備する
> * テスト データセットをインポート、変換、および作成する
> * データ準備パッケージを生成する
> * Python を使用してデータ準備パッケージを実行する
> * 追加の入力ファイルのデータ準備パッケージを再利用して、トレーニング データセットを生成する

> [!IMPORTANT]
> このチュートリアルでは、データを準備するだけです。予測モデルの構築は行いません。
>
> 準備したデータをユーザー固有の予測モデルのトレーニングに使用します。 たとえば、2 時間という枠内での自転車の需要を予測するモデルを作成できます。

## <a name="prerequisites"></a>前提条件
* Azure Machine Learning Workbench がローカルにインストールされていること。 詳細については、[インストールのクイック スタート](quickstart-installation.md)に関する記事をご覧ください。
* ワークベンチでの新しいプロジェクトの作成についてよく知っていること。

## <a name="data-acquisition"></a>データの取得
このチュートリアルでは、[ボストンの Hubway 社のデータセット](https://s3.amazonaws.com/hubway-data/index.html)と、[米国立海洋大気庁 (NOAA)](http://www.noaa.gov/) によるボストンの気象データを使用します。

1. 以下のリンクから、ローカルの開発環境にデータ ファイルをダウンロードします。 
   * [ボストン気象データ](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)。 
   * Hubway 社の Web サイトから入手した Hubway の旅行データ。

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. ダウンロード後に各 .zip ファイルを解凍します。

## <a name="learn-about-the-datasets"></a>データセットについて
1. __ボストンの気象__のファイルには、時間単位で報告される、次の気象関連フィールドが含まれています。
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. __Hubway__ 社のデータは、年月ごとのファイルとして編成されています。 たとえば、`201501-hubway-tripdata.zip` という名前のファイルには、2015 年 1 月のデータを含む .csv ファイルが含まれています。 データには、自転車旅行を表す次のフィールドが含まれています。

   * 旅行時間 (秒単位)
   * 開始日時
   * 終了日時
   * 開始駅の名前および ID
   * 終了駅の名前および ID
   * 自転車の ID
   * ユーザーの種類 (カジュアル = 24 時間または 72 時間パスのユーザー、メンバー = 年間または月単位のメンバー)
   * 郵便番号 (ユーザーがメンバーである場合)
   * 性別 (メンバーの自己報告)

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. スタート メニューまたは起動プログラムから、**Azure Machine Learning Workbench** を起動します。

2. 新しい Azure Machine Learning プロジェクトを作成します。  **[プロジェクト]**ページの **+** ボタンをクリックするか、または **[ファイル]** > **[新規]** の順にクリックします。
   - **[空のプロジェクト]** テンプレートを使用します。
   - プロジェクトに **BikeShare** という名前を付けます。 

## <a id="newdatasource"></a>新しいデータ ソースの作成

1. 新しいデータ ソースを作成します。 左のツールバーにある **[データ]** ボタン (シリンダーのアイコン) をクリックします。 **[データビュー]** が表示されます。

   ![データ ビュー タブの画像](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. データ ソースを追加します。 **+** アイコンを選択して、**[データ ソースの追加]** をクリックします。

   ![[データ ソースの追加] エントリの画像](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>気象データの追加

1. **[データ ストア]**: データを含むデータ ストアを選択します。 ファイルを使用しているため、**[File(s)/Directory]\(ファイル/ディレクトリ\)**を選択します。 **[次へ]** をクリックして続行します。

   ![ファイル/ディレクトリ エントリの画像](media/tutorial-bikeshare-dataprep/datasources.png)

2. **[ファイルの選択]**: 気象データを追加します。 事前にダウンロードした `BostonWeather.csv` ファイルを参照して、選択します。 **[次へ]** をクリックします。

   ![BostonWeater.csv が選択されているファイル選択の画像](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **[ファイルの詳細]**: 検出されたファイルのスキーマを検証します。 Azure Machine Learning Workbench はファイル内のデータを分析し、使用するスキーマを推論します。

   ![ファイルの詳細の画像](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > 場合によっては、ワークベンチで適切なスキーマが検出されないことがあります。 お使いのデータセットのパラメーターが正しいことを常に確認する必要があります。 気象データの場合は、次の値に設定されていることを確認します。
   >
   > * __[ファイルの種類]__: 区切りファイル (csv、tsv、txt など)
   > * __[区切り記号]__: コンマ [,]
   > * __[Comment Line Character]\(コメント行の文字\)__: 値の設定なし。
   > * __[Skip Lines Mode]\(行のスキップ モード\)__: スキップしない
   > * __[ファイルのエンコード]__: utf-8
   > * __[Promote Headers Mode]\(ヘッダーの昇格モード\)__: 初期ファイルのヘッダーを使用する

   データのプレビューでは、次の列が表示されます。
   * **パス**
   * **DATE** (日付)
   * **REPORTTYPE** (プロパティの種類)
   * **HOURLYDRYBULBTEMPF** (1 時間ごとの乾球温度)
   * **HOURLYRelativeHumidity** (1 時間ごとの相対湿度)
   * **HOURLYWindSpeed** (1 時間ごとの風速)

   **[次へ]** を選択して、続行します。

4. **[データの種類]**: 自動的に検出されたデータの種類を確認します。 Azure Machine Learning Workbench は、ファイルのデータを分析して、使用するデータの種類を推論します。

   このデータでは、すべての列の `DATA TYPE` を `String`に変更します。

   > [!NOTE]
   > `String` は、このチュートリアルで後からワークベンチの機能を強調するために使用されます。 

   ![データの種類の画像](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   __[次へ]__ を選択して、続行します。 

5. **[サンプリング]**: サンプリング スキームを作成するには、**[+ New]\(+ 新規\)** ボタンを選択します。 追加された新しい__上位 10000__ 行を選択して、__[編集]__を選択します。 __[Sample Strategy]\(サンプル戦略\)__ を **[Full File]\(完全ファイル\)** に設定してから、**[適用]** を選択します。

   ![新しいサンプル戦略の追加の画像](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   __[Full File]\(完全ファイル\)__ 戦略を使用するために、__[Full File]\(完全ファイル\)__ エントリを選択して __[アクティブとして設定]__を選びます。 __[Full File]\(完全ファイル\)__ の横に、アクティブな戦略であることを示す星が表示されます。

   ![[Full File]\(完全ファイル\) がアクティブな戦略になっている画像](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   **[次へ]** を選択して、続行します。

6. **[Path Column]\(パス列\)**: __[Path Column]\(パス列\)__ セクションでは、インポートされたデータに完全なファイル パスを列として組み入れることができます。 __[Do Not Include Path Column]\(パス列を組み入れない\)__ を選びます。

   > [!TIP]
   > ファイル名の異なる多数のファイルを格納したフォルダーをインポートする場合は、パスを列として組み入れると便利です。 また、後で抽出したい情報がファイル名に含まれている場合にも便利です。

   ![パス列を組み入れない設定になっている画像](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **[完了]**: データ ソースの作成を完了するには、**[完了]** ボタンを選択します。

    __BostonWeather__ という名前の新しいデータ ソース タブが開きます。 データのサンプルは、グリッド ビューに表示されます。 サンプルは、前に指定されたアクティブなサンプリング スキームに基づいています。

    画面の右側にある **[ステップ]** ウィンドウには、このデータ ソースの作成中に実行された個々のアクションが表示されます。

   ![データ ソース、サンプル、およびステップを表示した画像](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>データ ソース メトリックの表示

タブのグリッド ビューの左上にある __[メトリック]__ ボタンを選択します。 このビューには、サンプル データの分布とその他の集計済みの統計が表示されます。

![メトリックを表示した画像](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> 統計の表示を設定するには、**[Choose Metric]\(メトリックの選択\)** ドロップダウンを使用します。 このドロップダウンにあるメトリックのチェックをオンまたはオフにして、グリッド ビューを変更します。

__データ ビュー__を返すには、ページの左上の __[データ]__ を選択します。

## <a name="add-data-source-to-data-preparation-package"></a>データ ソースをデータ準備パッケージに追加する

1. __[準備]__ を選択して、データの準備を開始します。 

2. プロンプトが表示されたら、`BikeShare Data Prep` のようにデータ準備パッケージの名前を入力します。 

3. __[OK]__ を選択して続行します。

   ![準備ダイアログの画像](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. **BikeShare Data Prep** という名前の新しいパッケージが、__[データ]__ タブの __[データ準備]__ セクションに表示されます。 

   パッケージを表示するには、このエントリを選択します。 

5. **>>** ボタンを選択して展開し、パッケージに格納されている __[Dataflows]\(データフロー\)__ を表示します。 この例では、__BostonWeather__ が唯一のデータフローです。

   > [!IMPORTANT]
   > パッケージには、複数のデータフローを含めることができます。

   ![パッケージに含まれているデータフローの画像](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>値によるデータのフィルター処理
1. データをフィルター処理するには、特定の値を含むセルを右クリックして、__[フィルター]__ を選択してから、フィルターの種類を選びます。

2. このチュートリアルでは、値 `FM-15` を含むセルを選択して、フィルターを **[Equals]\(に等しい\)** のフィルターに設定します。  これで、__REPORTTYPE__ が `FM-15` になっている戻り値の行だけに、データがフィルター処理されました。

   ![フィルター ダイアログの画像](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 は、Meteorological Terminal Aviation Routine Weather Report (METAR) の1 つの種類です。 FM-15 のレポートは経験的に、データの欠損がほとんどなく、最も完成されていると考えられています。

## <a name="remove-a-column"></a>列の削除

__REPORTTYPE__ 列は必要なくなりました。 列見出しを右クリックして **[列の削除]** を選択します。

   ![列の削除オプションの画像](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>データ型の変更とエラーの除去
1. 列見出しを選択したまま __Ctrl__ キーを押すと、一度に複数の列を選択できます。 これを使用して、以下の列見出しを選択します。
   * **HOURLYDRYBULBTEMPF** (1 時間ごとの乾球温度)
   * **HOURLYRelativeHumidity** (1 時間ごとの相対湿度)
   * **HOURLYWindSpeed** (1 時間ごとの風速)

2. 選択した列見出しの 1 つを**右クリック**して、**[Convert Field Type to Numeric]\(フィールドの型を数値に変換する\)** を選択します。 これにより、列のデータ型が数値に変換されます。

   ![複数の列を数値に変換する](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. エラー値をフィルターで除外します。 一部の列に、データ型の変換の問題が発生します。 この問題は、列の __Data Quality Bar\(データ品質バー\)__ に赤色で示されます。

   エラーを含む行を削除するには、**HOURLYDRYBULBTEMPF** 列の見出しを右クリックします。 **[列のフィルター]** を選択します。 既定の **[I Want To]\(次の設定を使用する\)** を **[Keep Rows]\(行の保持\)** として使用します。 **[条件]** ドロップダウンの選択を **[is not error]\(エラーではない\)** に変更します。 **[OK]** を選択して、フィルターを適用します。

4. 他の列に残っているエラー行を除去するには、**HOURLYRelativeHumidity** 列と **HOURLYWindSpeed** 列に対してこのフィルター処理を繰り返します。

## <a name="use-by-example-transformations"></a>例の使用による変換

2 時間の時間ブロック予測でデータを使用するには、2 時間という期間の平均の気象条件を計算する必要があります。 これを行うために、以下の操作を行います。

* **DATE** 列を別個の **Date** 列と **Time** 列に分割します。 詳細な手順については、次のセクションを参照してください。

* **Time** 列から **Hour_Range**列を派生します。 詳細な手順については、次のセクションを参照してください。

* **DATE** および **Hour_Range** 列から **Date\_Hour\_Range** 列を派生します。 詳細な手順については、次のセクションを参照してください。

### <a name="split-column-by-example"></a>列の分割の例

1. **DATE** 列を別個の日付の列と時刻の列に分割します。 **DATE** 列の見出しを右クリックし、**[Split Column by Example]\(列の分割の例\)** を選択します。

   ![列の分割の例エントリの画像](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench では、自動的に意味のある区切り記号を識別し、データを日付と時刻の値に分割することで、2 つの列を作成します。 

3. __[OK]__ を選択して、分割操作の結果を受け入れます。

   ![分割された列 DATE_1 および DATE_2 の画像](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>列の派生の例

1. 2 時間の範囲を派生させるには、__DATE\_2__ 列の見出しを右クリックして、**[Derive Column by Example]\(列の派生の例\)** を選択します。

   ![列の派生の例エントリの画像](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   null 値を使って、新しい空白の列が追加されます。

2. 新しい列にある最初の空白のセルをクリックします。 新しい列に「`12AM-2AM`」と入力して必要な時間の範囲の例を提供し、Enter キーを押します。

   ![値に 12AM-2AM が設定された新しい列の画像](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML Workbench は、ユーザーから指定された例に基づくプログラムを合成して、残りの行に同じプログラムを適用します。 他のすべての行には、ユーザーが指定した例に基づいて自動的に値が入力されます。 また、ワークベンチでは、データを分析してエッジ ケースを特定しようとします。 

3. グリッドの上にあるテキスト "**データを分析しています**" は、ワークベンチがエッジ ケースの検出を試行していることを示します。 完了すると、"**Review next suggested row**\(提示された次の行を確認する\)" または "**No suggestions\(提示なし\)**" に状態が切り替わります。 この例では、"**Review next suggested row\(提示された次の行を確認する\)**" が返されます。

4. 提示された変更内容を確認するには、**[Review next suggested row]\(提示された次の行を確認する\)** を選択します。 確認および (必要な場合は) 修正する対象のセルが、画面に強調表示されます。

   ![行の確認の画像](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    __[OK]__ を選択して、変換を受け入れます。
 
5. __BostonWeather__ データのグリッド ビューに画面が戻ります。 これで、以前に追加した 3 つの列がグリッドに組み入れられました。

   ![追加された行があるグリッド ビューの画像](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  行われた変更はすべて、**[ステップ]** ウィンドウに保存されます。 **[ステップ]** ウィンドウで、下向き矢印をクリックして **[編集]**を選択します。 **[列の派生の例]** の詳細ウィンドウが表示されます。 すべての例がここに保存されています。 以下のグリッドの行をダブルクリックして、手動で例を追加することもできます。 **[キャンセル]** を選択して、変更を適用せずにグリッドのメイン ページに戻ります。 **[列の派生の例]** 変換の実行中に **[詳細モード]** を選択して、このビューにアクセスすることもできます。

6. 列の名前を変更するには、列見出しをダブルクリックして「**Hour Range**」と入力します。 **Enter** キーを押して変更を保存します。

   ![列名の変更](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. 日付と時間の範囲を派生するには、**Date\_1** および **Hour Range** 列を複数選択し、右クリックして **[Derive column by example]\(列の派生の例\)** を選択します。

   ![列の派生の例](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   最初の行に対する例として「`Jan 01, 2015 12AM-2AM`」と入力し、**Enter**キーを押します。

   ワークベンチは、指定した例に基づいて変換を決定します。 この例では、結果として、日付の形式が変更され、2 時間の枠と連結されました。

   ![`Jan 01, 2015 12AM-2AM (2015 年 1 月 1 日 午前 0 時～午前 2 時) の例の画像](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

8. **[データを分析しています]** から **[Review next suggested row]\(提示された次の行を確認する\)** に状態が変更するまで待機します。 これには数秒かかる場合があります。 状態のリンクを選択して、提示された行に移動します。 

   ![提示された確認が必要な行の画像](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   ソースの日付の値が dd/mm/yyyy または mm/dd/yyyy のどちらかに対応にしている可能性があるため、行には null 値が設定されています。 正しい値「`Jan 13, 2015 2AM-4AM`」を入力して、**Enter**キーを押します。 ワークベンチでは、残りの行の派生処理を改善するために、2 つの例を使用しています。

   ![正しく書式設定されたデータの画像](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. **[OK]** を選択して、変換を受け入れます。

   ![完了した変換グリッドの画像](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > **[ステップ]** ウィンドウの下向き矢印をクリックすると、このステップの **[Derive column by example]\(列の派生の例\)** の詳細モードを使用できます。 データ グリッドでは、**DATE\_1** と **Hour Range** 列の列名の横にチェック ボックスがあります。 **Hour Range** 列の横にあるチェックボックスをオフにして、この設定で出力がどう変化するかを確認します。 入力値に **Hour Range** 列がない場合、**12AM-2AM** は定数として処理され、派生した値にアペンドされます。 **[キャンセル]** を選択して、変更を適用せずにグリッドのメイン ページに戻ります。

10. 列の名前を変更するには、見出しをダブルクリックします。 名前を **Date Hour Range** に変更して、**Enter**キーを押します。

11. **DATE**、**DATE\_1**、**DATE\_2**、および **Hour Range** 列を複数選択します。 右クリックして、**[列の削除]** を選択します。

## <a name="summarize-data-mean"></a>データの集計 (平均) 

次の手順では、時間の範囲でグループ化して、値の平均を取得することで、気象条件を集計します。

1. **Date Hour Range** 列を選択して、**[変換]** メニューから **[集計]** を選択します。

    ![[変換] メニュー](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. データを集計するには、ページ下部にあるグリッドから上部の左右のウィンドウに列をドラッグします。 左側のウィンドウには、"**Drag columns here to group data\(データをグループ化するには、ここに列をドラッグしててください\)**" というテキストが表示されています。 右側のウィンドウには、"**Drag columns here to summarize data\(データを集計するには、ここに列をドラッグしててください\)**" というテキストが表示されています。 

    下部にあるグリッドから左側のウィンドウに **Date Hour Range** 列をドラッグします。 **HOURLYDRYBULBTEMPF**、**HOURLYRelativeHumidity**、および**HOURLYWindSpeed** を右側のウィンドウにドラッグします。 

    右側のウィンドウで各列の **[集計]** メジャーとして **[平均]** を選択します。 **[OK]** をクリックして集計を完了します。

   ![集計されたデータ画面の画像](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>スクリプトを使用したデータフローの変換

数値型の列のデータを 0 ～ 1 の範囲に変更すると、一部のモデルを迅速に収束させることができます。 現状では、この変換を汎用的に実行する組み込みの変換機能はありませんが、Python スクリプトを使用してこの処理を実行できます。

1. **[変換]** メニューの **[Transform Dataflow]\(データフローの変換\)** を選択します。

2. 表示されたテキストボックスに次のコードを入力します。 列名を使用している場合は、変更を伴わずにコードが機能する必要があります。 最小 / 最大正規化の単純なロジックを Python で記述しています。

    > [!WARNING]
    > スクリプトでは、このチュートリアルで以前に使用された列名を期待しています。 別の列名を使用している場合は、スクリプト内の名前を変更する必要があります。

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > Python スクリプトでは、最後に `df` を返す必要があります。 この値は、グリッドの入力に使用されます。
    
    ![データ フロー スクリプトの変換ダイアログ](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. __[OK]__ を選択してスクリプトを使用します。 これで、グリッド内の数値列には、0 ～ 1 の範囲の値が含まれるようになりました。

    ![0 ～ 1 の値を含むグリッド](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

気象データの準備が完了しました。 次は、旅行データを準備します。

## <a name="load-trip-data"></a>旅行データの読み込み

1. `201701-hubway-tripdata.csv` ファイルをインポートするには、「[新しいデータ ソースの作成](#newdatasource)」セクションにある手順に従います。 インポートのプロセスでは、以下のオプションを使用します。

    * __[Sampling scheme]\(サンプリング スキーム\)__: **Full File\(完全ファイル\)** サンプリング スキーム。サンプルをアクティブにします。 
    * __[データ型]__: 既定値を受け入れます。

2. データをインポートした後は、__[準備]__ ボタンを選択してデータの準備を開始します。 既存の **BikeShare Data Prep.dprep** パッケージを選択して、__[OK]__ を選択します。

    このプロセスでは、**データフロー**を既存の **[データ準備]** ファイルに追加します。新規のパッケージ作成は行いません。

    ![既存のパッケージを選択している画像](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. グリッドが読み込まれたら、__[DATAFLOWS]\(データフロー\)__ を展開します。 **BostonWeather** および **201701 hubway-tripdata** という 2 つのデータフローが表示されています。 **[201701-hubway-tripdata]** エントリを選択します。

    ![201701-hubway-tripdata エントリの画像](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>マップ インスペクターの使用

データ準備では、データへの理解を深めて外れ値を特定するうえで役立つ文字列、数値、および地理データに対する "**インスペクター**" と呼ばれる便利な視覚化機能が多数用意されています。 以下の手順に従って、マップ インスペクターを使用します。

1. **start station latitude** および **start station longitude** 列を複数選択します。 どちらかの列を右クリックして、**[マップ]** を選択します。

    > [!TIP]
    > 複数選択を行うには、__Ctrl__ キーを押しながら各列の見出しを選択します。

    ![マップの視覚化の画像](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. マップの視覚化を最大限で行うには、**最大化**のアイコンをクリックします。 マップをウィンドウに合わせるには、視覚化機能の左上にある **[E]** アイコンを選択します。

    ![最大化の画像](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. **[最小化]** ボタンをクリックすると、グリッドに戻ります。

## <a name="use-column-statistics-inspector"></a>列の統計インスペクターの使用

列の統計インスペクターを使用するには、__tripduration__ 列を右クリックし、__[Column Statistics]\(列の統計)__ を選択します。

![列の統計エントリ](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

このプロセスでは、__[INSPECTORS]\(インスペクター\)__ ウィンドウにある __[tripduration Statistics]\(旅行時間の統計\)__ というタイトルの新しい視覚化を追加します。

![旅行時間の統計インスペクターの画像](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> 旅行時間の最大値は **961,814 分**であり、約 2 年間に相当します。 データセットにいくつかの外れ値がありそうです。

## <a name="use-histogram-inspector"></a>ヒストグラム インスペクターの使用

外れ値の特定を試みるには、__tripduration__ 列を右クリックして、__[ヒストグラム]__ を選択します。

![ヒストグラム インスペクター](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

外れ値がグラフを歪めてしまうので、ヒストグラムは役立ちません。

## <a name="add-column-using-script"></a>スクリプトを使用した列の追加

1. **tripduration**列を右クリックして、**[列の追加 (スクリプト)]** を選択します。

    ![列の追加 (スクリプト) メニューの画像](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. __[列の追加 (スクリプト)]__ ダイアログ ボックスでは、次の値を使用します。

    * __[新しい列名]__: logtripduration
    * __[Insert this New Column After]\(の次にこの新しい列を挿入します\)__: tripduration
    * __[New Column Code]\(新しい列のコード\)__: `math.log(row.tripduration)`
    * __[Code Block Type]\(コード ブロックの種類\)__: 式

   ![列の追加 (スクリプト) ダイアログ](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. __[OK]__を選択して **logtripduration** 列を追加します。

4. 列を右クリックして、**[ヒストグラム]** を選択します。

    ![logtripduration 列のヒストグラム](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  視覚的には、このヒストグラムは末尾が異常な正規分布のように見えます。

## <a name="use-advanced-filter"></a>高度なフィルターの使用

データにフィルターを使用すると、インスペクターが新しい分布に更新されます。 **logtripduration** 列を右クリックして、**[列フィルター]** を選択します。 __[編集]__ ダイアログで、次の値を使用します。

* __[Filter this Number Column]\(この数値列をフィルターする\)__: logtripduration
* __[I Want To]\(次の設定を使用する\)__: Keep Rows\(行の保持\)
* __[When\(タイミング\)]__: Any of the Conditions below are True (logical OR)\(以下のいずれかの条件が True になっている (論理和)\)
* __[If this Column]\(この列の条件\)__: less than\(未満\)
* __[The Value]\(値\)__: 9

![フィルター オプション](media/tutorial-bikeshare-dataprep/loftripfilter.png)

__[OK]__ を選択してフィルターを適用します。

![フィルターが適用された後の更新後のヒストグラム](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>ハロー効果

1. **logtripduration** のヒストグラムを最大化します。 灰色のヒストグラムに重なった青いヒストグラムがあります。 この表示は**ハロー効果**と呼ばれています。

    * **灰色のヒストグラム**は、処理 (ここでは、フィルター処理) 前の分布を表します。
    * **青いヒストグラム**は、処理後のヒストグラムを表します。 

   ハロー効果は、データに対する処理の効果を視覚化するうえで役立ちます。

   ![ハロー効果の画像](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > 青いヒストグラムが、前の例に比べて短く表示されていることに気が付きます。 これは、データが新しい範囲に自動的に再調整されたためです。

2. ハローを削除するには、__[編集]__ を選択して__[Show halo]\(ハローの表示\)__ チェックボックスをオフにします。

    ![ヒストグラムのオプション](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. **[OK]** を選択してハロー効果を無効にし、ヒストグラムを最小化します。

### <a name="remove-columns"></a>列の削除

旅行データの場合、各行は自転車の受け取りイベントを表します。 このチュートリアルでは、**starttime** と **start station** 列のみが必要です。 これら 2 つの列を複数選択して、列見出しを右クリックし、**[Keep Column]\(列を保持する\)** を選択することで、他の列を除去します。 その他の列が削除されます。

![列の保持オプションの画像](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>データの集計 (数)

2 時間の自転車の需要を集計するには、派生列を使用します。

1. **starttime** 列を右クリックし、**[Derive Column by Example]\(列の派生の例\)** を選択します。

    ![派生の例オプションの画像](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. 例では、最初の行に `Jan 01, 2017 12AM-2AM` という値を入力します。

    > [!IMPORTANT]
    > 前の例の派生列では、日時の期間を含む列を派生するために、複数のステップを使用しました。 この例では、最終的な出力の例を提供することで、この処理が 1 つのステップとして実行できることがわかります。

    > [!NOTE]
    > 任意の行に対して、例を付与することができます。 この例では、値 `Jan 01, 2017 12AM-2AM` はデータの最初の行に対して有効です。

    ![例のデータの画像](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

3. アプリケーションがすべての行に対する値を計算するまで待機します。 これには数秒かかる場合があります。 分析が完了したら、__[Review next suggested row]\(提示された次の行を確認する\)__  リンクを使用してデータを確認します。

   ![確認リンクがある完了した分析の画像](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    計算された値が正しいことを確認します。 正しくない場合は、目的の値で更新して Enter キーを押します。 その後、分析が完了するまで待機します。 **[No suggestions]\(提示なし\)** を確認するまで、**[Review next suggested row]\(提示された次の行を確認する\)** プロセスを完了します。 **[No suggestions]\(提示なし\)** が確認できたら、アプリケーションはエッジ ケースを検出して、合成されたプログラムが出来上がります。 変換を受け入れる前に、変換されたデータを視覚化して検査することをお勧めします。 

4. **[OK]** を選択して、変換を受け入れます。 新しく作成された列の名前を **Date Hour Range** に変更します。

    ![名前が変更された列の画像](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. **starttime** 列の見出しを右クリックし、**[列の削除]** を選択します。

6. データを集計するには、__[変換]__ メニューから __[集計]__を選択します。 変換を作成するには、次の操作を実行します。

    * __Date Hour Range__ および __start station id__ を左側のウィンドウ (グループ化) にドラッグします。
    * __start station id__ を右側のウィンドウ (集計されたデータ) にドラッグします。

   ![集計オプションの画像](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. **[OK]** を選択して、集計結果を受け入れます。

## <a name="join-dataflows"></a>データフローを結合する

気象データと旅行データを結合するには、以下の手順に従います。

1. __[変換]__ メニューから __[結合]__ を選択します。

2. __[テーブル]__: **BostonWeather** を左側のデータフローとして、**201701 hubway-tripdata** を右側のデータフローとして選択します。 **[次へ]** を選択して、続行します。

    ![テーブルの選択の画像](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __[キー列]__: 両方のテーブルの **Date Hour Range** 列を選択して、__[次へ]__ を選択します。

    ![キー列での結合の画像](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __[結合の種類]__: 結合の種類として__[Matching rows]\(一致する行\)__ を選択し、__[完了]__ を選択します。

    ![結合の種類が [一致する行] になっている](media/tutorial-bikeshare-dataprep/joinscreen.png)

    このプロセスによって、__Join Result\(結合結果\)__ という名前の新しいデータフローが作成されます。

## <a name="create-additional-features"></a>追加機能の作成

1. 曜日を格納する列を作成するには、**Date Hour Range** 列を右クリックし、**[Derive Column by Example]\(列の派生の例\)** を選択します。 日曜日だった日付には、"__Sun__" という値を使用します。 たとえば、**Jan 01, 2017 12AM-2AM**です。 **Enter** キーを押して、**[OK]** をクリックします。 この列の名前を __Weekday__ に変更します。

    ![曜日を格納する新しい列の作成の画像](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. 1 つの行に対応する時間単位の期間を格納する列を作成するには、**Date Hour Range** 列を右クリックし、**[Derive Column by Example]\(列の派生の例\)** を選択します。 **Jan 01, 2017 12AM-2AM** を含む行に対して、値 **12AM-2AM** を使用します。 **Enter** キーを押して、**[OK]** をクリックします。 この列の名前を **Period** に変更します｡

    ![期間の列の画像](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. **Date Hour Range** 列と **rDate Hour Range** 列を削除するには、**Ctrl** キーを押しながら各列の見出しを選択します。 右クリックして、**[列の削除]** を選択します。

## <a name="read-data-from-python"></a>Python のデータの読み取り

Python または PySpark から、データ準備のパッケージを実行して、**データ フレーム**として結果を取得します。

Python スクリプトの例を生成するには、__BikeShare Data Prep__ を右クリックして、__[Generate Data Access Code File]\(データ アクセス コード ファイルの生成\)__ を選択します。 Python ファイルの例が **[プロジェクト フォルダー]** に作成され、ワークベンチ内のタブにも読み込まれます。 以下の Python スクリプトは、生成されたコードの例です。

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

このチュートリアルでは、ファイルの名前は `BikeShare Data Prep.py` です。 このファイルは、このチュートリアルで後から使用します。

## <a name="save-test-data-as-a-csv-file"></a>テスト データを CSV ファイルとして保存します。

**Join Result\(結合結果\)** データフローを CSV ファイルに保存するには`BikeShare Data Prep.py` スクリプトを変更する必要があります。 次のコードを使用して、Python スクリプトを更新します。

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

画面の上部の **[実行]** を選択します。 スクリプトが 1 つの**ジョブ**としてローカル コンピューターに送信されます。 ジョブの状態が __[完了]__ に変わって、指定した場所にファイルが書き込まれました。

## <a name="substitute-data-sources"></a>データ ソースの置き換え

前の手順で、テスト データの準備に `201701-hubway-tripdata.csv` および `BostonWeather.csv` データ ソースを使用しました。 他の旅行データ ファイルと共にパッケージを使用するには、次の手順に従います。

1. プロセスに以下の変更を加え、前述した手順を使用して新しい**データ ソース**を作成します。

    * __[ファイルの選択]__: ファイルの選択時に、残りの旅行データの CSV ファイル 6 個を複数選択します。

        ![残りの 6 個のファイルを読み込む](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > __[+5]__ エントリは、表示されているファイル以外に、さらに 5 つのファイルがあることを示します。

    * __[ファイルの詳細]__: __[Promote Headers Mode]\(ヘッダーの昇格モード\)__ を **[All Files Have The Same Headers]\(全ファイルで同じヘッダーにする\)** に設定します。 この値は、各ファイルに同じヘッダーが含まれていることを示します。

        ![ファイルの詳細の選択](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   後の手順で使用するために、このデータ ソースの名前を保存します。

2. フォルダーのアイコンを選択して、プロジェクト内のファイルを表示します。 __aml\_config__ ディレクトリを展開して、`local.runconfig` ファイルを選択します。

    ![local.runconfig の場所の画像](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. `local.runconfig` ファイルの末尾に次の行を追加し、ディスク アイコンを選択してファイルを保存します。

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    この変更によって、元のデータ ソースが 6 個の旅行データ ファイルを含むデータ ソースに置き換えられます。

## <a name="save-training-data-as-a-csv-file"></a>トレーニング データを CSV ファイルとして保存する

以前に編集した Python ファイル `BikeShare Data Prep.py` に移動し、別のファイル パスを提供してトレーニング データを保存します。

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

新しいジョブを送信するには、ページの上部にある **[実行]** アイコンを使用します。 新しい設定で**ジョブ**が送信されます。 このジョブの出力はトレーニング データです。 このデータは、先ほど作成したときと同じデータの準備手順を使用して、作成されています。 ジョブが完了するまで数分かかる場合があります。

## <a name="next-steps"></a>次のステップ
自転車シェアのデータ準備のチュートリアルを完了しました。 このチュートリアルでは、以下の方法を説明するために、Azure Machine Learning サービス (プレビュー) を使用しました。
> [!div class="checklist"]
> * Azure Machine Learning データ準備ツールを使用して対話形式でデータを準備する
> * テスト データセットをインポート、変換、および作成する
> * データ準備パッケージを生成する
> * Python を使用してデータ準備パッケージを実行する
> * 追加の入力ファイルのデータ準備パッケージを再利用して、トレーニング データセットを生成する

次は、データ準備の詳細を確認してください。
> [!div class="nextstepaction"]
> [データ準備ユーザー ガイド](data-prep-user-guide.md)
