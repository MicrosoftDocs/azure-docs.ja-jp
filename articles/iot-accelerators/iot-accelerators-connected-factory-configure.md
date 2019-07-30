---
title: コネクテッド ファクトリ トポロジを構成する - Azure | Microsoft Docs
description: コネクテッド ファクトリ ソリューション アクセラレータのトポロジの構成方法。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 3ddbf5832424cdafad2c29254f51754203c7f079
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428317"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>コネクテッド ファクトリ ソリューション アクセラレータの構成

コネクテッド ファクトリ ソリューション アクセラレータは、Contoso という架空の会社向けにシミュレートされたダッシュボードを示しています。 この会社は、世界中のさまざまな場所に工場を持っています。

この記事では例として Contoso を使用して、コネクテッド ファクトリ ソリューションのトポロジの構成方法を説明します。

## <a name="simulated-factories-configuration"></a>シミュレートされた工場の構成

Contoso の各工場には、それぞれ 3 つのステーションで構成される生産ラインがあります。 各ステーションは実際の OPC UA サーバーであり、次の特定の役割が与えられています。

* アセンブリ ステーション
* テスト ステーション
* パッケージ化ステーション

これらの OPC UA サーバーは OPC UA ノードを持ち、[OPC Publisher](overview-opc-publisher.md) がこれらのノードの値をコネクテッド ファクトリに送信します。 次のトピックがあります。

* 現在の電力消費量など、現在の動作状態。
* 生産された製品数など、生産情報。

ダッシュボードを使用して、グローバル ビューからステーション レベル ビューまで Contoso ファクトリ トポロジを掘り下げて確認できます。 コネクテッド ファクトリ ダッシュボードで次のことが可能です。

* トポロジの各レイヤーの OEE と KPI の数値を視覚化。
* ステーションの OPC UA ノードの現在の値を視覚化。
* ステーション レベルからグローバル レベルまでの OEE と KPI の数値を集計。
* 値が特定のしきい値に達した場合に実行するアラートとアクションを視覚化。

## <a name="connected-factory-topology"></a>コネクテッド ファクトリ トポロジ

工場、生産ライン、ステーションのトポロジは階層的です。

* グローバル レベルは、子として工場ノードを持ちます。
* 工場は、子として生産ライン ノードを持ちます。
* 生産ラインは、子としてステーション ノードを持ちます。
* ステーション (OPC UA サーバー) は、子として OPC UA ノードを持ちます。

トポロジ内の各ノードは、以下を定義する共通のプロパティ セットを持ちます。

* そのトポロジ ノードの一意識別子。
* 名前。
* 説明。
* 画像。
* そのトポロジ ノードの子。
* OEE および KPI の数値と実行するアラート アクションの最小値、目標値、最大値。

## <a name="topology-configuration-file"></a>トポロジ構成ファイル

前のセクションの一覧に示したプロパティを構成するために、コネクテッド ファクトリ ソリューションは [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json) という名前の構成ファイルを使用します。

このファイルは、`WebApp/Contoso/Topology` フォルダーのソリューション ソース コード内で見つけることができます。

次のスニペットは、`ContosoTopologyDescription.json` 構成ファイルの概要を示しています。

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

 `<global_configuration>`、`<factory_configuration>`、`<production_line_configuration>`、および `<station_configuration>` の共通プロパティは次のとおりです。

* **Name** (string 型)

  わかりやすい名前を定義します。そのトポロジ ノードを表す 1 単語のみにして、ダッシュボードに表示されるようにします。

* **Description** (string 型)

  トポロジ ノードを詳細に説明します。

* **Image** (string 型)

  トポロジ ノードに関する情報がダッシュボード上に表示されるときに表示される、WebApp ソリューション内の画像へのパスです。

* **OeeOverall**、**OeePerformance**、**OeeAvailability**、**OeeQuality**、**Kpi1**、**Kpi2** (`<performance_definition>` 型)

  これらのプロパティは、アラートを生成するために使用する操作可能な数値の最小値、目標値、最大値を定義します。 また、アラートが検出された場合に実行するアクションもこれらのプロパティで定義します。

`<factory_configuration>` と `<production_line_configuration>` は次のプロパティを持ちます。

* **Guid** (string 型)

  トポロジ ノードを一意に識別します。

`<factory_configuration>` は次のプロパティを持ちます。

* **Location** (`<location_definition>` 型)

  工場がある場所を指定します。

`<station_configuration>` は次のプロパティを持ちます。

* **OpcUri** (string 型)

  このプロパティでは、OPC UA サーバーの OPC UA アプリケーション URI を設定する必要があります。
  これは OPC UA の仕様によりグローバルに一意である必要があるため、このプロパティはステーション トポロジ ノードを識別するために使用されます。

* **OpcNodes**、これは OPC UA ノードの配列です (`<opc_node_description>` 型)

`<location_definition>` は次のプロパティを持ちます。

* **City** (string 型)

  所在地に最も近い都市の名前

* **Country** (string 型)

  所在地の国

