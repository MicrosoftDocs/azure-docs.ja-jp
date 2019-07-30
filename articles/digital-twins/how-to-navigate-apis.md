---
title: Azure Digital Twins API の移動 | Microsoft Docs
description: Azure Digital Twins 管理 API にクエリを実行する一般的なパターンについて説明します。
author: kingdomofends
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: v-adgera
ms.openlocfilehash: da1493d2d52f2c8a964df3b72c1622a9c6b66abf
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719855"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Azure Digital Twins 管理 API の使用方法

Azure Digital Twins 管理 API は、IoT アプリのための強力な機能を提供します。 この記事では、API 構造内を移動する方法を示します。  

## <a name="api-summary"></a>API の概要

次の一覧は、Digital Twins API のコンポーネントを示しています。

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces):これらの API は、セットアップでの物理的な場所と対話します。 これらは物理的な場所のデジタル マッピングを[空間グラフ](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)の形式で作成、削除、および管理するのに役立ちます。

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices):これらの API は、セットアップのデバイスと対話します。 これらのデバイスは、1 つまたは複数のセンサーを管理できます。 たとえば、電話機や Raspberry Pi センサー ポッド、あるいは LoRa ゲートウェイなどがデバイスに該当します。

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors):これらの API は、デバイスおよび物理的な場所に関連付けられたセンサーと通信するのに役立ちます。 センサーは周辺環境の値を記録して送信し、この値は空間環境を操作するために後で使用できます。  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources):これらの API は、IoT Hub などのリソースを Digital Twins インスタンス用にセットアップするのに役立ちます。

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types):これらの API を使用すると、Digital Twins オブジェクトに拡張型を関連付けて、それらのオブジェクトに特定の特性を追加することができます。 これらの型によって、UI 内のオブジェクトや、テレメトリ データを処理するカスタム関数のフィルター処理やグループ化を簡単に行うことができます。 拡張型の例として、*DeviceType*、*SensorType*、*SensorDataType*、*SpaceType*、*SpaceSubType*、*SpaceBlobType*、*SpaceResourceType* などがあります。

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies):これらの API は、拡張型のコレクションであるオントロジを管理するのに役立ちます。 オントロジでは、オブジェクトの種類が表す物理的スペースに従ってオブジェクトの種類の名前を指定します。 たとえば、*BACnet* オントロジは *sensor types*、*datatypes*、*datasubtypes*、*dataunittypes* に対して特定の名前を指定します。 オントロジはサービスによって管理および作成されます。 ユーザーはオントロジをロードおよびアンロードできます。 オントロジがロードされると、それに関連付けられているすべての種類の名前が有効になり、空間グラフにプロビジョニングできるようになります。 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys):これらの API を使用して、*スペース*、*デバイス*、*ユーザー*、*センサー*のためのカスタム プロパティを作成できます。 これらのプロパティは、キー/値のペアとして作成されます。 これらのプロパティのデータ型は、*PrimitiveDataType* を設定して定義できます。 たとえば、センサーに対して種類が *uint* の*BasicTemperatureDeltaProcessingRefreshTime* という名前のプロパティを定義し、センサーごとにこのプロパティの値を割り当てることができます。 また、プロパティを作成するときに、これらの値の制約を追加することもでき、たとえば *Min* や *Max* の範囲や、許可される値として *ValidationData* を指定できます。

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers):これらの API を使用すると、受信デバイス データから評価する条件を指定できます。 詳細については、[こちらの記事](concepts-user-defined-functions.md#matchers)をご覧ください。 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions):これらの API を使用すると、セットアップから受け取るデータを処理するために、*マッチャー*によって定義された条件が発生したときに実行されるカスタム関数を作成、削除、または更新できます。 *ユーザー定義関数*とも呼ばれるこれらのカスタム関数については、[こちら記事](concepts-user-defined-functions.md#user-defined-functions)を参照してください。 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints):これらの API を使用すると、Digital Twins ソリューションがデータの保管と分析のために他の Azure サービスと通信できるようにエンドポイントを作成できます。 詳細については、[こちらの記事](concepts-events-routing.md)を参照してください。 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores):これらの API を使用すると、スペースのセキュリティ キー ストアを管理できます。 これらのストアはセキュリティ キーのコレクションを保持できるため、ユーザーは最新の有効なキーを簡単に取得できます。

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users):これらの API を使用すると、必要なときにユーザーを特定するために、これらの個人をスペースに関連付けることができます。 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System):これらの API を使用すると、スペースやセンサーの既定の種類など、システム全体の設定を管理できます。 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments):これらの API を使用すると、エンティティ (ユーザー ID、ユーザー定義関数 ID など) にロールを関連付けることができます。各ロールの割り当てには、関連付けるエンティティの ID、エンティティの種類、関連付けるロールの ID、テナント ID、その関連付けを使用してエンティティがアクセスできるリソースの上限を定義するパスが含まれています。 詳細については、[こちらの記事](security-role-based-access-control.md)を参照してください。


