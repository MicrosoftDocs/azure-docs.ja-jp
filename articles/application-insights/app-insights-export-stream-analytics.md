---
title: "Azure Application Insights からの Stream Analytics のエクスポート | Microsoft Docs"
description: "Stream Analytics は、Application Insights からエクスポートされたデータを継続的に変換、フィルター処理、ルーティングできます。"
services: application-insights
documentationcenter: 
author: noamben
manager: douge
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: be47e9de14700b66a3f1c59554762303987f67be


---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Application Insights からエクスポートされたデータを、Stream Analytics を使って処理する
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) は、[Application Insights からエクスポートされた](app-insights-export-telemetry.md)データを処理するのに理想的なツールです。 Stream Analytics は、さまざまなソースからデータを取り込むことができます。 Stream Analytics は、データを変換してフィルター処理し、さまざまなシンクにルーティングできます。

この例では、Application Insights からデータを取得し、一部のフィールドの名前を変更して処理した後、Power BI にパイプするアダプターを作成します。

> [!WARNING]
> [Power BI で Application Insights のデータを表示するには、はるかに優れた簡単な方法](app-insights-export-power-bi.md)があります。 ここで説明するパスは、エクスポートされたデータを処理する方法を示すための例に過ぎません。
> 
> 

![SA を介した PBI へのエクスポートのブロック図](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Azure でのストレージの作成
連続エクスポートでは、常に Azure のストレージ アカウントにデータが出力されるため、まずストレージを作成する必要があります。

1. [Azure ポータル](https://portal.azure.com)で、サブスクリプションの "クラシック" ストレージ アカウントを作成します。
   
   ![Azure ポータルで、[新規]、[データ]、[Storage] の順に選択します](./media/app-insights-export-stream-analytics/030.png)
2. コンテナーを作成する
   
    ![新しいストレージで、[コンテナー] を選択し、[コンテナー] タイルをクリックし、[追加] を選択します](./media/app-insights-export-stream-analytics/040.png)
3. ストレージ アクセス キーのコピー
   
    これは、ストリーム分析サービスへの入力のセットアップのためにすぐに必要になります。
   
    ![ストレージで、[設定]、[キー] の順に開き、プライマリ アクセス キーのコピーを取ります](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure Storage への連続エクスポートの開始
[連続エクスポート](app-insights-export-telemetry.md) は、Application Insights から Azure のストレージにデータを移動します。

1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。
   
    ![[参照]、[Application Insights]、アプリケーションの順に選択します](./media/app-insights-export-stream-analytics/050.png)
2. 連続エクスポートを作成します。
   
    ![[設定]、[連続エクスポート]、[追加] の順に選択します](./media/app-insights-export-stream-analytics/060.png)

    以前に作成したストレージ アカウントを選択します。

    ![エクスポート先の設定](./media/app-insights-export-stream-analytics/070.png)

    表示するイベントの種類を設定します。

    ![イベントの種類の選択](./media/app-insights-export-stream-analytics/080.png)

1. データを蓄積します。 しばらく待機し、ユーザーにアプリケーションを使用してもらいます。 テレメトリが開始し、統計グラフが[メトリックス エクスプローラー](app-insights-metrics-explorer.md)に表示され、個々のイベントが[診断検索](app-insights-diagnostic-search.md)に表示されます。 
   
    また、データはストレージにもエクスポートされます。 
2. エクスポートされたデータを検査します。 Visual Studio で、**[表示]、[Cloud Explorer]** の順に選びます。[Azure]、[Storage] の順に開きます  (このメニュー オプションがない場合は、Azure SDK をインストールする必要があります。[新しいプロジェクト] ダイアログを開き、[Visual c#]、[クラウド]、[Microsoft Azure SDK for .NET の取得] の順に開きます)。
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    パス名の共通部分を書き留めます。共通部分はアプリケーションの名前とインストルメンテーション キーから派生します。 

イベントが JSON 形式で BLOB ファイルに書き込まれます。 各ファイルに&1; つ以上のイベントが含まれる場合があります。 このため、イベント データを読み取って必要なフィールドをフィルター処理します。 データの処理に関して実行できることは多数ありますが、今日の計画は、Stream Analytics を使用してデータを Power BI に移動することです。

## <a name="create-an-azure-stream-analytics-instance"></a>Azure Stream Analytics インスタンスの作成
[従来の Azure ポータル](https://manage.windowsazure.com/)で、Azure Stream Analytics サービスを選択し、新しい Stream Analytics ジョブを作成します。

![](./media/app-insights-export-stream-analytics/090.png)

![](./media/app-insights-export-stream-analytics/100.png)

新しいジョブが作成された後、その詳細を展開します。

![](./media/app-insights-export-stream-analytics/110.png)

### <a name="set-blob-location"></a>BLOB の場所の設定
連続エクスポート BLOB から入力を取るよう設定します。

![](./media/app-insights-export-stream-analytics/120.png)

ここで、ストレージ アカウントからのプライマリ アクセス キーが必要になります。これは前にメモしておいたものです。 ストレージ アカウント キーとしてこれを設定します。

![](./media/app-insights-export-stream-analytics/130.png)

### <a name="set-path-prefix-pattern"></a>パスのプレフィックス パターンの設定
![](./media/app-insights-export-stream-analytics/140.png)

**日付の書式は YYYY-MM-DD (ダッシュ付き) に設定してください。**

パスのプレフィックス パターンは、Stream Analytics がストレージ内の入力ファイルを検索する場所を指定します。 連続エクスポートによるデータ格納方法と一致するように設定する必要があります。 次のように設定します。

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

次の点に注意してください。

* ** は Application Insights リソースの名前です。`webapplication27`すべて小文字で指定します**。
* ** は Application Insights リソースのインストルメンテーション キーです。`1234...`ダッシュは省略します**。 
* `PageViews` は分析するデータの種類です。 使用可能な種類は、連続エクスポートで設定するフィルターによって異なります。 エクスポートされたデータを調べて、その他の使用可能な種類を確認します。「[Application Insights エクスポート データ モデル](app-insights-export-data-model.md)」をご覧ください。
* `/{date}/{time}` はそのまま書き込まれるパターンです。

> [!NOTE]
> ストレージを検査して、正しいパスを取得されることを確認してください。
> 
> 

### <a name="finish-initial-setup"></a>初期セットアップの完了
シリアル化の書式を確認します。

![[確認]をクリックしてウィザードをして閉じます](./media/app-insights-export-stream-analytics/150.png)

ウィザードを閉じ、セットアップが完了するまで待機します。

> [!TIP]
> サンプルのコマンドを使用し、データをダウンロードします。 クエリをデバッグするために、それをテスト サンプルとして保存します。
> 
> 

## <a name="set-the-output"></a>出力の設定
では、ジョブを選択し、出力を設定しましょう。

![新しいチャネルを選択して、[出力]、[追加]、[Power BI] をクリックします。](./media/app-insights-export-stream-analytics/160.png)

**作業または学校のアカウント** を指定し、Power BI リソースにアクセスする許可を Stream Analytics に与えます。 次に、出力に名前を付け、ターゲット Power BI データセットと表に名前を付けます。

![3 つの名前を作成します。](./media/app-insights-export-stream-analytics/170.png)

## <a name="set-the-query"></a>クエリの設定
クエリでは、入力から出力への変換を制御します。

![ジョブを選択し、[クエリ] をクリックします。 以下のサンプルを貼り付けます。](./media/app-insights-export-stream-analytics/180.png)

テスト機能を使用し、出力が正しいことを確認します。 入力ページから取得したサンプル データを指定します。 

### <a name="query-to-display-counts-of-events"></a>イベントの数を表示するためのクエリ
このクエリを貼り付けます。

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-input は、ストリーム入力に付けたエイリアスです。
* pbi-output は、定義した出力エイリアスです。
* イベントの名前は JSON 配列にネストされているため、 [OUTER APPLY GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) を使用します。 Select でイベント名と、期間内にその名前を持つインスタンスの個数を取得します。 [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) 句では、1 分という期間内に要素をグループ化します。

### <a name="query-to-display-metric-values"></a>メトリックの値を表示するためのクエリ
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* このクエリはメトリックス テレメトリをドリルダウンし、イベント時刻とメトリック値を取得します。 メトリック値は配列内に置かれます。そのため、OUTER APPLY GetElements パターンを使用し、行を抽出します。 「myMetric」がこのケースのメトリックの名前になります。 

### <a name="query-to-include-values-of-dimension-properties"></a>ディメンション プロパティの値を追加するクエリ
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* このクエリは、次元配列の固定インデックスにある特定のディメンションに依存せず、ディメンション プロパティの値を追加します。

## <a name="run-the-job"></a>ジョブを実行する
ジョブを開始する過去の日付を選択できます。 

![ジョブを選択し、[クエリ] をクリックします。 以下のサンプルを貼り付けます。](./media/app-insights-export-stream-analytics/190.png)

ジョブが実行されるまで待ちます。

## <a name="see-results-in-power-bi"></a>Power BI で結果を確認します。
> [!WARNING]
> [Power BI で Application Insights のデータを表示するには、はるかに優れた簡単な方法](app-insights-export-power-bi.md)があります。 ここで説明するパスは、エクスポートされたデータを処理する方法を示すための例に過ぎません。
> 
> 

職場または学校のアカウントで Power BI を開き、Stream Analytics ジョブの出力として定義したデータセットとテーブルを選択します。

![Power BI で、データセットとフィールドを選択します。](./media/app-insights-export-stream-analytics/200.png)

このデータセットを、 [Power BI](https://powerbi.microsoft.com)のレポートやダッシュボードで使用できるようになりました。

![Power BI で、データセットとフィールドを選択します。](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>データが表示されない場合
* [日付の書式が YYYY-MM-DD (ダッシュ付き) に正しく設定されている](#set-path-prefix-pattern) ことを確認してください。

## <a name="video"></a>ビデオ
Noam Ben Zeev が、Stream Analytics を使ってエクスポートされたデータを処理する方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>次のステップ
* [連続エクスポート](app-insights-export-telemetry.md)
* [データ モデルについては、プロパティの型と値のリファレンスで詳しく説明されています。](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)
* [その他のサンプルとチュートリアル](app-insights-code-samples.md)




<!--HONumber=Jan17_HO5-->


