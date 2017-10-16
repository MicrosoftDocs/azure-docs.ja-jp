---
title: "Azure Log Analytics でのログ検索ポータルの使用 | Microsoft Docs"
description: "この記事のチュートリアルでは、ログ検索ポータルを使用してログ検索を作成し、Log Analytics ワークスペースに保存されたデータを分析する方法を説明します。  このチュートリアルでは、さまざまな種類のデータを返すシンプルなクエリを実行し、結果を分析します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>ログ検索ポータルを使用して Azure Log Analytics でログ検索を作成する

> [!NOTE]
> この記事では、新しいクエリ言語を使用した Azure Log Analytics のログ検索ポータルについて説明します。  新しい言語の詳細、およびワークスペースのアップグレード手順については、「[Upgrade your Azure Log Analytics workspace to new log search (新しいログ検索への Azure Log Analytics ワークスペースのアップグレード)](log-analytics-log-search-upgrade.md)」を参照してください。  
>
> ワークスペースが新しいクエリ言語にアップグレードされていない場合は、ログ検索ポータルの現在のバージョンについて「[ログ検索を使用してデータを探す](log-analytics-log-searches.md)」を参照してください。

この記事のチュートリアルでは、ログ検索ポータルを使用してログ検索を作成し、Log Analytics ワークスペースに保存されたデータを分析する方法を説明します。  このチュートリアルでは、さまざまな種類のデータを返すシンプルなクエリを実行し、結果を分析します。  これは、クエリを直接変更するのではなく、ログ検索ポータルで変更する機能に重点を置いています。  クエリを直接編集する方法の詳細については、[クエリ言語リファレンス](https://go.microsoft.com/fwlink/?linkid=856079)を参照してください。

ログ検索ポータルではなく、高度な分析ポータルで検索を作成するには、「[Getting Started with the Analytics Portal (分析ポータルの概要)](https://go.microsoft.com/fwlink/?linkid=856587)」を参照してください。  どちらのポータルも同じクエリ言語を使用して、Log Analytics ワークスペースの同じデータにアクセスします。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、1 つ以上のソースが接続された Log Analytics ワークスペースが既にあり、クエリで分析するデータが生成されることを前提としています。  

- ワークスペースがない場合は、「[Log Analytics ワークスペースを使って作業を開始する](log-analytics-get-started.md)」の手順を使用して無料で作成できます。
- 1 つ以上の [Windows エージェント](log-analytics-windows-agents.md)または [Linux エージェント](log-analytics-linux-agents.md)をワークスペースに接続します。  

## <a name="open-the-log-search-portal"></a>ログ検索ポータルを開く
最初に、ログ検索ポータルを開きます。  Azure Portal または OMS ポータルのいずれかにアクセスできます。

1. Azure Portal を開きます。
2. Log Analytics に移動し、ワークスペースを選択します。
3. 引き続き Azure Portal で作業を行う場合は **[ログ検索]** を選択します。OMS ポータルを起動する場合は **[OMS ポータル]** を選択して、[ログ検索] ボタンをクリックします。

![[ログの検索] ボタン](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>シンプルな検索を作成する
テーブルのすべてのレコードを返すシンプルなクエリを使用すると、作業データを最も簡単に取得できます。  Windows クライアントまたは Linux クライアントがワークスペースに接続されている場合、データは Event (Windows) テーブルまたは Syslog (Linux) テーブルにあります。

検索ボックスに次のいずれかのクエリを入力し、検索ボタンをクリックします。  

```
Event
```
```
Syslog
```

データは既定のリスト ビューに返されます。ここで、返されたレコードの合計数を確認できます。

![単純なクエリ](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

表示されているのは、各レコードの最初の数個のプロパティだけです。  特定のレコードのすべてのプロパティを表示するには、**[show more]\(詳細を表示\)** をクリックします。

![レコードの詳細](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>時間範囲を設定する
Log Analytics によって収集されたすべてのレコードに **TimeGenerated** プロパティがあります。このプロパティには、レコードの作成日時が格納されています。  ログ検索ポータルのクエリが返すのは、**TimeGenerated** が、画面の左側に表示されている時間範囲内にあるレコードだけです。  

時間フィルターを変更するには、ドロップダウンから選択するか、スライダーを変更します。  スライダーで表示される棒グラフによって、範囲内の時間セグメントごとにレコードの相対数が示されます。  このセグメントは、範囲によって異なります。

既定の時間範囲は **1 日**です。  この値を **7 日**に変更すると、レコードの合計数が増加します。

![日時の範囲](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>クエリのフィルター結果
画面の左側はフィルター ウィンドウです。ここでは、クエリを直接変更せずに、フィルターを追加することができます。  返されたレコードのプロパティが、上位 10 個の値とレコード数と一緒に表示されます。

**Event** を操作する場合は、**[EVENTLEVELNAME]** の **[Error]** の横にあるチェック ボックスをオンにします。   **Syslog** を操作する場合は、**[SEVERITYLEVEL]** の **[err]** の横にあるチェック ボックスをオンにします。  これにより、クエリが次のいずれかの変更され、結果がエラー イベントに制限されます。

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![フィルター](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

レコードのいずれかのプロパティ メニューから **[フィルターに追加]** を選択して、プロパティをフィルター ウィンドウに追加します。

![[フィルターに追加] メニュー](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

同じフィルターを設定するには、フィルターを適用する値を持つレコードのプロパティ メニューから **[フィルター]** を選択します。  

名前が青色で示されているプロパティには、**[フィルター]** オプションのみがあります。  これは、検索条件のインデックスが付けられた "*検索可能*" フィールドです。  灰色のフィールドは "*フリー テキスト検索可能*" フィールドで、**[参照設定を表示]** オプションのみがあります。  このオプションは、その値を持つ任意のプロパティ レコードを返します。

![[フィルター] メニュー](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

1 つのプロパティで結果をグループ化するには、レコード メニューで **[グループ化]** オプションを選択します。  これにより、グラフの結果を表示する [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 演算子がクエリに追加されます。  複数のプロパティでグループ化できますが、クエリを直接編集する必要があります。  **Computer** プロパティの横にあるレコード メニューを選択し、**['Computer' でグループ化]** を選択します。  

![コンピューターでグループ化](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>結果を操作する
ログ検索ポータルには、クエリの結果を操作するためのさまざまな機能が用意されています。  結果の並べ替え、フィルター処理、およびグループ化機能を使用すると、実際のクエリを変更せずに、データを分析できます。  既定では、クエリの結果は並べ替えられません。

フィルター処理および並べ替えの追加オプションが用意されたテーブル フォームでデータを表示するには、**[テーブル]** をクリックします。  

![テーブル ビュー](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

レコードの矢印をクリックすると、そのレコードの詳細が表示されます。

![結果の並べ替え](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

フィールドを並べ替えるには、そのフィールドの列ヘッダーをクリックします。

![結果の並べ替え](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

列の特定の値に基づいて結果をフィルター処理するには、フィルター ボタンをクリックして、フィルター条件を指定します。

![結果のフィルター処理](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

列でグループ化するには、その列ヘッダーを、結果の最上部にドラッグします。  複数の列を最上部にドラッグすると、複数のフィールドでグループ化できます。

![結果のグループ化](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>パフォーマンス データを処理する
Log Analytics ワークスペースの **Perf** テーブルには、Windows エージェントと Linux エージェントの両方のパフォーマンス データが格納されています。  パフォーマンス レコードは他のレコードと同じように見えるため、イベントと同様に、すべてのパフォーマンス レコードを返すシンプルなクエリを記述できます。

```
Perf
```

![パフォーマンス データ](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

ただし、すべてのパフォーマンス オブジェクトとカウンターの何百万ものレコードを返しても、それほど役には立ちません。  上記で使用したのと同じ方法を使用すると、データをフィルター処理できます。また、ログ検索ボックスに、次のクエリを直接入力することもできます。  これにより Windows と Linux の両方のコンピューターのプロセッサ使用率レコードのみが返されます。

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![プロセッサ使用率](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

これによりデータが特定のカウンターに制限されますが、特に便利なフォームには、まだデータが入力されません。  データは折れ線グラフに表示できますが、最初に Computer と TimeGenerated でデータをグループ化する必要があります。  複数のフィールドでグループ化するには、クエリを直接変更する必要があるため、次のようにクエリを変更します。  これにより、**CounterValue** プロパティで [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) 関数が使用され、1 時間ごとに平均値が計算されます。

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![パフォーマンス データのグラフ](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

データが適切にグループ化されたので、[render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) 演算子を追加して、データをビジュアル グラフに表示できます。  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![折れ線グラフ](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>次のステップ

- 「[Getting Started with the Analytics Portal (分析ポータルの概要)](https://go.microsoft.com/fwlink/?linkid=856079)」で、Log Analytics クエリ言語の詳細について確認します。
- ログ検索ポータルと同じクエリを実行し、同じデータにアクセスできる[高度な分析ポータル](https://go.microsoft.com/fwlink/?linkid=856587)を使用するチュートリアルを実行します。
