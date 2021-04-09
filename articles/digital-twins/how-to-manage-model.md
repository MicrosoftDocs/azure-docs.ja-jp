---
title: DTDL モデルの管理
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 内でモデルを作成、編集、および削除する方法を説明します。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 75911e91bb61b84d2e9315421f794739f2b5088b
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953699"
---
# <a name="manage-azure-digital-twins-models"></a>Azure Digital Twins のモデルを管理する

[**DigitalTwinsModels API**](/rest/api/digital-twins/dataplane/models)、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)、または [Azure Digital Twins CLI](how-to-use-cli.md) の使用について、Azure Digital Twins インスタンスで認識されている [モデル](concepts-models.md)を管理できます。 

管理操作には、モデルのアップロード、検証、取得、および削除が含まれます。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>モデルの管理方法

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>モデルを作成する

Azure Digital Twins のモデルは DTDL で記述され、 *.json* ファイルとして保存されます。 また、[DTDL 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)も用意されており、[Visual Studio Code](https://code.visualstudio.com/) に使用できます。これにより、構文の検証や、DTDL ドキュメントの作成を容易にするその他の機能が提供されます。

病院が部屋をデジタルで表現する例を考えてみましょう。 各部屋には、手の洗浄を監視するためのスマート ソープ ディスペンサーと、部屋の行き来を監視するセンサーが含まれています。

ソリューションに向けた最初の手順は、病院の状況を表すモデルを作成することです。 このシナリオの病室は、次のように記述できます。

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> これは、クライアントプロジェクトの一部としてアップロードされる、モデルが定義され保存されている .json ファイルのサンプル本文です。 一方、REST API 呼び出しは、上記のようなモデル定義の配列を取得します (これは .NET SDK の `IEnumerable<string>` にマップされています)。 そのため、REST API でこのモデルを直接使用するには、ブラケットで囲みます。

このモデルでは、病室の名前と一意の ID、および訪問者の数と手洗いの状態を表すプロパティを定義します (これらのカウンターはモーション センサーおよびスマート ソープ ディスペンサー から更新され、組み合わせて *手洗いパーセンテージ* プロパティを計算します)。 このモデルでは、*hasDevices* のリレーションシップも定義されています。これは、この *Room* モデルに基づいて [デジタル ツイン](concepts-twins-graph.md)を実際のデバイスに接続するために使用されます。

この方法に従うと、病院の病棟、ゾーン、または病院自体のモデルを定義することができます。

### <a name="validate-syntax"></a>構文を検証する

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>モデルのアップロード

モデルを作成したら、Azure Digital Twins インスタンスにアップロードできます。

モデルをアップロードする準備が整ったら、次のコード スニペットを使用できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

`CreateModels` メソッドが 1 つのトランザクションで複数のファイルを受け入れることを確認します。 次に例を示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

モデル ファイルには、1 つ以上のモデルを含めることができます。 この場合、モデルを JSON 配列に配置する必要があります。 次に例を示します。

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

アップロード時に、サービスによってモデル ファイルが検証されます。

## <a name="retrieve-models"></a>モデルの取得

Azure Digital Twins インスタンスに格納されているモデルを一覧表示したり、取得したりすることができます。 

そのためのオプションを次に示します。
* 単一のモデルの取得
* すべてのモデルの取得
* モデルのメタデータと依存関係を取得する

呼び出し例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

モデルを取得するための API 呼び出しはすべて `DigitalTwinsModelData` オブジェクトを返します。 `DigitalTwinsModelData` には、名前、DTMI、モデルの作成日など、Azure Digital Twins インスタンスに格納されているモデルに関するメタデータが含まれます。 `DigitalTwinsModelData` オブジェクトには、必要に応じてモデル自体を含めることもできます。 パラメーターによっては、取得呼び出しを使用して、メタデータのみ (これは、使用可能なツールの UI の一覧やモデル全体を表示するシナリオで役に立ちます)、またはモデル全体を取得できます。

`RetrieveModelWithDependencies` 呼び出しでは、要求されたモデルだけでなく、要求されたモデルが依存しているすべてのモデルも返されます。

モデルは、必ずしもアップロードされたドキュメント形式では返されません。 Azure Digital Twins は、返される形式が意味的に同等であることだけを保証します。 

## <a name="update-models"></a>モデルを更新する

モデルが Azure Digital Twins インスタンスにアップロードされると、モデル インターフェイス全体が不変になります。 つまり、従来のモデルの「編集」はありません。 また、Azure Digital Twins では、同じモデルを再アップロードすることはできません。

代わりに、`displayName` や `description` の更新など、モデルを変更する場合には、**新しいバージョン** のモデルをアップロードする方法があります。 

### <a name="model-versioning"></a>モデルのバージョン管理

既存のモデルの新しいバージョンを作成するには、元のモデルの DTDL から開始します。 変更するフィールドを更新、追加、または削除します。

次に、モデルの `id` フィールドを更新して、モデルの新しいバージョンとしてマークを付けます。 モデル ID の最後のセクション (`;` の後) は、モデル番号を表します。 ここで、このモデルの新たに更新されたバージョンであることを示すには、`id` 値の最後にある数値を現在のバージョン番号よりも大きい数値に増やします。

たとえば、以前のモデル ID が次の場合:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

このモデルのバージョン 2 は次のとおりです。

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

次に、このモデルの新しいバージョンをインスタンスにアップロードします。 

このバージョンのモデルは、インスタンスで使用可能となり、デジタル ツインに使用できるようになります。 これによって、以前のバージョンのモデルが上書きされることは **ない** ため、[それを削除する](#remove-models)までは複数のバージョンのモデルがインスタンス内に共存します。

### <a name="impact-on-twins"></a>ツインへの影響

新しいツインを作成する場合、新しいモデル バージョンと古いモデル バージョンが共存するため、新しいツインでは、新しいバージョンのモデルまたは古いバージョンのいずれかを使用できます。

また、モデルの新しいバージョンをアップロードしても、既存のツインに自動的に影響を与えることはありません。 既存のツインは、古いモデル バージョンのインスタンスに残ります。

これらの既存のツインは、ファイルの部分置換を実行することで、新しいモデル バージョンに更新することができます。詳細な説明については、「[*デジタル ツインのモデルを更新する*](how-to-manage-twin.md#update-a-digital-twins-model)」セクション、 *「デジタル ツインを管理する」方法* を参照してください。 ファイルの部分置換を実行する場合、両方の **モデル ID** を新しいバージョンに更新し、**ツインで変更する必要があるすべてのフィールドを新しいモデルに準拠させる** 必要があります。

## <a name="remove-models"></a>モデルの削除

モデルは、次の 2 つの方法のいずれかでサービスから削除することもできます。
* **使用停止**:モデルが使用停止になると、新しいデジタル ツインを作成するために使用できなくなります。 このモデルを既に使用している既存のデジタル ツインは影響を受けないため、プロパティの変更やリレーションシップの追加や削除などの操作で更新できます。
* **削除**:これにより、ソリューションからモデルが完全に削除されます。 このモデルを使用していたすべてのツインは、有効なモデルに関連付けられなくなったため、モデルがまったくないかのように扱われます。 これらのツインは引き続き読み取ることができますが、別のモデルに再割り当てされるまで、更新を行うことはできません。

これらは別の機能であり、互いに影響を与えることはありませんが、一緒に使用してモデルを段階的に削除することもできます。 

### <a name="decommissioning"></a>使用停止

モデルの使用を停止するコードを次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

モデルの使用停止状態は、モデル取得 API によって返された `ModelData` レコードに含まれます。

### <a name="deletion"></a>削除

インスタンス内のすべてのモデルを一度に削除することも、個別に実行することもできます。

すべてのモデルを削除する方法の例について、ダウンロードできるサンプル アプリは、[*サンプル クライアント アプリを使用した基本事項の確認に関するチュートリアル*](tutorial-command-line-app.md)" で再利用できます。 *CommandLoop.cs* ファイルでは、`CommandDeleteAllModels` 関数でこれを実行します。

このセクションの残りの部分では、モデルの削除を詳細に分割し、個々のモデルに対してその方法を示します。

#### <a name="before-deletion-deletion-requirements"></a>削除前: 削除の要件

一般的に、設定はいつでも削除できます。

例外は、`extends` リレーションシップまたはコンポーネントとして、他のモデルが依存しているモデルです。 たとえば、*ConferenceRoom* モデルが *Room* モデルを拡張し、コンポーネントとして *ACUnit* モデルを持っている場合、*ConferenceRoom* によってそれらの参照が削除されるまで、*Room* または *ACUnit* を削除することはできません。 

これを行うには、依存モデルを更新して依存関係を削除するか、依存モデルを完全に削除します。

#### <a name="during-deletion-deletion-process"></a>削除中: 削除プロセス

モデルが直ちに削除するための要件を満たしている場合でも、ツインが残されている場合に意図しない結果が生じないように、まずいくつかの手順を実行することをお勧めします。 プロセスの管理に役立つ手順を次に示します。
1. まず、モデルの使用を停止します
2. 使用停止前の最後の数分間に送信されたツイン作成要求をサービスが処理したことを確認するため、数分待ちます
3. モデル別にツインをクエリして、現在使用停止モデルを使用しているすべてのツインを表示します
4. 不要になった場合はツインを削除するか、必要に応じて新しいモデルにパッチします。 また、なにもせず、モデルを削除した後にモデルなしのツインにする選択もあります。 この状態の影響については、次のセクションを参照してください。
5. さらに数分待って、変更がすべて適用されたことを確実にします
6. モデルを削除します 

モデルを削除するには、次の呼び出しを使用します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>削除後: モデルなしのツイン

モデルを削除すると、モデルを使用していたすべてのデジタル ツインがモデルなしと見なされるようになります。 この状態のすべてのツインの一覧を表示できるクエリは存在しないことに注意してください。ただし、削除されたモデルによってツインにクエリを実行して、どのツインが影響を受けているかを把握することは *できます*。

ここでは、モデルを持たないツインで実行できることとできないことの概要を示します。

**可能** な操作:
* ツインのクエリ
* プロパティの読み取り
* 外部に対するリレーションシップの読み取り
* 外部からの関係の追加および削除 (つまり、他のツインはこのツイン *に対して* リレーションシップを形成できます)。
  - リレーションシップ定義内の `target` には、削除されたモデルの DTMI が引き続き反映されます。 ここでは、ターゲットが定義されていないリレーションシップを使用することもできます。      
* リレーションシップの削除
* ツインを削除する

**不可能** なこと:
* 外部へのリレーションシップを編集します (つまり、*このツインから他のツインへのリレーションシップ*)
* プロパティの編集

#### <a name="after-deletion-re-uploading-a-model"></a>削除後: モデルの再アップロード

モデルを削除した後で、削除したものと同じ ID を持つ新しいモデルを後でアップロードすることを決定できます。 その場合は、次のようになります。
* ソリューション ストアの観点から見ると、これはまったく新しいモデルをアップロードすることと同じです。 古いバージョンがアップロードされたことをサービスは覚えていません。   
* 削除されたモデルを参照しているグラフに残りのツインがある場合は、孤立していない状態になります。このモデル ID は、新しい定義で再び有効になります。 ただし、モデルの新しい定義が削除されたモデル定義と異なる場合、これらのツインには、削除された定義と一致するプロパティとリレーションシップが含まれている可能性があります。これは、新しい定義では無効です。

Azure Digital Twins ではこの状態を防ぐことができないため、モデル定義の切り替えで有効なままになるように、ツインを適切にパッチするように注意してください。

## <a name="next-steps"></a>次のステップ

モデルに基づいてデジタル ツインを作成して管理する方法を説明します。
* [*方法: Digital Twins を管理する*](how-to-manage-twin.md)