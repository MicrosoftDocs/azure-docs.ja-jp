---
title: Azure IoT Central データを Power BI ダッシュボードに視覚化する |Microsoft Docs
description: Azure IoT Central 用の Power BI ソリューション、IoT Central データを視覚化して分析します。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/15/2019
ms.topic: conceptual
ms.openlocfilehash: 322be1e13662d92a3cb0a805a9ccaacd05928f7d
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328097"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Azure IoT Central データを Power BI ダッシュボードに視覚化する

*このトピックでは、管理者に適用されます。*

![Power BI ソリューションのパイプライン](media/howto-connect-powerbi/iot-continuous-data-export.png)

Azure IoT Central 用の Power BI ソリューションを使用して、IoT デバイスのパフォーマンスを監視するための強力な Power BI ダッシュボードを作成します。 Power BI ダッシュ ボードでは、以下を実行できます。
- デバイスが特定の時間に送信しているデータの量を追跡する
- テレメトリ、状態、およびイベント間でデータ ボリュームを比較する
- 多数の測定値を報告しているデバイスを識別する
- デバイスの測定値の過去の傾向を観察する
- 多数の重要なイベントを送信している、問題のあるデバイスを識別する

このソリューションは、[データの連続エクスポート](howto-export-data.md)から Azure Blob Storage アカウントにデータを取り込むパイプラインを設定します。 このデータが Azure Functions、Azure Data Factory、Azure SQL Database を通って、データの処理と変換が行われます。 その出力は、PBIX ファイルとしてダウンロード可能な Power BI レポートとして視覚化し、分析することができます。 これらのリソースは、すべてが Azure サブスクリプションで作成されるため、各コンポーネントをニーズに合わせてカスタマイズできます。

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Microsoft AppSource から、[Azure IoT Central 用の Power BI ソリューション](https://aka.ms/iotcentralpowerbisolutiontemplate)を取得します。

## <a name="prerequisites"></a>前提条件
このソリューションを設定するには、以下が必要です。
- Azure サブスクリプションへのアクセス。
- [継続的データ エクスポート](howto-export-data.md)を使用して IoT Central アプリからエクスポートされるデータ。 Power BI ダッシュボードを最大限に活用するには、測定値、デバイス、およびデバイス テンプレート ストリームを有効にすることをお勧めします。
- Power BI Desktop (最新バージョン)
- Power BI Pro (ダッシュボードを他のユーザーと共有する場合)

## <a name="reports"></a>Reports

2 つのレポートが自動的に生成されます。 

最初のレポートは、デバイスによって報告された測定値の履歴を示し、測定値の種類別と、最も高い測定値を送信しているデバイス別に分類します。

![Power BI レポート (1 ページ目)](media/howto-connect-powerbi/template-page1-hasdata.PNG)

2 つ目のレポートは、イベントに深く踏み込み、報告されたエラーと警告の履歴ビューを示します。 報告されているイベントの合計数が最も多いデバイスと、具体的なエラー イベントと警告イベントの数も示されます。

![Power BI レポート (2 ページ目)](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="architecture"></a>アーキテクチャ
作成されたすべてのリソースには、Azure portal でアクセスできます。 すべて、1 つのリソース グループの下にあります。

![リソース グループの Azure Portal ビュー](media/howto-connect-powerbi/azure-deployment.PNG)

各リソースの詳細とその使用方法を、以下に説明します。

### <a name="azure-functions"></a>Azure Functions
Azure Function アプリは、新しいファイルが BLOB ストレージに書き込まれるたびにトリガーされます。 関数により、各測定値、デバイス、およびデバイス テンプレート ファイル内のフィールドが抽出され、Azure Data Factory によって使用されるいくつかの中間 SQL テーブルが入力されます。

### <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory は、リンクされたサービスとして SQL データベースに接続します。 これは、データを処理して分析テーブルに格納するストアド プロシージャ アクティビティを実行します。

### <a name="azure-sql-database"></a>Azure SQL Database
これらのテーブルは、既定のレポートを入力するために自動的に作成されます。 Power BI でこれらのスキーマを調べて、このデータで独自の視覚エフェクトを作成できます。

| テーブル名 |
|------------|
|[analytics].[Measurements]|
|[analytics].[Messages]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[MeasurementDefinitions]|
|[analytics].[Devices]|
|[analytics].[DeviceTemplates]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>概算コスト

関連する Azure のコスト (Azure Function、Data Factory、Azure SQL) の概算は次のとおりです。 すべての価格は米国ドルです。 価格はリージョンに応じて異なることに注意してください。そのため、実際の価格については、個々のサービスの最新の価格を確認されることをお勧めします。
以下は、テンプレートに用意された既定値です (設定した後、これらの値はすべて変更できます)。

- Azure Functions:App Service プランS1、$74.40/月
- Azure SQL S1、~$30/月

さまざまな価格オプションについて理解することで、ご自身のニーズに合わせて自由に調整できます。

## <a name="resources"></a>リソース

AppSource から、[Azure IoT Central 用の Power BI ソリューション](https://aka.ms/iotcentralpowerbisolutiontemplate)を取得できます。

## <a name="next-steps"></a>次の手順

ここでは、Power BI にデータを視覚化する方法を説明しました。次に以下の記事を読むことをお勧めします。

> [!div class="nextstepaction"]
> [デバイスを管理する方法](howto-manage-devices.md)