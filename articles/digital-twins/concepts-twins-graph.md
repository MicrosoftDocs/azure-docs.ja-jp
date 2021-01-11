---
title: デジタル ツインとツイン グラフ
titleSuffix: Azure Digital Twins
description: デジタル ツインの概念と、そのリレーションシップによってグラフが作成されるしくみについて説明します。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5821a1d1f6713ef39d7475fb004164e7c0fd71ec
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87062051"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>デジタル ツインとツイン グラフについて理解する

Azure Digital Twins ソリューションでは、環境内のエンティティは Azure **デジタル ツイン**で表されます。 デジタル ツインは、カスタム定義[モデル](concepts-models.md)のいずれかのインスタンスです。 それを**リレーションシップ**によって他のデジタル ツインに接続して、**ツイン グラフ**を形成できます。このツイン グラフは、環境全体を表しています。

> [!TIP]
> "Azure Digital Twins" は、この Azure サービス全体を指します。 "デジタル ツイン" または単に "ツイン" は、サービスのインスタンス内の個々のツイン ノードを指します。

## <a name="digital-twins"></a>Digital Twins

Azure Digital Twins インスタンスでデジタル ツインを作成するには、"*モデル*" をサービスにアップロードしておく必要があります。 モデルは、特に、プロパティ、テレメトリ メッセージ、特定のツインが持つことのできるリレーションシップのセットを記述したものです。 モデルで定義される情報の種類については、[*カスタム モデル*](concepts-models.md)に関するページを参照してください。

モデルを作成してアップロードしたら、クライアント アプリはその型のインスタンスを作成できます。これがデジタル ツインです。 たとえば、モデル *Floor* を作成したら、この型を使用する 1 つまたは複数のデジタル ツイン (*GroundFloor* という *Floor* 型ツイン、*Floor2* という別のツインなど) を作成できます。 

## <a name="relationships-a-graph-of-digital-twins"></a>リレーションシップ: デジタル ツインのグラフ

ツインは、そのリレーションシップによってツイン グラフに接続されます。 ツインが持つことのできるリレーションシップは、そのモデルの一部として定義されます。  

たとえば、モデル *Floor* で、*room* 型のツインをターゲットとする *contains* リレーションシップが定義されているとします。 この定義により、Azure Digital Twins では、任意の *Floor* ツインから任意の *Room* ツイン (*Room* サブタイプのツインを含む) への *contains* リレーションシップを作成できます。 

このプロセスにより、グラフ内にエッジ (リレーションシップ) によって接続された一連のノード (デジタル ツイン) が作成されます。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>API を使用して作成する

このセクションでは、クライアント アプリケーションからデジタル ツインとリレーションシップを作成する方法について説明します。 これらの各概念の内部で何が行われるかについて追加のコンテキストを提供するため、[Digital Twins API](how-to-use-apis-sdks.md) を利用する .NET コードの例が含まれています。

### <a name="create-digital-twins"></a>デジタル ツインを作成する

[DigitalTwins API](how-to-use-apis-sdks.md) を使用して、*Room* 型のツインをインスタンス化するクライアント コードのスニペットを次に示します。

Azure Digital Twins の現在のプレビューでは、ツインを作成する前に、ツインのすべてのプロパティを初期化しておく必要があります。 これを行うには、必要な初期化値を提供する JSON ドキュメントを作成します。

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

### <a name="create-relationships"></a>リレーションシップの作成

[DigitalTwins API](how-to-use-apis-sdks.md) を使用して、*GroundFloor* という *Floor* 型デジタル ツインと *Cafe* という *Room* 型デジタル ツインの間のリレーションシップを構築するクライアント コードの例を次に示します。

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

## <a name="json-representations-of-graph-elements"></a>グラフ要素の JSON 表現

デジタル ツイン データとリレーションシップ データは、どちらも JSON 形式で保存されます。 つまり、Azure Digital Twins インスタンスで[ツイン グラフのクエリを実行する](how-to-query-graph.md)と、結果は作成済みのデジタル ツインとリレーションシップの JSON 表現になります。

### <a name="digital-twin-json-format"></a>デジタル ツインの JSON 形式

JSON オブジェクトとして表された場合、デジタル ツインには次のフィールドが表示されます。

| フィールド名 | 説明 |
| --- | --- |
| `$dtId` | デジタル ツインの ID を表すユーザー指定の文字列 |
| `$etag` | Web サーバーによって割り当てられた標準 HTTP フィールド |
| `$conformance` | このデジタル ツインの適合性状態 (*conformant*、*non-conformant*、*unknown*) を含む列挙型 |
| `{propertyName}` | JSON のプロパティ値 (`string`、数値型、またはオブジェクト) |
| `$relationships` | リレーションシップ コレクションへのパスの URL。 デジタル ツインに発信リレーションシップ エッジがない場合、このフィールドは存在しません。 |
| `$metadata.$model` | [省略可能] このデジタル ツインを特徴付けるモデル インターフェイスの ID |
| `$metadata.{propertyName}.desiredValue` | [書き込み可能なプロパティ専用] 指定されたプロパティの目的の値 |
| `$metadata.{propertyName}.desiredVersion` | [書き込み可能なプロパティ専用] 目的の値のバージョン |
| `$metadata.{propertyName}.ackVersion` | デジタル ツインを実装するデバイス アプリによって確認されたバージョン |
| `$metadata.{propertyName}.ackCode` | [書き込み可能なプロパティ専用] デジタル ツインを実装するデバイス アプリによって返される `ack` コード |
| `$metadata.{propertyName}.ackDescription` | [書き込み可能なプロパティ専用] デジタル ツインを実装するデバイス アプリによって返される `ack` の説明 |
| `{componentName}` | コンポーネントのプロパティ値とメタデータを含む JSON オブジェクト。ルート オブジェクトの場合と似たものです。 このオブジェクトは、コンポーネントにプロパティがない場合でも存在します。 |
| `{componentName}.{propertyName}` | コンポーネントの、JSON のプロパティ値 (`string`、数値型、またはオブジェクト) |
| `{componentName}.$metadata` | コンポーネントのメタデータ情報。ルートレベルの `$metadata` と似たものです。 |

JSON オブジェクトとして書式設定されたデジタル ツインの例を次に示します。

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>リレーションシップの JSON 形式

JSON オブジェクトとして表された場合、デジタル ツインのリレーションシップには次のフィールドが表示されます。

| フィールド名 | 説明 |
| --- | --- |
| `$relationshipId` | このリレーションシップの ID を表すユーザー指定の文字列。 この文字列は、ソース デジタル ツインのコンテキストで一意です。つまり、`sourceId` + `relationshipId` は、Azure Digital Twins インスタンスのコンテキストで一意になるということでもあります。 |
| `$etag` | Web サーバーによって割り当てられた標準 HTTP フィールド |
| `$sourceId` | ソース デジタル ツインの ID |
| `$targetId` | ターゲット デジタル ツインの ID |
| `$relationshipName` | リレーションシップの名前 |
| `{propertyName}` | [省略可能] このリレーションシップの、JSON のプロパティ値 (`string`、数値型、またはオブジェクト) |

JSON オブジェクトとして書式設定されたリレーションシップの例を次に示します。

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>次のステップ

Azure Digital Twins の API を使用してグラフ要素を管理する方法を確認します。
* [*方法: Digital Twins を管理する*](how-to-manage-twin.md)
* [*方法: リレーションシップを使用してツイン グラフを管理する*](how-to-manage-graph.md)

または、Azure Digital Twins のツイン グラフに対して情報のクエリを行う方法について学習します。
* "[*概念: クエリ言語*](concepts-query-language.md)