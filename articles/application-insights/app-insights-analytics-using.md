---
title: "Analytics の使用 - Azure Application Insights の強力な検索ツール | Microsoft Docs"
description: "Application Insights の強力な診断検索ツールである Analytics を使用します。 "
services: application-insights
documentationcenter: 
author: danhadari
manager: carmonm
ms.assetid: c3b34430-f592-4c32-b900-e9f50ca096b3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: d7f6f9582a3d15563c19d69845836a92a35ee95e
ms.lasthandoff: 03/16/2017


---
# <a name="using-analytics-in-application-insights"></a>Application Insights の Analytics の使用
[Analytics](app-insights-analytics.md) は、[Application Insights](app-insights-overview.md) の強力な検索機能です。 ここでは、Analytics のクエリ言語について説明します。

* **[紹介ビデオを見る](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**。
* **[シミュレーション データで Analytics を試す](https://analytics.applicationinsights.io/demo)** (ご使用のアプリからまだ Application Insights にデータが送信されていない場合)。

## <a name="open-analytics"></a>Analytics を開く
Application Insights のアプリのホーム リソースで、[Analytics] をクリックします。

![portal.azure.com で Application Insights リソースを開き、[Analytics] をクリックします。](./media/app-insights-analytics-using/001.png)

インラインのチュートリアルでは、実行可能な操作が説明されています。

[ここにはさらに広範なツアー](app-insights-analytics-tour.md)があります。

## <a name="query-your-telemetry"></a>テレメトリのクエリを実行する
### <a name="write-a-query"></a>クエリを記述します
![スキーマの表示](./media/app-insights-analytics-using/150.png)

左側の一覧に表示されるテーブルのいずれかの名前 (または [range](app-insights-analytics-reference.md#range-operator) や [union](app-insights-analytics-reference.md#union-operator) 演算子) から始めます。 `|` を使用して、 [演算子](app-insights-analytics-reference.md#queries-and-operators)のパイプラインを作成します。 

IntelliSense によって、使用できる演算子と、式の要素が示されます。 情報アイコンをクリックする (または Ctrl キーを押しながら Space キーを押す) と、より詳しい説明と各要素の使用方法の例が表示されます。

[Analytics 言語のツアー](app-insights-analytics-tour.md)と[言語のリファレンス](app-insights-analytics-reference.md)に関するページを参照してください。

### <a name="run-a-query"></a>クエリを実行する
![Running a query](./media/app-insights-analytics-using/130.png)

1. クエリでは単一改行を使用できます。
2. 実行するクエリの内部または末尾にカーソルを置きます。
3. クエリの時間範囲を確認します。 (変更するか、クエリに独自の [`where...timestamp...`](app-insights-analytics-tour.md#time-range) 句を含めることによってオーバーライドできます。)
3. [実行] をクリックしてクエリを実行します。
4. クエリに空白行を入れないでください。 いくつかの個別のクエリを、空白行で区切ることによって、1 つのクエリ タブに保持することができます。 カーソルのあるクエリのみが実行されます。

### <a name="save-a-query"></a>クエリを保存する
![Saving a query](./media/app-insights-analytics-using/140.png)

1. 現在のクエリ ファイルを保存します。
2. 保存したクエリ ファイルを開きます。
3. 新しいクエリ ファイルを作成します。

## <a name="see-the-details"></a>詳細を表示する
結果の任意の行を展開すると、プロパティの完全な一覧が表示されます。 値が構造化されているプロパティ (たとえば、カスタム ディメンション、例外に一覧表示されるスタックなど) は、さらに展開することができます。

![Expand a row](./media/app-insights-analytics-using/070.png)

## <a name="arrange-the-results"></a>結果を配置する
クエリから返された結果は、並べ替え、フィルター処理、改ページ、およびグループ化することができます。

> [!NOTE]
> ブラウザーでの並べ替え、グループ化、およびフィルター処理では、クエリは再実行されません。 最後のクエリによって返された結果を再配置するだけです。 
> 
> 結果が返される前に、サーバーでこれらのタスクを実行するには、[sort](app-insights-analytics-reference.md#sort-operator)、[summarize](app-insights-analytics-reference.md#summarize-operator)、および [where](app-insights-analytics-reference.md#where-operator) 演算子を使用してクエリを記述します。
> 
> 

表示する列を選択し、列ヘッダーをドラッグして再配置したり、境界をドラッグして列の幅を変更したりします。

![Arrange columns](./media/app-insights-analytics-using/030.png)

### <a name="sort-and-filter-items"></a>項目の並べ替えとフィルター処理を行う
結果を並べ替えるには、列のヘッダーをクリックします。 もう一度クリックすると、もう 1 つの方法で並べ替えられ、3 回目のクリックで、クエリから返された元の順序に戻ります。

検索を絞り込むには、フィルター アイコンを使用します。

![Sort and filter columns](./media/app-insights-analytics-using/040.png)

### <a name="group-items"></a>項目をグループ化する
複数の列で並べ替えるには、グループ化を使用します。 まず有効にしてから、列ヘッダーをテーブルの上の領域にドラッグします。

![グループ](./media/app-insights-analytics-using/060.png)

### <a name="missing-some-results"></a>結果の一部が表示されない場合

予期したすべての結果が表示されていないと思われる場合は、いくつかの原因が考えられます。

* **時間範囲フィルター**。 既定では、過去 24 時間の結果のみが表示されます。 自動フィルターにより、元のテーブルから取得された結果の範囲が制限されます。 

    ただし、ドロップダウン メニューを使用して時間範囲フィルターを変更できます。

    または、クエリに独自の [`where  ... timestamp ...` 句](app-insights-analytics-reference.md#where-operator)を含めることによって自動範囲をオーバーライドできます。 次に例を示します。

    `requests | where timestamp > ago('2d')`

* **結果の制限**。 ポータルから返される結果には、約 10k 行という制限があります。 制限を超えると、警告が表示されます。 そうなった場合、テーブルで結果を並べ替えると、実際の結果のうちの先頭または末尾の部分が表示されないことがあります。 

    制限に達しないようにすることをお勧めします。 時間範囲フィルターを使用するか、次のような演算子を使用します。

  * [top 100 by timestamp](app-insights-analytics-reference.md#top-operator) 
  * [take 100](app-insights-analytics-reference.md#take-operator)
  * [summarize ](app-insights-analytics-reference.md#summarize-operator) 
  * [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)

(10,000 を超える数の行が必要な場合は、 [連続エクスポート](app-insights-export-telemetry.md)を代わりに使用することを検討してください。 Analytics は生データの取得ではなく分析を目的として設計されています。)

## <a name="diagrams"></a>ダイアグラム
必要なダイアグラムの種類を選択します。

![ダイアグラムの種類の選択](./media/app-insights-analytics-using/230.png)

種類が適切な複数の列がある場合は、x 軸と y 軸を選択して、結果を分割するディメンションの列を選択できます。

既定では、結果は最初にテーブルとして表示されるので、ダイアグラムを手動で選択します。 ただし、クエリの末尾に [render ディレクティブ](app-insights-analytics-reference.md#render-directive) を使用して、ダイアグラムを選択できます。

## <a name="pin-to-dashboard"></a>Pin to dashboard
ピン アイコンをクリックするだけで、いずれかの[共有ダッシュボード](app-insights-dashboards.md)にダイアグラムまたはテーブルをピン留めすることができます。 (この機能を有効にするには、[アプリの料金パッケージのアップグレード](app-insights-pricing.md)が必要になる場合があります)。 

![ピンのクリック](./media/app-insights-analytics-using/pin-01.png)

つまり、Web サービスのパフォーマンスや使用状況の監視に利用するダッシュボードを構成するときに、複雑な分析を他のメトリックに一緒に組み込むことができます。 

列の数が 4 列以下のテーブルはダッシュボードにテーブルをピン留めすることができます。 最初の 7 行のみが表示されます。

### <a name="dashboard-refresh"></a>ダッシュボードの更新
ダッシュボードにピン留めされているグラフは、約 2 時間おきにクエリが再実行されるときに自動的に更新されます。

### <a name="automatic-simplifications"></a>自動簡略化

グラフをダッシュボードにピン留めする際は、特定の簡略化が適用されます。

**時間の制限:** クエリの対象は自動的に過去 14 日間のものに制限されます。 結果は、クエリに `where timestamp > ago(14d)` が含まれている場合と同じです。

**ビン数の制限:** 多数の不連続ビン (通常は横棒グラフ) が表示されているグラフを表示すると、割合の低いビンは "その他" のビンに自動的にグループ化されます。 次のクエリを例にします。

    requests | summarize count_search = count() by client_CountryOrRegion

上のクエリは、Analytics では次のようになります。

![底部が長いグラフ](./media/app-insights-analytics-using/pin-07.png)

ところが、ダッシュボードにピン留めすると次のようになります。

![ビンが制限されたグラフ](./media/app-insights-analytics-using/pin-08.png)

## <a name="export-to-excel"></a>Excel へのエクスポート
クエリを実行したら、.csv ファイルをダウンロードできます。 **[エクスポート]、[Excel]** の順にクリックします。

## <a name="export-to-power-bi"></a>Power BI へのエクスポート
クエリにカーソルを置いて、**[エクスポート]、[Power BI]** の順に選択します。

![Analytics から Power BI へのエクスポート](./media/app-insights-analytics-using/240.png)

Power BI でクエリを実行します。 スケジュールに従って更新するように設定できます。

Power BI では、さまざまなソースのデータをまとめるダッシュボードを作成できます。

[Power BI へのエクスポートの詳細については、こちら をご覧ください。](app-insights-export-power-bi.md)

## <a name="deep-link"></a>ディープ リンク

**[エクスポート]、[リンクの共有]** の順に選択して、別のユーザーに送信できるリンクを取得します。 ユーザーが[リソース グループへのアクセス権](app-insights-resources-roles-access-control.md)を所有していれば、クエリが Analytics UI で開きます。

(リンクでは、クエリ テキストは "?q=" の後ろに gzip 方式で圧縮され、Base-64 でエンコードされて表示されます。 ユーザーに提供するディープ リンクを生成するコードを記述できます。 ただし、Analytics をコードから実行するための推奨する方法は、[REST API](https://dev.applicationinsights.io/) を使用することです。)


## <a name="automation"></a>Automation

Analytics クエリを実行するには、[データ アクセス REST API](https://dev.applicationinsights.io/) を使用します。 [例](https://dev.applicationinsights.io/apiexplorer/query?appId=DEMO_APP&apiKey=DEMO_KEY&query=requests%0A%7C%20where%20timestamp%20%3E%3D%20ago%2824h%29%0A%7C%20count) (PowerShell 使用):

```PS
curl "https://api.applicationinsights.io/beta/apps/DEMO_APP/query?query=requests%7C%20where%20timestamp%20%3E%3D%20ago(24h)%7C%20count" -H "x-api-key: DEMO_KEY"
```

Analytics UI とは異なり、REST API は、タイムスタンプ制限をクエリに自動的に追加しません。 大量の応答を回避するために、独自の where 句を必ず追加してください。



## <a name="import-data"></a>データのインポート

CSV ファイルからデータをインポートすることができます。 一般的な使用方法は、テーブルと結合できる静的データをテレメトリからインポートすることです。 

たとえば、認証されたユーザーがエイリアスや難読化された ID によってテレメトリで識別されている場合は、エイリアスを実際の名前にマップするテーブルをインポートできます。 要求テレメトリに対して結合を実行することにより、Analytics レポートで実際の名前によってユーザーを識別できます。

### <a name="define-your-data-schema"></a>データ スキーマを定義する

1. **[設定]** (左上にある) に続けて **[データ ソース]** をクリックします。 
2. 指示に従ってデータ ソースを追加します。 データのサンプル (少なくとも 10 行が含まれる必要がある) を提供するよう要求されます。 その後でスキーマを修正します。

これにより、個々 のテーブルをインポートするために使用できるデータ ソースが定義されます。

### <a name="import-a-table"></a>テーブルをインポートする

1. 一覧からデータ ソース定義を開きます。
2. [アップロード] をクリックし、指示に従ってテーブルをアップロードします。 これには REST API への呼び出しが含まれているため、自動化を簡単に実行できます。 

これで、テーブルは Analytics クエリで使用可能になります。 Analytics に表示されます 

### <a name="use-the-table"></a>テーブルを使用する

データ ソース定義の名前が `usermap` で、`realName` および `user_AuthenticatedId` の 2 つのフィールドが含まれているとします。 `requests` テーブルにも `user_AuthenticatedId` という名前のフィールドがあるため、簡単に結合を実行できます。

```AIQL

    requests
    | where notempty(user_AuthenticatedId) | take 10
    | join kind=leftouter ( usermap ) on user_AuthenticatedId 
```
要求の結果テーブルには `realName` という追加の列があります。

### <a name="import-from-logstash"></a>LogStash からインポートする

[LogStash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html) を使用する場合は、Analytics を使用してログのクエリを実行できます。 [Analytics にデータをパイプするプラグイン](https://github.com/Microsoft/logstash-output-application-insights)を使用してください。 

## <a name="video"></a>ビデオ

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


