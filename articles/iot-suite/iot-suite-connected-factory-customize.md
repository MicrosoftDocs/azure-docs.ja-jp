---
title: "Azure IoT Suite コネクテッド ファクトリのカスタマイズ | Microsoft Docs"
description: "コネクテッド ファクトリ事前構成済みソリューションの動作をカスタマイズする方法の説明です。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: c#
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 90a6172dbd887ecda5a9f5d9082a4e136092bc10
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>コネクテッド ファクトリ ソリューションによる OPC UA サーバー データの表示方法をカスタマイズする

## <a name="introduction"></a>はじめに

コネクテッド ファクトリ ソリューションは、自身に接続されている OPC UA サーバーのデータを集計して表示します。 このソリューションでは OPC UA サーバーを参照し、コマンドを送信することができます。 OPC UA の詳細については、[コネクテッド ファクトリの FAQ](iot-suite-faq-cf.md) に関するページをご覧ください。

ソリューションの集計データの例には、ダッシュボードで確認できるファクトリ レベル、ライン レベル、およびステーション レベルの総合設備効率 (OEE) と主要業績評価指標 (KPI) が含まれます。 次のスクリーンショットは、**ミュンヘン** ファクトリの**生産ライン 1** にある**アセンブリ** ステーションの OEE 値と KPI 値を示しています。

![ソリューションの OEE 値と KPI 値の例][img-oee-kpi]

このソリューションを使用すると、"*ステーション*" と呼ばれる OPC UA サーバーの特定のデータ項目に関する詳細情報を表示できます。 次のスクリーンショットは、特定のステーションの製造アイテム数のプロットを示しています。

![製造アイテム数のプロット][img-manufactured-items]

グラフのいずれかをクリックすると、Time Series Insights (TSI) を使用して、データをさらに詳しく確認できます。

![Time Series Insights を使用したデータ詳細の確認][img-tsi]

この記事では、次の内容について説明します。

- ソリューションのさまざまなビューでデータを使用できるようにする方法。
- ソリューションによるデータの表示方法をカスタマイズする方法。

## <a name="data-sources"></a>データ ソース

コネクテッド ファクトリ ソリューションは、自身に接続されている OPC UA サーバーのデータを表示します。 既定のインストールには、ファクトリ シミュレーションを実行する複数の OPC UA サーバーが含まれています。 ソリューションに[ゲートウェイ経由で接続][lnk-connect-cf]する独自の OPC UA サーバーを追加できます。

ダッシュボードでは、接続された OPC UA サーバーからソリューションに送信されるデータ項目を参照できます。

1. **[Select an OPC UA server (OPC UA サーバーの選択)]** ビューに移動します。

    ![[Select an OPC UA server (OPC UA サーバーの選択)] ビュー][img-select-server]

1. サーバーを選択し、**[接続]** をクリックします。 セキュリティの警告が表示されたら、**[続行]** をクリックします。

    > [!NOTE]
    > この警告はサーバーごとに 1 回だけ表示され、ソリューション ダッシュボードとサーバーの間の信頼関係を確立します。

1. これで、サーバーがソリューションに送信できるデータ項目を参照できます。 ソリューションに送信中の項目には緑色のチェック マークが付いています。

    ![公開されている項目][img-published]

1. ソリューションの "*管理者*" は、データ項目を公開して、コネクテッド ファクトリ ソリューションで使用できるようにすることを選択できます。 また、データ項目の値を変更したり、OPC UA サーバーでメソッドを呼び出したりすることもできます。

## <a name="map-the-data"></a>データのマップ

コネクテッド ファクトリ ソリューションでは、OPC UA サーバーの公開データ項目をソリューションのさまざまなビューにマップしたり、ビューで集計したりできます。 コネクテッド ファクトリ ソリューションは、ソリューションのプロビジョニング時に Azure アカウントにデプロイされます。 Visual Studio コネクテッド ファクトリ ソリューションの JSON ファイルには、このマッピング情報が格納されます。 この JSON 構成ファイルは、Visual Studio コネクテッド ファクトリ ソリューションで表示および変更できます。 ソリューションは、変更後に再デプロイできます。

構成ファイルを使用すると、次の操作を行うことができます。

- シミュレートされた既存のファクトリ、生産ライン、ステーションを編集する。
- ソリューションに接続する実際の OPC UA サーバーからデータをマップする。

Visual Studio コネクテッド ファクトリ ソリューションのコピーを複製するには、次の git コマンドを使用します。

`git clone https://github.com/Azure/azure-iot-connected-factory.git`

**ContosoTopologyDescription.json** ファイルにより、コネクテッド ファクトリ ソリューション ダッシュボードのビューへの OPC UA サーバーのデータ項目のマッピングが定義されます。 この構成ファイルは、Visual Studio ソリューションの **WebApp** プロジェクトの**Contoso\Topology** フォルダーにあります。

JSON ファイルの内容は、ファクトリ ノード、生産ライン ノード、ステーション ノードの階層として構成されています。 この階層により、コネクテッド ファクトリ ダッシュボードのナビゲーション階層が定義されます。 ダッシュボードに表示される情報は、この階層の各ノードの値によって決まります。 たとえば、JSON ファイルには、次のようなミュンヘン ファクトリの値が含まれます。