* **Latitude** (double 型)

  所在地の緯度

* **Longitude** (double 型)

  所在地の経度

`<performance_definition>` は次のプロパティを持ちます。

* **Minimum** (double 型)

  下限のしきい値です。 現在の値がこのしきい値より低い場合に、アラートが生成されます。

* **Target** (double 型)

  理想の目標値です。

* **Maximum** (double 型)

  上限のしきい値です。 現在の値がこのしきい値より高い場合に、アラートが生成されます。

* **MinimumAlertActions** (`<alert_action>` 型)

  下限アラートに対する応答として取り得るアクションのセットを定義します。

* **MaximumAlertActions** (`<alert_action>` 型)

  上限アラートに対する応答として取り得るアクションのセットを定義します。

`<alert_action`> は次のプロパティを持ちます。

* **Type** (string 型)

  アラート アクションの種類です。 次の種類が知られています。

  * **AcknowledgeAlert**: アラートの状態は承認済みに変更されます。
  * **CloseAlert**: 同じ種類の古いアラートはすべて、ダッシュボードに表示されないようになります。
  * **CallOpcMethod**: OPC UA メソッドが呼び出されます。
  * **OpenWebPage**: ブラウザー ウィンドウが開かれ他のコンテキスト情報が表示されます。

* **Description** (string 型)

  ダッシュボードに表示されるアクションの説明です。

* **Parameter** (string 型)

  アクションの実行に必要なパラメーターです。 この値はアクションの種類によって異なります。

  * **AcknowledgeAlert**: パラメーター不要。
  * **CloseAlert**: パラメーター不要。
  * **CallOpcMethod**: "親ノードの NodeId, 呼び出すメソッドの NodeId, OPC UA サーバーの URI" という形式で呼び出す、OPC UA メソッドのノード情報とパラメーター。
  * **OpenWebPage**: ブラウザー ウィンドウに表示する URL。

`<opc_node_description>` には、ステーション (OPC UA サーバー) 内の OPC UA ノードに関する情報が含まれています。 現存しないものの、コネクテッド ファクトリの計算ロジックにおいてストレージとして使用される OPC UA ノードを表すノードも有効です。 これは次のプロパティを持ちます。

* **NodeId** (string 型)

  ステーションの (OPC UA サーバーの) アドレス スペース内にある OPC UA ノードのアドレスです。 構文は NodeId の OPC UA の仕様で指定されているとおりにする必要があります。

* **SymbolicName** (string 型)

  この OPC UA ノードの値が表示される時にダッシュボードに表示される名前です。

* **Relevance** (string 型の配列)

  OPC UA ノードの値が関連する OEE または KPI の計算を示します。 各配列要素は、次のいずれかの値になります。

  * **OeeAvailability_Running**: この値は OEE 可用性の計算に関連します。
  * **OeeAvailability_Fault**: この値は OEE 可用性の計算に関連します。
  * **OeePerformance_Ideal**: この値は OEE パフォーマンスの計算に関連し、通常は定数値です。
  * **OeePerformance_Actual**: この値は OEE パフォーマンスの計算に関連します。
  * **OeeQuality_Good**: この値は OEE 品質の計算に関連します。
  * **OeeQuality_Bad**: この値は OEE 品質の計算に関連します。
  * **Kpi1**: この値は KPI1 の計算に関連します。
  * **Kpi2**: この値は KPI2 の計算に関連します。

* **OpCode** (string 型)

  Time Series Insight クエリと OEE/KPI 計算における OPC UA ノードの値の処理方法を示します。 各 Time Series Insight クエリは特定の期間を対象とします。これはクエリのパラメーターであり、結果を提供します。 OpCode は、結果の計算方法を制御し、次のいずれかの値になります。

  * **Diff**: その期間の最後の値と最初の値の差。
  * **Avg**: その期間のすべての値の平均。
  * **Sum**: その期間のすべての値の合計。
  * **Last**: 現在は使用されません。
  * **Count**: その期間の値の数。
  * **Max**: その期間の最大値。
  * **Min**: その期間の最小値。
  * **Const**: 結果は ConstValue プロパティで指定された値になります。
  * **SubMaxMin**: 最大値と最小値の差。
  * **Timespan**: その期間。

* **Units** (string 型)

  ダッシュボードに表示される値の単位を定義します。

* **Visible** (boolean 型)

  値をダッシュボードに表示するかどうかを制御します。

* **ConstValue** (double 型)

  **OpCode** が **Const** の場合、このプロパティはそのノードの値になります。

* **Minimum** (double 型)

  現在の値がこの値を下回った場合は、下限アラートが生成されます。

* **Maximum** (double 型)

  現在の値がこの値を上回った場合は、上限アラートが生成されます。

* **MinimumAlertActions** (`<alert_action>` 型)

  下限アラートに対する応答として取り得るアクションのセットを定義します。

* **MaximumAlertActions** (`<alert_action>` 型)

  上限アラートに対する応答として取り得るアクションのセットを定義します。

