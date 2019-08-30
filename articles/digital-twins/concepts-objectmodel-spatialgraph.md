---
title: Digital Twins のオブジェクト モデルと空間インテリジェンス グラフを理解する | Microsoft Docs
description: Azure Digital Twins を使用して、人、場所、デバイスの間の関係をモデル化します
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: 0bb7309ae452b2885be279d1ab38b8467397f2f8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638495"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Digital Twins のオブジェクト モデルと空間インテリジェンス グラフを理解する

Azure Digital Twins は Azure IoT サービスであり、物理環境および関連するデバイス、センサー、ユーザーの包括的な仮想表現を強化します。 ドメイン固有の概念が適切なモデルに体系化されて、開発の向上につながります。 モデルはさらに、空間インテリジェンス グラフ内に配置されます。 このような概念により、人、スペース、デバイスの間の関係と相互作用が忠実にモデル化されます。

Digital Twins のオブジェクト モデルは、ドメイン固有の概念、カテゴリ、プロパティを表します。 モデルは、特定のニーズに合わせてソリューションを調整したいと考えるユーザーによって予め定義されます。 同時に、予め定義されるこれらの Digital Twins のオブジェクト モデルによって "_オントロジー_" が構成されます。 スマート ビルディングのオントロジーでは、地域、会場、フロア、オフィス、ゾーン、会議室、フォーカス ルームが記述されています。 エネルギー グリッドのオントロジーでは、さまざまな発電所、変電所、エネルギー資源、顧客が記述されています。 Digital Twins のオブジェクト モデルとオントロジーを使用すると、多様なシナリオやニーズをカスタマイズすることができます。

Digital Twins のオブジェクト モデルとオントロジーが整えば、"_空間グラフ_" を設定できます。 空間グラフは、IoT ソリューションに関連するスペース、デバイス、人の間における多数の関係の仮想表現です。 この図は、スマート ビルディングのオントロジーが使用された空間グラフの例です。

![Digital Twins 空間グラフの構築][1]

<a id="model"></a>

空間グラフには、スペース、デバイス、センサー、ユーザーがまとめられています。 それぞれは、現実世界をモデル化する方法でリンクされています。 このサンプルでは、会場 43 には 4 つのフロアがあり、それぞれに多数の異なる領域があります。 ユーザーは各自のワークステーションに関連付けられていて、グラフの一部にアクセスできます。 管理者には空間グラフを変更する権限があるのに対し、訪問者にはビルのデータの一部しか見る権限がありません。

## <a name="digital-twins-object-models"></a>Digital Twins のオブジェクト モデル

Digital Twins オブジェクト モデルでは、オブジェクトの以下の主要カテゴリがサポートされています。

- **スペース**は仮想的または物理的な場所です (例: `Tenant`、`Customer`、`Region`、`Venue`)。
- **デバイス**は仮想的または物理的な機器です (例: `AwesomeCompany Device`、`Raspberry Pi 3`)。
- **センサー**はイベントを検出するオブジェクトです (例: `AwesomeCompany Temperature Sensor`、`AwesomeCompany Presence Sensor`)。
- **ユーザー**は占有者とその特性を示します。

オブジェクトには他に次のようなカテゴリがあります。

- **リソース**はスペースに関連付けられており、通常は空間グラフ内のオブジェクトによって使用される Azure リソースを表します (例: `IoTHub`)。
- **BLOB** はオブジェクト (スペース、デバイス、センサー、ユーザーなど) に関連付けられます。 これらは、MIME の種類とメタデータを備えたファイルとして使用されます (例: `maps`、`pictures`、`manuals`)。
- **拡張型**は、特定の特性を備えたエンティティを拡張する拡張可能な列挙です (例: `SpaceType`、`SpaceSubtype`)。
- **オントロジー**は、一連の拡張型を表します (例: `Default`、`Building`、`BACnet`、`EnergyGrid`)。
- **プロパティのキーと値**は、スペース、デバイス、センサー、ユーザーのカスタムの特性です。 組み込みの特性と一緒に使用できます (例: キーとして `DeltaProcessingRefreshTime`、値として `10`)。
- **ロール**は、空間グラフ内のユーザーやデバイスに割り当てられたアクセス許可のセットです (例: `Space Administrator`、`User Administrator`、`Device Administrator`)。
- **ロールの割り当て**は、空間グラフ内のロールとオブジェクトの間の関連付けです。 たとえば、空間グラフ内のスペースを管理するアクセス許可をユーザーまたはサービス プリンシパルに付与することができます。
- **セキュリティ キー ストア**は、特定のスペース オブジェクトの下にある階層内のすべてのデバイスにセキュリティ キーを提供し、デバイスが Digital Twins と安全に通信できるようにします。
- **ユーザー定義関数** (UDF) は、空間グラフ内でセンサーのテレメトリ処理をカスタマイズできるようにします。 たとえば、UDF を使って次のことを行えます。
  - センサーの値を設定する。
  - センサーの読み取り値に基づいてカスタム ロジックを実行し、スペースへの出力を設定する。
  - スペースにメタデータを関連付ける。
  - 予め定義されている条件が満たされたときに通知を送る。 現在は、JavaScript で UDF を記述できます。
