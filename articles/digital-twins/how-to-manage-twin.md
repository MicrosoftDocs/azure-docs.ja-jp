---
title: デジタル ツインを管理する
titleSuffix: Azure Digital Twins
description: 個々のツインとリレーションシップを取得、更新、削除する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e20cd09ce3d9eb1937819da79cea17bdd14a07dc
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433269"
---
# <a name="manage-digital-twins"></a>デジタル ツインを管理する

環境内のエンティティは、[デジタル ツイン](concepts-twins-graph.md)で表されます。 デジタル ツインの管理には、作成、変更、削除などが伴います。 これらの操作を実行するには、[**DigitalTwins API**](/rest/api/digital-twins/dataplane/twins)、[NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用します。

この記事では、デジタル ツインの管理に重点を置いて説明します。リレーションシップと [ツイン グラフ](concepts-twins-graph.md)の全体的な操作については、「[*方法: リレーションシップを使用してツイン グラフを管理する*](how-to-manage-graph.md)」を参照してください。

> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>ツインの管理方法

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>デジタル ツインを作成する

ツインを作成するには、次のようにサービス クライアントで `CreateOrReplaceDigitalTwinAsync()` メソッドを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

デジタル ツインを作成するには、以下を指定する必要があります。
* デジタル ツインの目的の ID
* 使用する[モデル](concepts-models.md)

必要に応じて、デジタル ツインのすべてのプロパティの初期値を指定できます。 プロパティはオプションとして扱われ、後で設定できますが、**設定されるまではツインの一部として表示されません**。

>[!NOTE]
>ツイン プロパティは初期化する必要がありませんが、ツインの作成時にそのあらゆる [コンポーネント](concepts-models.md#elements-of-a-model)を設定する **必要があります**。 空のオブジェクトにすることができますが、コンポーネント自体は存在する必要があります。

モデルと初期プロパティ値は、`initData` パラメーターによって提供されます。これは、関連データを含む JSON 文字列です。 このオブジェクトを構造化する方法の詳細については、次のセクションに進んでください。

> [!TIP]
> ツインを作成または更新した後、変更が[クエリ](how-to-query-graph.md)に反映されるまでに最大 10 秒の待機時間が発生する可能性があります。 `GetDigitalTwin` API ([この記事で後ほど](#get-data-for-a-digital-twin)説明します) ではこの遅延が発生しないため、迅速な応答が必要な場合は、クエリの代わりに API 呼び出しを使用して、新しく作成されたツインを確認してください。 

### <a name="initialize-model-and-properties"></a>モデルとプロパティを初期化する

ツインの作成時、ツインのプロパティを初期化できます。 

ツイン作成 API は、ツイン プロパティの有効な JSON 記述にシリアル化されるオブジェクトを受け入れます。 "[*デジタル ツインとツイン グラフの概念*](concepts-twins-graph.md)" に関する記事をご覧ください。 

まず、ツインとそのプロパティ データを表すデータ オブジェクトを作成することができます。 パラメーター オブジェクトは手動で作成することも、用意されているヘルパー クラスを使用して作成することもできます。 それぞれの例を以下に示します。

#### <a name="create-twins-using-manually-created-data"></a>手動で作成したデータを使用してツインを作成する

カスタム ヘルパー クラスを使用しない場合は、`Dictionary<string, object>` でツインのプロパティを表すことができます。ここで、`string` はプロパティの名前であり、`object` はプロパティとその値を表すオブジェクトです。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>ヘルパー クラスを使用してツインを作成する

`BasicDigitalTwin` のヘルパー クラスを使用すると、"ツイン" オブジェクトにプロパティ フィールドを直接的に格納できます。 `Dictionary<string, object>` を使用してプロパティのリストを作成することもできます。それは、`CustomProperties` としてツイン オブジェクトに直接追加できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` オブジェクトには、`Id` フィールドが付属しています。 このフィールドは空のままとすることができますが、ID 値を追加する場合は、それが、`CreateOrReplaceDigitalTwinAsync()` 呼び出しに渡される ID パラメーターと一致する必要があります。 次に例を示します。
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>デジタル ツインのデータを取得する

次のように `GetDigitalTwin()` メソッドを呼び出すことで、デジタル ツインの詳細にアクセスすることができます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

この呼び出しからは、ツイン データが `BasicDigitalTwin` のような厳密に型指定されたオブジェクト型として返されます。 `BasicDigitalTwin` は、SDK に含まれているシリアル化ヘルパー クラスであり、ツインのコア メタデータとプロパティが解析済みの形で返されます。 これを使用してツインの詳細を表示する方法の例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

`GetDigitalTwin()` メソッドを使用してツインを取得すると、少なくとも 1 回は設定されたプロパティだけが返されます。

>[!TIP]
>ツインの `displayName` はモデル メタデータの一部であるため、ツイン インスタンスのデータを取得するときには表示されません。 この値を表示するには、[モデルから取得する](how-to-manage-model.md#retrieve-models)ことができます。

1 つの API 呼び出しを使用して複数のツインを取得するには、クエリ API の例を、[*ツイン グラフにクエリを実行する*](how-to-query-graph.md)方法に関する記事を参照してください。

*Moon* を定義する次のモデル ([Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL) で記述) について考えてみましょう。

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

*Moon* 型ツインで `object result = await client.GetDigitalTwinAsync("my-moon");` を呼び出すと、結果は次のようになります。

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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

`BasicDigitalTwin` などのシリアル化ヘルパー クラスの詳細については、"[*Azure Digital Twins の API および SDK を使用する方法*](how-to-use-apis-sdks.md)" に関するページで参照してください。

## <a name="view-all-digital-twins"></a>すべてのデジタル ツインを表示する

インスタンス内のすべてのデジタル ツインを表示するには、[クエリ](how-to-query-graph.md)を使用します。 クエリは、[Query API](/rest/api/digital-twins/dataplane/query) または [CLI コマンド](how-to-use-cli.md)を使用して実行できます。

次に示すのは、インスタンス内のすべてのデジタル ツインの一覧を返す基本的なクエリの本文です。

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>デジタル ツインを更新する

デジタル ツインのプロパティを更新するには、置き換える情報を [JSON Patch](http://jsonpatch.com/) 形式で記述します。 これにより、一度に複数のプロパティを置き換えることができます。 次に、その JSON Patch ドキュメントを `UpdateDigitalTwin()` メソッドに渡します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

パッチの呼び出しでは、1 つのツインのプロパティを必要な数だけ (それらをすべてであっても) 更新することができます。 複数のツイン間でプロパティを更新する必要がある場合は、ツインごとに個別の更新呼び出しが必要になります。

> [!TIP]
> ツインを作成または更新した後、変更が[クエリ](how-to-query-graph.md)に反映されるまでに最大 10 秒の待機時間が発生する可能性があります。 `GetDigitalTwin` API ([この記事の前の方で](#get-data-for-a-digital-twin)説明しました) ではこの遅延が発生しないため、迅速な応答が必要な場合は、クエリの代わりに API 呼び出しを使用して、新しく更新されたツインを確認してください。 

JSON Patch コードの例を次に示します。 このドキュメントでは、適用先のデジタル ツインの *mass* および *radius* プロパティ値を置き換えます。

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Azure .NET SDK の [JsonPatchDocument](/dotnet/api/azure.jsonpatchdocument) を使用して、修正プログラムを作成できます。 次に例を示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>デジタル ツイン コンポーネントのプロパティを更新する

モデルには、他のモデルで構成できるコンポーネントが含まれている場合があることに注意してください。 

デジタル ツインのコンポーネントのプロパティにパッチを適用するには、JSON Patch で path 構文を使用することができます。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>デジタル ツインのモデルを更新する

`UpdateDigitalTwin()` 関数を使用して、デジタル ツインを別のモデルに移行することもできます。 

たとえば、デジタル ツインのメタデータの `$model` フィールドを置き換える、次の JSON Patch ドキュメントについて考えてみましょう。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

この操作は、パッチによって変更されるデジタル ツインが新しいモデルに適合する場合にのみ成功します。 

次の例を確認してください。
1. *foo_old* というモデルを使用するデジタル ツインがあるとします。 *foo_old* では、必須プロパティの *mass* を定義しています。
2. 新しいモデル *foo_new* では、mass プロパティを定義し、新しい必須プロパティの *temperature* を追加します。
3. パッチの適用後、このデジタル ツインには mass と temperature の両方のプロパティが含まれている必要があります。 

この場合のパッチでは、次のように、モデルとツインの temperature プロパティを両方とも更新する必要があります。

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>競合する更新呼び出しを処理する

Azure Digital Twins では、すべての受信要求が確実に 1 つずつ処理されます。 つまり、複数の関数が同時にツイン上の同じプロパティを更新しようとする場合でも、競合を処理するための明示的なロック コードを記述する **必要はありません**。

この動作はツインごとに行われます。 

例として、これら 3 つの呼び出しが同時に到着するシナリオを考えてみましょう。 
*   *Twin1* でのプロパティ A の書き込み
*   *Twin1* でのプロパティ B の書き込み
*   *Twin2* でのプロパティ A の書き込み

*Twin1* を変更する 2 つの呼び出しが 1 つずつ実行され、変更のたびに変更メッセージが生成されます。 *Twin2* を変更する呼び出しは、到着したらすぐに、競合なしで同時に実行できます。

## <a name="delete-a-digital-twin"></a>デジタル ツインを削除する

`DeleteDigitalTwin()` メソッドを使用してツインを削除することができます。 ただし、ツインを削除できるのは、ツインにリレーションシップがない場合だけです。 そのため、最初にツインの受信と送信のリレーションシップを削除します。

ここに、ツインとそのリレーションシップを削除するコードの例を示します。 `DeleteDigitalTwin` SDK の呼び出しが強調表示されており、この例の幅広いコンテキストでの位置が明確になっています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>すべてのデジタル ツインを削除する

一度にすべてのツインを削除する方法の例については、"[*サンプル クライアント アプリを使用した基本事項の確認に関するチュートリアル*](tutorial-command-line-app.md)" で再利用できます。 *CommandLoop.cs* ファイルでは、`CommandDeleteAllTwins()` 関数でこれを実行します。

## <a name="runnable-digital-twin-code-sample"></a>実行可能なデジタル ツインのコード サンプル

次の実行可能なコード サンプルを使用してツインを作成し、その詳細を更新して、ツインを削除することができます。 

### <a name="set-up-the-runnable-sample"></a>実行可能なサンプルを設定する

このスニペットには、[Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) モデル定義 (「[*チュートリアル: サンプル クライアント アプリを使用して Azure Digital Twins を試す*](tutorial-command-line-app.md)」が使用されています。 [こちら](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)のリンクを使用してファイルに直接移動するか、完全なエンドツーエンドのサンプル プロジェクトの一部としてダウンロードすることができます。

このサンプルを実行する前に、以下を実行します。
1. モデル ファイルをダウンロードしてプロジェクトに配置し、以下のコードの `<path-to>` プレースホルダーを置き換えて、プログラムに検索場所を指示します。
2. プレースホルダー `<your-instance-hostname>` を Azure Digital Twins インスタンスのホスト名に置き換えます。
3. Azure Digital Twins を操作するために必要な 2 つの依存関係をプロジェクトに追加します。 1 つ目は [.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client) 用のパッケージであり、2 つ目では Azure に対する認証に役立つツールが提供されます。

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

サンプルを直接実行する場合は、ローカルの資格情報も設定する必要があります。 次のセクションでは、これについて説明します。
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>サンプルを実行する

上記の手順を完了すると、次のサンプル コードを直接実行できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

上記のプログラムのコンソール出力は次のようになります。 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="ツインが作成、更新、削除されたことを示すコンソール出力" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>次のステップ

デジタル ツイン間のリレーションシップを作成および管理する方法を確認します。
* [*方法: リレーションシップを使用してツイン グラフを管理する*](how-to-manage-graph.md)