ステーション レベルで、**Simulation** オブジェクトも確認できます。 これらのオブジェクトは、コネクテッド ファクトリ シミュレーションを構成するためにのみ使用し、実際のトポロジを構成するためには使用すべきではありません。

## <a name="how-the-configuration-data-is-used-at-runtime"></a>実行時の構成データの使用方法

構成ファイル内で使用されるすべてのプロパティは、その使用方法に応じてさまざまなカテゴリにグループ化できます。 これらのカテゴリは次のとおりです。

### <a name="visual-appearance"></a>視覚的な外観

このカテゴリのプロパティは、コネクテッド ファクトリ ダッシュボードの視覚的な外観を定義します。 たとえば、次のようになります。

* Name
* 説明
* Image
* Location
* Units
* Visible

### <a name="internal-topology-tree-addressing"></a>内部のトポロジ ツリーのアドレス指定

WebApp は、すべてのトポロジ ノードの情報を含む内部データ ディクショナリを保持しています。 **Guid** プロパティと **OpcUri** プロパティは、このディクショナリにアクセスするためのキーとして使用され、一意である必要があります。

### <a name="oeekpi-computation"></a>OEE/KPI 計算

コネクテッド ファクトリ シミュレーションの OEE/KPI の数値は、次のものによってパラメーター化されます。

* 計算に含まれる OPC UA ノードの値。
* テレメトリの値からの数値の計算方法。

コネクテッド ファクトリは、[http://www.oeefoundation.org](http://www.oeefoundation.org) が発行した OEE 数式を使用します。

ステーションの OPC UA ノード オブジェクトは、OEE/KPI 計算で使用するためのタグ付けを有効にします。 **Relevance** プロパティは、OPC UA ノードの値を使用する必要がある OEE/KPI の数値を示します。 **OpCode** プロパティは、値を計算に含める方法を定義します。

### <a name="alert-handling"></a>アラートの処理

コネクテッド ファクトリは、下限/上限しきい値に基づく単純なアラート生成メカニズムをサポートしています。 これらのアラートへの応答として構成できる定義済みアクションが数多く用意されています。 次のプロパティは、このメカニズムを制御します。

* 最大値
* 最小値
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>テレメトリ データの関連付け

最新の値の視覚化や Time Series Insight クエリの作成などの特定の操作では、WebApp は取り込まれたテレメトリ データのアドレス指定スキームを必要とします。 また、コネクテッド ファクトリに送信されるテレメトリは、内部データ構造に格納される必要があります。 これらの操作を有効化する次の 2 つのプロパティは、ステーション (OPC UA サーバー) と OPC UA ノード レベルにあります。

* **OpcUri**

  テレメトリの送信元である OPC UA サーバーを (グローバル、かつ一意に) 識別します。 取り込まれるメッセージでは、このプロパティは **ApplicationUri** として送信されます。

* **NodeId**

  OPC UA サーバー内のノードの値を識別します。 プロパティの形式は、OPC UA 仕様で指定されているとおりにする必要があります。 取り込まれるメッセージでは、このプロパティは **NodeId** として送信されます。

テレメトリ データをコネクテッド ファクトリに取り込む方法の詳細については、「[OPC Publisher とは](overview-opc-publisher.md)」を参照してください。

## <a name="example-how-kpi1-is-calculated"></a>例:KPI1 の計算方法

`ContosoTopologyDescription.json` ファイル内の構成により、OEE/KPI の数値の計算方法を制御します。 次の例では、このファイル内のプロパティが KPI1 の計算を制御する方法を示します。

コネクテッド ファクトリでは、KPI1 は直近の 1 時間で正常に製造された製品の数を測定するために使用します。 コネクテッド ファクトリ シミュレーション内の各ステーション (OPC UA サーバー) は、この KPI を計算するためのテレメトリを提供する OPC UA ノード (`NodeId: "ns=2;i=385"`) を提供します。

この OPC UA ノードの構成は、次のスニペットのようになっています。

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

この構成により、Time Series Insights を使用してこのノードのテレメトリの値に対してクエリを実行できるようになります。 Time Series Insights クエリは次のものを取得します。

* 値の数。
* 最小値。
* 最大値。
* すべての値の平均。
* 指定された期間における、すべての一意の **OpcUri** (**ApplicationUri**)、**NodeId** ペアの、すべての値の合計。

**NumberOfManufactureredProducts** ノードの値の 1 つの特徴は、それが増加するのみであるということです。 その期間に製造された製品の数を計算するために、コネクテッド ファクトリは **OpCode** **SubMaxMin** を使用します。 この計算では、その期間の開始時に最小値を、その期間の終了時に最大値を取得します。

構成内の **OpCode** によって計算ロジックが構成され、最大値と最小値の差の結果が計算されます。 これらの結果は、ボトムアップでルート (グローバル) レベルまで蓄積され、ダッシュボードに表示されます。

## <a name="next-steps"></a>次の手順

次は、[コネクテッド ファクトリ ソリューションをカスタマイズする](iot-accelerators-connected-factory-customize.md)方法について学習することをお勧めします。