- **マッチャー**は、特定のテレメトリ メッセージに対して実行される UDF を決定するオブジェクトです。
- **エンドポイント**は、テレメトリ メッセージと Digital Twins のイベントをルーティングできる場所です (例: `Event Hub`、`Service Bus`、`Event Grid`)。

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>空間インテリジェンス グラフ

空間グラフは、Digital Twins のオブジェクト モデルで定義されているスペース、デバイス、人の階層グラフです。 空間グラフでは、継承、フィルター、走査、スケーラビリティ、拡張性がサポートされています。 お客様の空間グラフは、REST API のコレクションを使用して管理および操作できます。

ご利用のサブスクリプションに Digital Twins サービスをデプロイすると、お客様はルート ノードのグローバル管理者になります。 そして、構造全体へのフル アクセスが自動的に許可されます。 グラフへのスペースのプロビジョニングには、Space API を使用します。 サービスは Device API を、センサーは Sensor API を使用してプロビジョニングします。 グラフを一括してプロビジョニングするための[オープン ソース ツール](https://github.com/Azure-Samples/digital-twins-samples-csharp)も用意されています。

**グラフの継承**。 継承はアクセス許可とプロパティに適用され、親ノードからその下にあるすべてのノードに伝えられます。 たとえば、特定のノード上のユーザーにロールが割り当てられると、ユーザーはその特定のノードとその下にあるすべてのノードに対してそのロールのアクセス許可を持つようになります。 特定のノードに対して定義されている各プロパティ キーと拡張型は、そのノードの下にあるすべてのノードによって継承されます。

**グラフのフィルター**。 要求の結果を絞り込むには、フィルターを使用します。 フィルターには、ID、名前、タイプ、サブタイプ、親のスペース、関連付けられているスペースを使用できます。 また、センサーのデータ型、プロパティのキーと値、*traverse*、*minLevel*、*maxLevel* など、各種の OData フィルター パラメーターを使用することもできます。

**グラフの走査**。 空間グラフ内を深さと幅の方向に走査することができます。 深さの場合は、パラメーター *traverse*、*minLevel*、*maxLevel* を使用して、トップダウンまたはボトムアップでグラフ内を走査できます。 幅の場合は、グラフ内を走査して、親スペースに直接関連付けられている兄弟ノードまたはその子孫のいずれかを取得できます。 オブジェクトのクエリでは、GET API の *includes* パラメーターを使用して、そのオブジェクトへの関係を備えたすべての関連オブジェクトを取得できます。

**グラフのスケーラビリティ**。 Digital Twins ではグラフのスケーラビリティが保証されているので、現実のワークロードを処理できます。 Digital Twins を使用して、不動産、インフラストラクチャ、デバイス、センサー、テレメトリなどの大規模なポートフォリオを表すことができます。

**グラフの拡張性**。 拡張性を利用して、基になっている Digital Twins のオブジェクト モデルを、新しい型とオントロジーでカスタマイズできます。 お客様の Digital Twins のデータを、拡張可能なプロパティと値で拡充することもできます。

### <a name="spatial-intelligence-graph-management-apis"></a>空間インテリジェンス グラフの Management API

[Azure portal](https://portal.azure.com) から Azure Digital Twins をデプロイすると、Management API シリーズの [Swagger](https://swagger.io/tools/swagger-ui/) URL が自動的に生成されます。 これは、Azure portal の **[概要]** セクションに次の形式で表示されます。

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| 名前 | 置換後の文字列 |
| --- | --- |
| YOUR_INSTANCE_NAME | お客様の Digital Twins インスタンスの名前 |
| YOUR_LOCATION | インスタンスをホストするサーバーのリージョン |

 この画像には、完全な URL 形式が表示されています。

![Digital Twins ポータル Management API][2]

空間インテリジェンス グラフの使用方法について詳しくは、Azure Digital Twins Management API シリーズのプレビューを参照してください。

> [!TIP]
> API の機能を見ることができる Swagger のプレビューが提供されています。
> [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger) でホストされています。

[Swagger の使用方法](how-to-use-swagger.md)の詳細を学習してください。

すべての API 呼び出しでは、[OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code) を使用して認証を行う必要があります。 API は、[Microsoft REST API ガイドライン規則](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)に従います。 コレクションを返すほとんどの API は、[OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) のシステム クエリ オプションをサポートしています。

## <a name="next-steps"></a>次の手順

- デバイスの接続と、Digital Twins にテレメトリ メッセージを送信する方法については、[Azure Digital Twins のデバイスの接続性とテレメトリの受信](concepts-device-ingress.md)に関するページを参照してください。

- Management API の制限と調整については、[Azure Digital Twins API の管理と制限](concepts-service-limits.md)に関するページをご覧ください。

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
