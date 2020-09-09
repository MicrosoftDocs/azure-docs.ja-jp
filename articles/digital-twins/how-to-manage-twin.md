---
title: デジタル ツインを管理する
titleSuffix: Azure Digital Twins
description: 個々のツインとリレーションシップを取得、更新、削除する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b88d017110f4d7b9859f2d801c5405ecee1589c5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297469"
---
# <a name="manage-digital-twins"></a>デジタル ツインを管理する

環境内のエンティティは、[デジタル ツイン](concepts-twins-graph.md)で表されます。 デジタル ツインの管理には、作成、変更、削除などが伴います。 これらの操作を実行するには、[**DigitalTwins API**](how-to-use-apis-sdks.md)、[NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用します。

この記事では、デジタル ツインの管理に重点を置いて説明します。リレーションシップと[ツイン グラフ](concepts-twins-graph.md)の全体的な操作については、「[*方法: リレーションシップを使用してツイン グラフを管理する*](how-to-manage-graph.md)」を参照してください。

> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

## <a name="create-a-digital-twin"></a>デジタル ツインを作成する

ツインを作成するには、次のようにサービス クライアントで `CreateDigitalTwin` メソッドを使用します。

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

デジタル ツインを作成するには、以下を指定する必要があります。
* デジタル ツインの目的の ID
* 使用する[モデル](concepts-models.md) 

必要に応じて、デジタル ツインのすべてのプロパティの初期値を指定できます。 

モデルと初期プロパティ値は、`initData` パラメーターによって提供されます。これは、関連データを含む JSON 文字列です。 このオブジェクトを構造化する方法の詳細については、次のセクションに進んでください。

> [!TIP]
> ツインを作成または更新した後、変更が[クエリ](how-to-query-graph.md)に反映されるまでに最大 10 秒の待機時間が発生する可能性があります。 `GetDigitalTwin` API ([この記事で後ほど](#get-data-for-a-digital-twin)説明します) ではこの遅延が発生しないため、迅速な応答が必要な場合は、クエリの代わりに API 呼び出しを使用して、新しく作成されたツインを確認してください。 

### <a name="initialize-model-and-properties"></a>モデルとプロパティを初期化する

ツイン作成 API は、ツイン プロパティの有効な JSON 記述にシリアル化されるオブジェクトを受け入れます。 "[*デジタル ツインとツイン グラフの概念*](concepts-twins-graph.md)" に関する記事をご覧ください。 

したがって、まず、ツインとそのプロパティ データを表すデータ オブジェクトを作成します。 次に、`JsonSerializer` を使用して、このシリアル化されたバージョンを `initdata` パラメーターの API 呼び出しに渡すことができます。

パラメーター オブジェクトは手動で作成することも、用意されているヘルパー クラスを使用して作成することもできます。 それぞれの例を以下に示します。

#### <a name="create-twins-using-manually-created-data"></a>手動で作成したデータを使用してツインを作成する

カスタム ヘルパー クラスを使用しない場合は、`Dictionary<string, object>` でツインのプロパティを表すことができます。ここで、`string` はプロパティの名前であり、`object` はプロパティとその値を表すオブジェクトです。

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>ヘルパー クラスを使用してツインを作成する

`BasicDigitalTwin` のヘルパー クラスを使用すると、"ツイン" オブジェクトにプロパティ フィールドをより直接的に格納できます。 `Dictionary<string, object>` を使用してプロパティのリストを作成することもできます。それは、`CustomProperties` としてツイン オブジェクトに直接追加できます。

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

>[!NOTE]
> `BasicDigitalTwin` オブジェクトには、`Id` フィールドが付属しています。 このフィールドは空のままとすることができますが、ID 値を追加する場合は、それが、`CreateDigitalTwin` 呼び出しに渡される ID パラメーターと一致する必要があります。 上記の例の場合、以下のようになります。
>
>```csharp
>twin.Id = "myNewRoomID";
>```

## <a name="get-data-for-a-digital-twin"></a>デジタル ツインのデータを取得する

次の呼び出しにより、任意のデジタル ツインの全データにアクセスできます。

```csharp
object result = await client.GetDigitalTwin(id);
```

この呼び出しでは、ツイン データが JSON 文字列として返されます。 

`GetDigitalTwin` を使用してツインを取得すると、少なくとも 1 回は設定されたプロパティだけが返されます。

>[!TIP]
>ツインの `displayName` はモデル メタデータの一部であるため、ツイン インスタンスのデータを取得するときには表示されません。 この値を表示するには、[モデルから取得する](how-to-manage-model.md#retrieve-models)ことができます。

1 つの API 呼び出しを使用して複数のツインを取得するには、クエリ API の例を、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

*Moon* を定義する次のモデル ([Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL) で記述) について考えてみましょう。

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

*Moon* 型ツインで `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` を呼び出すと、結果は次のようになります。

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

デジタル ツインの定義済みプロパティは、デジタル ツインの最上位プロパティとして返されます。 DTDL 定義に含まれていないメタデータやシステム情報は、`$` プレフィックス付きで返されます。 メタデータ プロパティは次のとおりです。
* この Azure Digital Twins インスタンスのデジタル ツインの ID (`$dtId`)。
* Web サーバーによって割り当てられた標準 HTTP フィールド (`$etag`)。
* `$metadata` セクションのその他のプロパティ。 これには以下が含まれます。
    - デジタル ツインのモデルの DTMI。
    - 書き込み可能な各プロパティの同期の状態。 これは、サービスとデバイスの状態が異なる可能性がある場合 (デバイスがオフラインの場合など) に、デバイスで最も役立ちます。 現在、このプロパティは IoT Hub に接続されている物理デバイスにのみ適用されます。 メタデータ セクションのデータにより、プロパティの完全な状態と、最終変更のタイムスタンプを把握できます。 同期の状態の詳細については、デバイスの状態の同期に関する[こちらの IoT Hub チュートリアル](../iot-hub/tutorial-device-twins.md)をご覧ください。
    - IoT Hub や Azure Digital Twins などのサービス固有のメタデータ。 

`System.Text.Json` など、任意の JSON 解析ライブラリを使用して、返されたツインの JSON を解析できます。

SDK に含まれているシリアル化ヘルパー クラスの `BasicDigitalTwin` を使用することもできます。この場合、ツインのコア メタデータとプロパティが解析済みの形で返されます。 たとえば次のようになります。

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

シリアル化ヘルパー クラスの詳細については、"[*Azure Digital Twins の API および SDK を使用する方法*](how-to-use-apis-sdks.md)" に関するページで参照してください。

## <a name="update-a-digital-twin"></a>デジタル ツインを更新する

デジタル ツインのプロパティを更新するには、置き換える情報を [JSON Patch](http://jsonpatch.com/) 形式で記述します。 これにより、一度に複数のプロパティを置き換えることができます。 次に、その JSON Patch ドキュメントを `Update` メソッドに渡します。

```csharp
await client.UpdateDigitalTwin(id, patch);
```

パッチの呼び出しでは、1 つのツインのプロパティを必要な数だけ (それらをすべてであっても) 更新することができます。 複数のツイン間でプロパティを更新する必要がある場合は、ツインごとに個別の更新呼び出しが必要になります。

> [!TIP]
> ツインを作成または更新した後、変更が[クエリ](how-to-query-graph.md)に反映されるまでに最大 10 秒の待機時間が発生する可能性があります。 `GetDigitalTwin` API ([この記事の前の方で](#get-data-for-a-digital-twin)説明しました) ではこの遅延が発生しないため、迅速な応答が必要な場合は、クエリの代わりに API 呼び出しを使用して、新しく更新されたツインを確認してください。 

JSON Patch コードの例を次に示します。 このドキュメントでは、適用先のデジタル ツインの *mass* および *radius* プロパティ値を置き換えます。

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

パッチは手動で作成することも、[SDK](how-to-use-apis-sdks.md) のシリアル化ヘルパー クラスを使用して作成することもできます。 それぞれの例を以下に示します。

#### <a name="create-patches-manually"></a>パッチを手動で作成する

```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>ヘルパー クラスを使用してパッチを作成する

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>デジタル ツイン コンポーネントのプロパティを更新する

モデルには、他のモデルで構成できるコンポーネントが含まれている場合があることに注意してください。 

デジタル ツインのコンポーネントのプロパティにパッチを適用するには、JSON Patch で path 構文を使用します。

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>デジタル ツインのモデルを更新する

`Update` 関数を使用して、デジタル ツインを別のモデルに移行することもできます。 

たとえば、デジタル ツインのメタデータの `$model` フィールドを置き換える、次の JSON Patch ドキュメントについて考えてみましょう。

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

この操作は、パッチによって変更されるデジタル ツインが新しいモデルに適合する場合にのみ成功します。 

次の例を確認してください。
1. *foo_old* というモデルを使用するデジタル ツインがあるとします。 *foo_old* では、必須プロパティの *mass* を定義しています。
2. 新しいモデル *foo_new* では、mass プロパティを定義し、新しい必須プロパティの *temperature* を追加します。
3. パッチの適用後、このデジタル ツインには mass と temperature の両方のプロパティが含まれている必要があります。 

この場合のパッチでは、次のように、モデルとツインの temperature プロパティを両方とも更新する必要があります。

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>競合する更新呼び出しを処理する

Azure Digital Twins では、すべての受信要求が確実に 1 つずつ処理されます。 つまり、複数の関数が同時にツイン上の同じプロパティを更新しようとする場合でも、競合を処理するための明示的なロック コードを記述する**必要はありません**。

この動作はツインごとに行われます。 

例として、これら 3 つの呼び出しが同時に到着するシナリオを考えてみましょう。 
*   *Twin1* でのプロパティ A の書き込み
*   *Twin1* でのプロパティ B の書き込み
*   *Twin2* でのプロパティ A の書き込み

*Twin1* を変更する 2 つの呼び出しが 1 つずつ実行され、変更のたびに変更メッセージが生成されます。 *Twin2* を変更する呼び出しは、到着したらすぐに、競合なしで同時に実行できます。

## <a name="delete-a-digital-twin"></a>デジタル ツインを削除する

`DeleteDigitalTwin(ID)` を使用してツインを削除できます。 ただし、ツインを削除できるのは、ツインにリレーションシップがない場合だけです。 最初にすべてのリレーションシップを削除する必要があります。 

このコードの例を次に示します。

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>すべてのデジタル ツインを削除する

一度にすべてのツインを削除する方法の例については、"[*サンプル クライアント アプリを使用した基本事項の確認に関するチュートリアル*](tutorial-command-line-app.md)" で再利用できます。 *CommandLoop.cs* ファイルでは、`CommandDeleteAllTwins` 関数でこれを実行します。

## <a name="manage-twins-with-cli"></a>CLI を使用してツインを管理する

ツインは、Azure Digital Twins CLI を使用して管理することもできます。 コマンドについては、"[*Azure Digital Twins CLI を使用する方法*](how-to-use-cli.md)" に関するページを参照してください。

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="next-steps"></a>次のステップ

デジタル ツイン間のリレーションシップを作成および管理する方法を確認します。
* [*方法: リレーションシップを使用してツイン グラフを管理する*](how-to-manage-graph.md)