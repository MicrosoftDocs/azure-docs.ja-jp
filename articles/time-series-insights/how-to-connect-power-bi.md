---
title: 環境を Power BI に接続する - Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights を Power BI に接続して、組織全体でデータを共有、グラフ化、表示する方法について説明します。
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374169"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Azure Time Series Insights Gen2 からのデータを Power BI で視覚化する

Azure Time Series Insights は、クラウド内の時系列データを格納、管理、クエリの実行、および視覚化するためのプラットフォームです。 [Power BI](https://powerbi.microsoft.com) は、組織全体で洞察と結果を共有できる豊富な視覚化機能を備えた、ビジネス分析ツールです。 この 2 つのサービスは統合できるようになりましたので、Power BI の強力なデータの視覚化および共有機能を使用して、Azure Time Series Insights の優れた分析を強化することができます。

学習内容は次のとおりです。

* ネイティブ Azure Time Series Insights コネクタを使用して Azure Time Series Insights を Power BI に接続する
* Power BI で時系列データを使用してビジュアルを作成する
* Power BI にレポートを発行し、組織の他の部門と共有する


## <a name="prerequisites"></a>[前提条件]

* お持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/)にサインアップしてください。
* 最新バージョンの [Power BI Desktop](https://powerbi.microsoft.com/downloads/) をダウンロードしてインストールすること
* [Azure Time Series Insights Gen2 環境](./how-to-provision-manage.md)がある、またはそれを作成する

[環境のアクセス ポリシー](./concepts-access-policies.md)をレビューして、Azure Time Series Insights Gen2 環境への直接アクセスまたはゲスト アクセスができることを確認してください。 

> [!IMPORTANT]
> * 最新バージョンの [Power BI Desktop](https://powerbi.microsoft.com/downloads/) をダウンロードし、インストールしてください。 この記事の手順に従って作業するには、2020 年 12 月 (2.88.321.0) 以上のバージョンの Power BI Desktop がインストールされていることを確認してください。 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Azure Time Series Insights から Power BI デスクトップにデータをエクスポートする

作業を開始するには:

1. Azure Time Series Insights Gen2 Explorer を開き、データをキュレーションします。 これは Power BI にエクスポートされるデータです。
1. 満足できるビューを作成したら、 **[その他の操作]** ドロップダウン メニューに移動して、 **[Power BI に接続]** を選択します。

    [![Azure Time Series Insights Gen2 Explorer でのエクスポート](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. エクスポート用のパラメーターを設定します。

   * **Data format**:**集計データ** または **生イベント** のどちらを Power BI にエクスポートするのかを選択します。 

       > [!NOTE]
       > 生イベントをエクスポートする場合は、そのデータを後で Power BI で集計することができます。 ただし、集計データをエクスポートする場合、Power BI で生データに戻すことはできません。 生イベント レベル データには、25 万までのイベント数の制限があります。

   * **時間範囲**: **固定された** 時間の範囲または **最新の** データのいずれを Power BI で表示するのかを選択します。 固定された時間の範囲を選択すると、グラフ化した検索範囲内のデータが Power BI にエクスポートされます。 最新の時間の範囲を選択すると、選択した検索範囲の最新のデータが Power BI によって取得されます (たとえば、任意の 1 時間のデータをグラフ化して [最新] 設定を選択した場合は、Power BI コネクタによって常に最新の 1 時間のデータのクエリが実行されます)。
  
   * **ストアの種類**: 選択したクエリを **ウォーム ストア** または **コールド ストア** のいずれに対して実行するのかを選択します。 コールドストアとウォームストアの両方にまたがる範囲を選択した場合、ウォームストアには最新のデータのみが含まれるため、クエリは既定でコールドストアにルーティングされます。 storeType パラメーターを手動で変更することはできますが、最良のエクスペリエンスを得るためにはお勧めしません。 

    > [!TIP] 
    > エクスポートするように選択したデータの検索範囲とビューに応じて、推奨されるパラメーターが Azure Time Series Insights Explorer によって自動的に選択されます。 

1. 使用する設定を構成したら、 **[クエリをクリップボードにコピー]** を選択します。

    [![Azure Time Series Insights Explorer のエクスポート モーダル](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Power BI Desktop を起動します。
   
1. **[ホーム]** タブの Power BI Desktop で、左上隅にある **[データの取得]** を選択し、 **[More]** を選択します。

    [![Power BI でデータを取得する](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. 「**Azure Time Series Insights**」を検索し、 **[Azure Time Series Insights (Beta)]** を選択してから、 **[接続]** をクリックします。

    [![Power BI を Azure Time Series Insights に接続する](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    または、 **[Azure]** タブに移動し、 **[Azure Time Series Insights (Beta)]** を選択して、 **[接続]** をクリックします。

1. Azure Time Series Insights Explorer からコピーしたクエリを **[カスタム クエリ]** フィールドに貼り付けて、 **[OK]** を押します。

    [![カスタム クエリを貼り付けて、[OK] を選択する](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  これで、データ テーブルが読み込まれます。 **[読み込み]** を押して、Power BI に読み込みます。 データに何らかの変換を加える場合は、 **[データの変換]** をクリックしてそれが行えるようになりました。 ご利用のデータは、読み込み後に変換することもできます。

    [![テーブル内のデータを確認し、[読み込み] を選択する](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>ビジュアルでレポートを作成する

データを Power BI にインポートしたので、次は視覚化されたレポートを作成します。

1. ウィンドウの左側で、 **[レポート]** ビューが選択されていることを確認します。

    [![[レポート ビュー] アイコンを示すスクリーンショット。](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. **[視覚化]** 列で、希望する視覚化を選択します。 たとえば、 **[折れ線グラフ]** を選択します。 これにより、空の折れ線グラフがキャンバスに追加されます。

1.  **[フィールド]** 一覧で **[_Timestamp]\(_タイムスタンプ\)** を選択し、 **[軸]** フィールドにドラッグして時間を X 軸に沿って表示します。 必ず、 **[軸]** の値として **[_Timestamp]\(_タイムスタンプ\)** に切り替えます (既定は **[日付の階層]** です)。

    [![[_Timestamp]\(_タイムスタンプ\) を選択する](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  再び **[フィールド]** の一覧でプロットする変数を選択し、 **[値]** フィールドにドラッグして値を Y 軸に沿って表示します。 時系列 ID 値を選択し、 **[凡例]** フィールドにドラッグして、グラフ内に複数の行を作成します (時系列 ID ごとに 1 つ)。 これにより、Azure Time Series Insights Explorer で提供されるものと同様のビューがレンダリングされます。 

    [![基本的な折れ線グラフを作成する](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. キャンバスに別のグラフを追加するには、キャンバス上の折れ線グラフの外側の任意の場所をクリックして、このプロセスを繰り返します。

    [![追加のグラフを作成して共有する](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

レポートの作成が完了したら、Power BI Reporting Services にパブリッシュして、自分が属する組織内の他のユーザーと共有することができます。

## <a name="advanced-editing"></a>高度な編集
データセットを既に Power BI に読み込んでいるが、クエリ (日付/時刻や環境 ID パラメーターなど) を変更する場合、Power BI の詳細エディター機能を使用してこれを行うことができます。 **Power Query エディター** を使用して変更を加える方法の詳細については、[Power BI のドキュメント](/power-bi/desktop-query-overview)を参照してください。 

## <a name="next-steps"></a>次の手順

* [Power BI Desktop](/power-bi/desktop-query-overview) の詳細を参照してください。

* Azure Time Series Insights Gen2 での[データのクエリ](concepts-query-overview.md)について学習します。