```json
"Guid": "73B534AE-7C7E-4877-B826-F1C0EA339F65",
"Name": "Munich",
"Description": "Braking system",
"Location": {
    "City": "Munich",
    "Country": "Germany",
    "Latitude": 48.13641,
    "Longitude": 11.57754
},
"Image": "munich.jpg"
```

名前、説明、および場所は、ダッシュボードの次のビューに表示されます。

![ダッシュボードのミュンヘンのデータ][img-munich]

各ファクトリ、生産ライン、およびステーションには、イメージ プロパティが指定されています。 これらの JPEG ファイルは、**WebApp** プロジェクトの **Content\img** フォルダーにあります。 こうしたイメージ ファイルは、コネクテッド ファクトリ ダッシュボードに表示されます。

各ステーションには、OPC UA データ項目からのマッピングを定義する複数の詳細なプロパティが含まれています。 以降のセクションでは、こうしたプロパティについて説明します。

### <a name="opcuri"></a>OpcUri

**OpcUri** 値は、OPC UA サーバーを一意に識別する OPC UA アプリケーション URI です。 たとえば、ミュンヘンにある生産ライン 1 のアセンブリ ステーションの **OpcUri** 値は **urn:scada2194:ua:munich:productionline0:assemblystation** のようになります。

ソリューション ダッシュボードでは、接続されている OPC UA サーバーの URI を確認できます。

![OPC UA サーバーの URI の表示][img-server-uris]

### <a name="simulation"></a>シミュレーション

**シミュレーション** ノードの情報は、既定でプロビジョニングされている OPC UA サーバーで実行中の OPC UA シミュレーションに固有です。 これは、実際の OPC UA サーバーでは使用されません。

### <a name="kpi1-and-kpi2"></a>Kpi1 と Kpi2

このノードでは、ステーションのデータが、ダッシュボードの 2 つの KPI 値にどのように影響を与えているかを説明します。 既定のデプロイでは、この KPI 値はそれぞれ 1 時間あたりの出荷単位と 1 時間あたりの kWh を示します。 ソリューションでは、KPI 値をステーション レベルで計算し、生産ライン レベルおよびファクトリ レベルで集計します。

各 KPI には最小値、最大値、および目標値があります。 また、KPI 値ごとに、コネクテッド ファクトリ ソリューションで実行するアラート アクションを定義することもできます。 次のスニペットは、ミュンヘンにある生産ライン 1 のアセンブリ ステーションの KPI 定義を示しています。

```json
"Kpi1": {
  "Minimum": 150,
  "Target": 300,
  "Maximum": 600
},
"Kpi2": {
  "Minimum": 50,
  "Target": 100,
  "Maximum": 200,
  "MinimumAlertActions": [
    {
      "Type": "None"
    }
  ]
}
```

次のスクリーンショットは、ダッシュボードの KPI データを示しています。

![ダッシュボードの KPI 情報][lnk-kpi]

### <a name="opcnodes"></a>OpcNodes

**OpcNodes** ノードは、OPC UA サーバーからの公開データ項目を識別し、そのデータの処理方法を指定します。

**NodeId** 値は、OPC UA サーバーからの特定の OPC UA NodeID を識別します。 ミュンヘンにある生産ライン 1 のアセンブリ ステーションの最初のノードには、**ns=2;i=385** という値が指定されています。 **NodeId** 値は、OPC UA サーバーから読み取るデータ項目を指定し、**SymbolicName** は、ダッシュボードでそのデータに対して使用するわかりやすい名前を提供します。

各ノードに関連付けられているその他の値を次の表に示します。

| 値 | Description |
| ----- | ----------- |
| 関連性  | このデータが影響を与える KPI 値と OEE 値。 |
| OpCode     | データの集計方法。 |
| Units      | ダッシュボードで使用する単位。  |
| Visible    | この値をダッシュボードに表示するかどうか。 一部の値は計算には使用されても、表示はされません。  |
| 最大値    | ダッシュボードでアラートをトリガーする最大値。 |
| MaximumAlertActions | アラートに対して実行するアクション。 たとえば、ステーションにコマンドを送信します。 |
| ConstValue | 計算で使用される定数値。 |

## <a name="deploy-the-changes"></a>変更のデプロイ

**ContosoTopologyDescription.json** ファイルに対する変更が完了したら、コネクテッド ファクトリ ソリューションを Azure アカウントに再デプロイする必要があります。

**azure-iot-connected-factory** リポジトリには、ソリューションの再構築とデプロイに使用できる **build.ps1** PowerShell スクリプトが含まれています。

## <a name="next-steps"></a>次のステップ

コネクテッド ファクトリ事前構成済みソリューションの詳細については、次の記事を参照してください。

* [コネクテッド ファクトリ事前構成済みソリューションのチュートリアル][lnk-rm-walkthrough]
* [コネクテッド ファクトリ用のゲートウェイをデプロイする][lnk-connect-cf]
* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]
* [コネクテッド ファクトリに関する FAQ](iot-suite-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-suite-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-suite-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-suite-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-suite-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-suite-connected-factory-customize/published.png
[img-munich]: ./media/iot-suite-connected-factory-customize/munich.png
[img-server-uris]: ./media/iot-suite-connected-factory-customize/serveruris.png
[lnk-kpi]: ./media/iot-suite-connected-factory-customize/kpidisplay.png

[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
