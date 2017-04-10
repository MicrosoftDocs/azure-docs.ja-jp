---
title: "Application Insights から Power BI へのエクスポート | Microsoft Docs"
description: "Power BI で Analytics クエリを表示できます。"
services: application-insights
documentationcenter: 
author: noamben
manager: douge
ms.assetid: 7f13ea66-09dc-450f-b8f9-f40fdad239f2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 24ccafb4df95e0010416485199e19f81e1ae31aa
ms.openlocfilehash: 11017c7c0a761569892aebcd085d5d3fb2d67a69


---
# <a name="feed-power-bi-from-application-insights"></a>Application Insights のデータを Power BI に入力する
[Power BI](http://www.powerbi.com/) は、データを分析し、洞察を共有できる一連のビジネス分析ツールです。 あらゆるデバイスで機能豊富なダッシュボードを利用できます。 [Azure Application Insights](app-insights-overview.md) の Analytics クエリなど、さまざまなソースのデータを組み合わせることができます。

Application Insights のデータを Power BI にエクスポートする場合、3 つの推奨される方法があります。 これらの方法は個別に使用することも、組み合わせて使用することもできます。

* [**Power BI アダプター**](#power-pi-adapter) - アプリのテレメトリの完全なダッシュボードを設定します。 グラフ セットが事前定義されていますが、他のソースの独自のクエリを追加できます。
* [**Analytics クエリのエクスポート**](#export-analytics-queries) - Analytics を使用して必要なクエリを作成し、Power BI にエクスポートします。 このクエリを他のデータと共にダッシュボードに配置できます。
* [**連続エクスポートと Stream Analytics**](app-insights-export-stream-analytics.md) - この方法では、多くの設定作業が必要になります。 この方法は、データを長期間保持する必要がある場合に便利です。 それ以外の場合は、他の方法をお勧めします。

## <a name="power-bi-adapter"></a>Power BI アダプター
この方法では、テレメトリの完全なダッシュボードが作成されます。 初期データ セットが事前定義されていますが、さらに多くのデータを追加できます。

### <a name="get-the-adapter"></a>アダプターの入手
1. [Power BI](https://app.powerbi.com/) にサインインします。
2. **[データを取得]**、**[サービス]**、**[Application Insights]** の順に開きます。
   
    ![Application Insights データ ソースから取得する](./media/app-insights-export-power-bi/power-bi-adapter.png)
3. Application Insights リソースの詳細を指定します。
   
    ![Application Insights データ ソースから取得する](./media/app-insights-export-power-bi/azure-subscription-resource-group-name.png)
4. データがインポートされるまで&1; ～&2; 分待ちます。
   
    ![Power BI アダプター](./media/app-insights-export-power-bi/010.png)

Application Insights のグラフを他のソースのグラフや Analytics クエリと組み合わせて、ダッシュボードを編集できます。 さらに多くのグラフを入手できるビジュアル ギャラリーがあり、各グラフにはユーザーが設定できるパラメーターがあります。

最初のインポート後は、ダッシュボードとレポートが毎日更新されます。 データセットの更新スケジュールを管理できます。

## <a name="export-analytics-queries"></a>Analytics クエリのエクスポート
この方法では、必要な Analytics クエリを作成し、Power BI ダッシュボードにエクスポートできます  (アダプターによって作成されたダッシュボードに追加できます)。

### <a name="one-time-install-power-bi-desktop"></a>1 回限り: Power BI Desktop のインストール
Application Insights のクエリをインポートするには、Power BI のデスクトップ バージョンを使用します。 ただし、その後、Web または Power BI クラウド ワークスペースにクエリを発行できます。 

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) をインストールします。

### <a name="export-an-analytics-query"></a>Analytics クエリのエクスポート
1. [Analytics を開き、クエリを作成](app-insights-analytics-tour.md)します。
2. 満足のいく結果が得られるまで、クエリをテストして改善します。

   **エクスポートする前に、クエリが Analytics で正しく実行されることを確認します。**
3. **[エクスポート]** メニューの **[Power BI (M)]** を選択します。 テキスト ファイルを保存します。
   
    ![Power BI クエリをエクスポートする](./media/app-insights-export-power-bi/analytics-export-power-bi.png)
4. Power BI Desktop で **[データを取得]、[空のクエリ]** の順に選択し、クエリ エディターで **[表示]** の **[詳細クエリ エディター]** を選択します。

    エクスポートした M 言語スクリプトを詳細クエリ エディターに貼り付けます。

    ![詳細クエリ エディター](./media/app-insights-export-power-bi/power-bi-import-analytics-query.png)

1. Power BI に Azure へのアクセスを許可するために、資格情報を指定することが必要な場合があります。 [組織アカウント] を使用して、Microsoft アカウントでサインインします。
   
    ![Power BI で Application Insights のクエリを実行できるように、Azure の資格情報を指定する](./media/app-insights-export-power-bi/power-bi-import-sign-in.png)

    (資格情報を確認する必要がある場合は、クエリ エディターの [Data Source Settings (データ ソース設定)] メニュー コマンドを使用します。 Azure 用の資格情報を指定するように注意してください。これは Power BI 用の資格情報とは異なる可能性があります。)
2. クエリの視覚エフェクトを選択し、x 軸、y 軸、セグメント化ディメンションの各フィールドを選択します。
   
    ![視覚エフェクトを選択する](./media/app-insights-export-power-bi/power-bi-analytics-visualize.png)
3. Power BI クラウド ワークスペースにレポートを発行します。 そこから、同期されたバージョンを他の Web ページに埋め込むことができます。
   
    ![視覚エフェクトを選択する](./media/app-insights-export-power-bi/publish-power-bi.png)
4. レポートを周期的に手動で更新するか、オプション ページで更新のスケジュールを設定します。

## <a name="about-sampling"></a>サンプリングについて
アプリケーションで大量のデータを送信すると、アダプティブ サンプリング機能が動作して、テレメトリのごく一部だけが送信される可能性があります。 これは、SDK または取り込みでサンプリングを手動で設定した場合にも当てはまります。 [サンプリングの詳細については、こちらを参照してください。](app-insights-sampling.md)

## <a name="next-steps"></a>次のステップ
* [Power BI - 詳細](http://www.powerbi.com/learning/)
* [Analytics のチュートリアル](app-insights-analytics-tour.md)




<!--HONumber=Feb17_HO2-->