## <a name="api-navigation"></a>API ナビゲーション

Digital Twins API では、以下のパラメーターを使用して、空間グラフ全体でのフィルター処理やおよびナビゲーションをサポートします。

- **spaceId**:API は特定のスペース ID で結果をフィルター処理します。 さらに、ブール型フラグ **useParentSpace** を [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API に適用でき、このフラグは、指定したスペース ID が、現在のスペースではなく親のスペースを表すことを示します。 

- **minLevel** および **maxLevel**:ルート スペースは、レベル 1 であると見なされます。 親のスペースがレベル *n* であるスペースは、レベル *n+1* です。 これらの値を設定して、特定のレベルで結果をフィルター処理できます。 これらの設定値は、その値を含みます。 デバイス、センサー、およびその他のオブジェクトは、それらの最も近いスペースと同じレベルであると見なされます。 特定のレベルのすべてのオブジェクトを取得するには、**minLevel** と **maxLevel** の両方を同じ値に設定します。

- **minRelative** および **maxRelative**:これらのフィルターを指定すると、対応するレベルは、指定したスペース ID のレベルを基準とした相対値となります。
   - 相対レベル *0* は、特定のスペース ID と同じレベルになります。
   - 相対レベル *1* は、特定のスペース ID の子と同じレベルのスペースを表します。 相対レベル *n* は、指定されたスペースよりも *n* レベル低いスペースを表します。
   - 相対レベル *-1* は、指定されたスペースの親のスペースと同じレベルのスペースを表します。

- **traverse**:指定したスペース ID から、次の値で指定するいずれかの方向に走査できます。
   - **なし**:このデフォルト値では、指定したスペース ID でフィルター処理します。
   - **Down**:指定したスペース ID とその子孫でフィルター処理します。 
   - **Up**:指定したスペース ID とその先祖でフィルター処理します。 
   - **Span**:指定したスペース ID と同じレベルで空間グラフの水平方向の部分をフィルター処理します。 **minRelative** または **maxRelative** のいずれかを true に設定する必要があります。 


### <a name="examples"></a>例

次の一覧は、[/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API を使用した移動の例をいくつか示しています。 プレース ホルダー `YOUR_MANAGEMENT_API_URL` は、`https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/` 形式の Digital Twins API の URI を示すことに注意してください。ここで、`YOUR_INSTANCE_NAME` は Azure Digital Twins インスタンスの名前、`YOUR_LOCATION` はインスタンスをホストするリージョンです。

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` は、ルート スペースに接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` は、レベル 2、3、または 4 のスペースに接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` は、mySpaceId に直接接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` は、mySpaceId またはその子孫の 1 つに接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` は、mySpaceId の子孫 (mySpaceId を除く) に接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` は、mySpaceId の直下の子に接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` は、mySpaceId の先祖の 1 つに接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` は、mySpaceId の子孫のうち、レベル 5 またはそれより小さい子孫に接続されているすべてのデバイスを返します。
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` は、mySpaceId と同じレベルのスペースに接続されているすべてのデバイスを返します。


## <a name="odata-support"></a>OData のサポート
/spaces の GET 呼び出しなど、コレクションを返す API のほとんどは、一般的な [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) システム クエリ オプションの次のサブセットをサポートします。  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - コレクション全体を表示する場合、1 回の呼び出しで 1 つのセットとしてコレクションを要求し、その後アプリケーション内でページングを実行してください。 

$count、$expand、$search などの他のクエリ オプションはサポートされていないことに注意してください。

### <a name="examples"></a>例

次の一覧は、OData のシステム クエリ オプションを使用したクエリのいくつかの例を示しています。

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>次の手順

API のいくつかの一般的なクエリ パターンについては、「[一般的なタスクについて Azure Digital Twins API をクエリする方法](how-to-query-common-apis.md)」を参照してください。

API エンドポイントの詳細については、[Digital Twins Swagger の使用方法](./how-to-use-swagger.md)に関するページをご覧ください。
