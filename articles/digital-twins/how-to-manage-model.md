---
title: カスタム モデルを管理する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 内でモデルを作成、編集、および削除する方法を説明します。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ff89b38de1ff62ddea328a49b998692e8039341f
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661556"
---
# <a name="manage-azure-digital-twins-models"></a>Azure Digital Twins のモデルを管理する

[**DigitalTwinsModels API**](how-to-use-apis-sdks.md)、[.NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)、または [Azure Digital Twins CLI](how-to-use-cli.md) の使用について、Azure Digital Twins インスタンスで認識されている[モデル](concepts-models.md)を管理できます。 

管理操作には、モデルのアップロード、検証、取得、および削除が含まれます。 

## <a name="create-models"></a>モデルを作成する

Azure Digital Twins のモデルは DTDL で記述され、 *.json* ファイルとして保存されます。 また、[DTDL 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)も用意されており、[Visual Studio Code](https://code.visualstudio.com/) に使用できます。これにより、構文の検証や、DTDL ドキュメントの作成を容易にするその他の機能が提供されます。

病院が部屋をデジタルで表現する例を考えてみましょう。 各部屋には、手の洗浄を監視するためのスマート ソープ ディスペンサーと、部屋の行き来を監視するセンサーが含まれています。

ソリューションに向けた最初の手順は、病院の状況を表すモデルを作成することです。 このシナリオの病室は、次のように記述できます。

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> これは、クライアントプロジェクトの一部としてアップロードされる、モデルが定義され保存されている .json ファイルのサンプル本文です。 一方、REST API 呼び出しは、上記のようなモデル定義の配列を取得します (これは .NET SDK の `IEnumerable<string>` にマップされています)。 そのため、REST API でこのモデルを直接使用するには、ブラケットで囲みます。

このモデルでは、病室の名前と一意の ID、および訪問者の数と手洗いの状態を表すプロパティを定義します (これらのカウンターはモーション センサーおよびスマート ソープ ディスペンサー から更新され、組み合わせて*手洗いパーセンテージ* プロパティを計算します)。 このモデルでは、*hasDevices* のリレーションシップも定義されています。これは、この *Room* モデルに基づいて[デジタル ツイン](concepts-twins-graph.md)を実際のデバイスに接続するために使用されます。

この方法に従うと、病院の病棟、ゾーン、または病院自体のモデルを定義することができます。

### <a name="validate-syntax"></a>構文を検証する

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>API を使用してモデルを管理する

以下のセクションでは、[Azure Digital Twins API と SDK](how-to-use-apis-sdks.md) を使用して、さまざまなモデル管理操作を完了する方法について説明します。

> [!NOTE]
> 以下の例では、簡潔にするためにエラー処理は含まれていません。 ただし、try/catch ブロックでサービス呼び出しをラップするには、プロジェクト内で含めることを強くお勧めします。

> [!TIP] 
> すべての SDK メソッドに同期バージョンと非同期バージョンがあることに注意してください。 ページング呼び出しの場合、非同期メソッドは `AsyncPageable<T>` を返し、同期バージョンが `Pageable<T>` を返します。

### <a name="upload-models"></a>モデルのアップロード

モデルを作成したら、Azure Digital Twins インスタンスにアップロードできます。

> [!TIP]
> ご自分のモデルは、ご自分の Azure Digital Twins インスタンスにアップロードする前に、オフラインで検証することをお勧めします。 モデルをサービスにアップロードする前に、[DTDL クライアント側パーサー ライブラリ](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)と [DTDL 検証ツールのサンプル](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)を使用して ([*方法:モデルの解析と検証*](how-to-parse-models.md)に関する記事を参照)、モデルを確認できます。

モデルをアップロードする準備が整ったら、次のコード スニペットを使用できます。

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

`CreateModels` メソッドが 1 つのトランザクションで複数のファイルを受け入れることを確認します。 次に例を示します。

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

モデル ファイルには、1 つ以上のモデルを含めることができます。 この場合、モデルを JSON 配列に配置する必要があります。 次に例を示します。

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
アップロード時に、サービスによってモデル ファイルが検証されます。

### <a name="retrieve-models"></a>モデルの取得

Azure Digital Twins インスタンスに格納されているモデルを一覧表示したり、取得したりすることができます。 

そのためのオプションを次に示します。
* すべてのモデルの取得
* 単一のモデルの取得
* 依存関係を持つ単一つのモデルの取得
* モデルのメタデータの取得

呼び出し例を次に示します。

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

モデルを取得するための API 呼び出しはすべて `ModelData` オブジェクトを返します。 `ModelData` には、名前、DTMI、モデルの作成日など、Azure Digital Twins インスタンスに格納されているモデルに関するメタデータが含まれます。 `ModelData` オブジェクトには、必要に応じてモデル自体を含めることもできます。 パラメーターによっては、取得呼び出しを使用して、メタデータのみ (これは、使用可能なツールの UI の一覧やモデル全体を表示するシナリオで役に立ちます)、またはモデル全体を取得できます。

`RetrieveModelWithDependencies` 呼び出しでは、要求されたモデルだけでなく、要求されたモデルが依存しているすべてのモデルも返されます。

モデルは、必ずしもアップロードされたドキュメント形式では返されません。 Azure Digital Twins は、返される形式が意味的に同等であることだけを保証します。 

### <a name="remove-models"></a>モデルの削除

モデルは、次の 2 つの方法のいずれかでサービスから削除することもできます。
* **使用停止**:モデルが使用停止になると、新しいデジタル ツインを作成するために使用できなくなります。 このモデルを既に使用している既存のデジタル ツインは影響を受けないため、プロパティの変更やリレーションシップの追加や削除などの操作で更新できます。
* **削除**:これにより、ソリューションからモデルが完全に削除されます。 このモデルを使用していたすべてのツインは、有効なモデルに関連付けられなくなったため、モデルがまったくないかのように扱われます。 これらのツインは引き続き読み取ることができますが、別のモデルに再割り当てされるまで、更新を行うことはできません。

これらは別の機能であり、互いに影響を与えることはありませんが、一緒に使用してモデルを段階的に削除することもできます。 

#### <a name="decommissioning"></a>使用停止

モデルの使用を停止するコードを次に示します。

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

モデルの使用停止状態は、モデル取得 API によって返された `ModelData` レコードに含まれます。

#### <a name="deletion"></a>削除

インスタンス内のすべてのモデルを一度に削除することも、個別に実行することもできます。

すべてのモデルを削除する方法の例について、ダウンロードできるサンプル アプリは、[*サンプル クライアント アプリを使用した基本事項の確認に関するチュートリアル*](tutorial-command-line-app.md)" で再利用できます。 *CommandLoop.cs* ファイルでは、`CommandDeleteAllModels` 関数でこれを実行します。

このセクションの残りの部分では、モデルの削除を詳細に分割し、個々のモデルに対してその方法を示します。

##### <a name="before-deletion-deletion-requirements"></a>削除前: 削除の要件

一般的に、設定はいつでも削除できます。

例外は、`extends` リレーションシップまたはコンポーネントとして、他のモデルが依存しているモデルです。 たとえば、*ConferenceRoom* モデルが *Room* モデルを拡張し、コンポーネントとして *ACUnit* モデルを持っている場合、*ConferenceRoom* によってそれらの参照が削除されるまで、*Room* または *ACUnit* を削除することはできません。 

これを行うには、依存モデルを更新して依存関係を削除するか、依存モデルを完全に削除します。

##### <a name="during-deletion-deletion-process"></a>削除中: 削除プロセス

モデルが直ちに削除するための要件を満たしている場合でも、ツインが残されている場合に意図しない結果が生じないように、まずいくつかの手順を実行することをお勧めします。 プロセスの管理に役立つ手順を次に示します。
1. まず、モデルの使用を停止します
2. 使用停止前の最後の数分間に送信されたツイン作成要求をサービスが処理したことを確認するため、数分待ちます
3. モデル別にツインをクエリして、現在使用停止モデルを使用しているすべてのツインを表示します
4. 不要になった場合はツインを削除するか、必要に応じて新しいモデルにパッチします。 また、なにもせず、モデルを削除した後にモデルなしのツインにする選択もあります。 この状態の影響については、次のセクションを参照してください。
5. さらに数分待って、変更がすべて適用されたことを確実にします
6. モデルを削除します 

モデルを削除するには、次の呼び出しを使用します。
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>削除後: モデルなしのツイン

モデルを削除すると、モデルを使用していたすべてのデジタル ツインがモデルなしと見なされるようになります。 この状態のすべてのツインの一覧を表示できるクエリは存在しないことに注意してください。ただし、削除されたモデルによってツインにクエリを実行して、どのツインが影響を受けているかを把握することは*できます*。

ここでは、モデルを持たないツインで実行できることとできないことの概要を示します。

**可能**な操作:
* ツインのクエリ
* プロパティの読み取り
* 外部に対するリレーションシップの読み取り
* 外部からの関係の追加および削除 (つまり、他のツインはこのツイン*に対して*リレーションシップを形成できます)。
  - リレーションシップ定義内の `target` には、削除されたモデルの DTMI が引き続き反映されます。 ここでは、ターゲットが定義されていないリレーションシップを使用することもできます。
* リレーションシップの削除
* ツインを削除する

**不可能**なこと:
* 外部へのリレーションシップを編集します (つまり、*このツインから他のツインへのリレーションシップ*)
* プロパティの編集

##### <a name="after-deletion-re-uploading-a-model"></a>削除後: モデルの再アップロード

モデルを削除した後で、削除したものと同じ ID を持つ新しいモデルを後でアップロードすることを決定できます。 その場合は、次のようになります。
* ソリューション ストアの観点から見ると、これはまったく新しいモデルをアップロードすることと同じです。 古いバージョンがアップロードされたことをサービスは覚えていません。   
* 削除されたモデルを参照しているグラフに残りのツインがある場合は、孤立していない状態になります。このモデル ID は、新しい定義で再び有効になります。 ただし、モデルの新しい定義が削除されたモデル定義と異なる場合、これらのツインには、削除された定義と一致するプロパティとリレーションシップが含まれている可能性があります。これは、新しい定義では無効です。

Azure Digital Twins ではこの状態を防ぐことができないため、モデル定義の切り替えで有効なままになるように、ツインを適切にパッチするように注意してください。

## <a name="manage-models-with-cli"></a>CLI を使用してモデルを管理する。

モデルは、Azure Digital Twins CLI を使用して管理することもできます。 コマンドについては、"[*Azure Digital Twins CLI を使用する方法*](how-to-use-cli.md)" に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

モデルに基づいてデジタル ツインを作成して管理する方法を説明します。
* [*方法: Digital Twins を管理する*](how-to-manage-twin.md)