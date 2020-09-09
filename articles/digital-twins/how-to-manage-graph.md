---
title: リレーションシップを使ってツイン グラフを管理する
titleSuffix: Azure Digital Twins
description: リレーションシップを使って接続することでデジタル ツインのグラフを管理する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f7239e0c13478af712d8e8d9dad8fda23fe42c7
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87125534"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>リレーションシップを使ってデジタル ツインのグラフを管理する

Azure Digital Twins の中核は、環境全体を表す[ツイン グラフ](concepts-twins-graph.md)です。 ツイン グラフは、**リレーションシップ**を介して接続された個々のデジタル ツインで構成されています。

機能する [Azure Digital Twins インスタンス](how-to-set-up-instance-scripted.md)があり、クライアント アプリで[認証](how-to-authenticate-client.md)コードを設定すると、[**DigitalTwins API**](how-to-use-apis-sdks.md) を使用して Azure Digital Twins インスタンス内のデジタル ツインとそのリレーションシップを作成、変更、削除することができます。 [.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用することもできます。

この記事では、リレーションシップとグラフ全体の管理に焦点を当てます。個々のデジタル ツインの操作については、[ *「デジタル ツインを管理する」方法*](how-to-manage-twin.md)を参照してください。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>リレーションシップの作成

リレーションシップには、さまざまなデジタル ツインが相互にどのように接続されるかが記述され、それによってツイン グラフの基礎が形成されます。

リレーションシップは、`CreateRelationship` 呼び出しを使用して作成されます。 

リレーションシップを作成するには、以下を指定する必要があります。
* ソース ツイン ID (リレーションシップの発生元のツイン)
* ターゲット ツイン ID (リレーションシップの到達先のツイン)
* リレーションシップ名
* リレーションシップ ID

リレーションシップ ID は、指定されたソース ツイン内で一意である必要があります。 グローバルに一意である必要はありません。
たとえば、ツイン *foo* の場合、それぞれのリレーションシップ ID は一意である必要があります。 ただし、別のツインである *bar* は、*foo* リレーションシップの同じ ID と一致する発信リレーションシップを持つことができます。 

次のコード サンプルは、Azure Digital Twins インスタンスにリレーションシップを追加する方法を示しています。

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

ヘルパー クラス `BasicRelationship` の詳細については、[ *「Azure Digital Twins の API および SDK を使用する」方法*](how-to-use-apis-sdks.md)を参照してください。

## <a name="list-relationships"></a>リレーションシップの一覧表示

グラフ内の特定のツインのリレーションシップの一覧にアクセスするには、以下を使用できます。

```csharp
await client.GetRelationshipsAsync(id);
```

これによって、呼び出しの同期または非同期のバージョンのどちらを使用するかに応じて、`Azure.Pageable<T>` または `Azure.AsyncPageable<T>` が返されます。

リレーションシップの一覧を取得する完全な例を次に示します。

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

取得したリレーションシップを使用して、グラフ内の他のツインに移動できます。 これを行うには、返されたリレーションシップから `target` フィールドを読み取り、それを `GetDigitalTwin` への次の呼び出しの ID として使用します。 

### <a name="find-relationships-to-a-digital-twin"></a>デジタル ツインとのリレーションシップを見つける

Azure Digital Twins には、特定のツインとのすべての受信リレーションシップを検索する API もあります。 これは、逆方向のナビゲーションの場合やツインを削除するときに便利です。

前のコード サンプルでは、発信リレーションシップの検索に重点を置いていました。 次の例は似ていますが、代わりに受信リレーションシップを検索します。 また、これらは検出後に削除されます。

`IncomingRelationship` の呼び出しからは、リレーションシップ全体が返されないことに注意してください。

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>リレーションシップの削除

`DeleteRelationship(source, relId);` を使用してリレーションシップを削除することができます。

最初のパラメーターには、ソース ツイン (リレーションシップの発生元のツイン) を指定します。 もう一方のパラメーターはリレーションシップ ID です。 リレーションシップ ID はツインのスコープ内でのみ一意であるため、ツイン ID とリレーションシップ ID の両方が必要です。

## <a name="create-a-twin-graph"></a>ツイン グラフを作成する 

次のコード スニペットでは、この記事のリレーションシップ操作を使用して、デジタル ツインとリレーションシップからツイン グラフを作成します。

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>スプレッドシートからツイン グラフを作成する

実際の使用例では、多くの場合、別のデータベース、または場合によってはスプレッドシートに格納されたデータからツイン階層が作成されます。 このセクションでは、スプレッドシートを解析する方法について説明します。

デジタル ツインのセットと作成されるリレーションシップを説明する次のデータ テーブルがあるとします。

| モデル    | id | Parent | [リレーションシップ名] | その他のデータ |
| --- | --- | --- | --- | --- |
| floor    | Floor01 | | | ... |
| room    | Room10 | Floor01 | contains | ... |
| room    | Room11 | Floor01 | contains | ... |
| room    | Room12 | Floor01 | contains | ... |
| floor    | Floor02 | | | ... |
| room    | Room21 | Floor02 | contains | ... |
| room    | Room22 | Floor02 | contains | ... |

次のコードでは、[Microsoft Graph API](https://docs.microsoft.com/graph/overview) を使用してスプレッドシートを読み取り、結果から Azure Digital Twins ツイン グラフを作成しています。

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>CLI を使用してリレーションシップを管理する

ツインとそのリレーションシップは、Azure Digital Twins CLI を使用して管理することもできます。 コマンドについては、[ *「Azure Digital Twins CLI を使用する」方法*](how-to-use-cli.md)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins ツイン グラフのクエリについて確認します。
* "[*概念: クエリ言語*](concepts-query-language.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)