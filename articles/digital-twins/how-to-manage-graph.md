---
title: リレーションシップを使ってツイン グラフを管理する
titleSuffix: Azure Digital Twins
description: リレーションシップを使って接続することでデジタル ツインのグラフを管理する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433320"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>リレーションシップを使ってデジタル ツインのグラフを管理する

Azure Digital Twins の中核は、環境全体を表す[ツイン グラフ](concepts-twins-graph.md)です。 ツイン グラフは、**リレーションシップ** を介して接続された個々のデジタル ツインで構成されています。 

機能する [Azure Digital Twins インスタンス](how-to-set-up-instance-portal.md)があり、クライアント アプリで [認証](how-to-authenticate-client.md)コードを設定すると、[**DigitalTwins API**](/rest/api/digital-twins/dataplane/twins) を使用して Azure Digital Twins インスタンス内のデジタル ツインとそのリレーションシップを作成、変更、削除することができます。 [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)、または [Azure Digital Twins CLI](how-to-use-cli.md) を使用することもできます。

この記事では、リレーションシップとグラフ全体の管理に焦点を当てます。個々のデジタル ツインの操作については、[ *「デジタル ツインを管理する」方法*](how-to-manage-twin.md)を参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>グラフの管理方法

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

また、Azure Digital Twins Explorer サンプルを使用して、グラフに変更を加えることもできます。このサンプルでは、ツインとグラフを視覚化し、バックグラウンドで SDK を利用できます。 次のセクションでは、このサンプルについて詳しく説明します。

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>リレーションシップの作成

リレーションシップには、さまざまなデジタル ツインが相互にどのように接続されるかが記述され、それによってツイン グラフの基礎が形成されます。

リレーションシップは、`CreateOrReplaceRelationshipAsync()` 呼び出しを使用して作成されます。 

リレーションシップを作成するには、以下を指定する必要があります。
* ソース ツイン ID (以下のコード サンプルの `srcId`):リレーションシップが発生したツインの ID。
* ターゲット ツイン ID (以下のコード サンプルの `targetId`):リレーションシップの対象のツインの ID。
* リレーションシップ名 (以下のコード サンプルの `relName`):_contains_ のような一般的な種類のリレーションシップ。
* リレーションシップ ID (以下のコード サンプルの `relId`):このリレーションシップの具体的な名前 (_Relationship1_ など)。

リレーションシップ ID は、指定されたソース ツイン内で一意である必要があります。 グローバルに一意である必要はありません。
たとえば、ツイン *foo* の場合、それぞれのリレーションシップ ID は一意である必要があります。 ただし、別のツインである *bar* は、*foo* リレーションシップの同じ ID と一致する発信リレーションシップを持つことができます。

次のコード サンプルは、Azure Digital Twins インスタンスにリレーションシップを作成する方法を示しています。 より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

これで、このカスタム関数を呼び出して、このような "_包含_" 関係を作成できます。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

複数のリレーションシップを作成する場合は、同じメソッドの呼び出しを繰り返して、異なるリレーションシップの種類を引数に渡すことができます。 

ヘルパー クラス `BasicRelationship` の詳細については、[ *「Azure Digital Twins の API および SDK を使用する」方法*](how-to-use-apis-sdks.md#serialization-helpers)を参照してください。

### <a name="create-multiple-relationships-between-twins"></a>ツイン間でリレーションシップの作成

リレーションシップは次のいずれかに分類できます。 

* 発信リレーションシップ:他のツインに接続するために外側を指す、このツインに属するリレーションシップ。 `GetRelationshipsAsync()` メソッドは、ツインの発信リレーションシップを取得するために使用されます。
* 受信リレーションシップ:"受信" リンクを作成するためにこのツインを指す、他のツインに属するリレーションシップ。 `GetIncomingRelationshipsAsync()` メソッドは、ツインの受信リレーションシップを取得するために使用されます。

2 つのツイン間のリレーションシップの数には制限がありません。ツイン間には、必要な数のリレーションシップを含めることができます。 

これは、2 つのツイン間に、さまざまな種類のリレーションシップを同時に複数表すことができることを意味します。 たとえば、"*ツイン A*" と "*ツイン B*" の間に、"*格納された*" リレーションシップと "*製造された*" リレーションシップを含めることができます。

必要に応じて、同じ 2 つのツイン間に、同じ種類のリレーションシップのインスタンスを複数作成することもできます。 この例では、リレーションシップのリレーションシップ ID が異なる限り、"*ツイン A*" は "*ツイン B*" と 2 つの異なる "*格納された*" リレーションシップを持つことができます。

## <a name="list-relationships"></a>リレーションシップの一覧表示

グラフ内の特定のツインの "**発信**" リレーションシップの一覧にアクセスするには、次のように `GetRelationships()` メソッドを使用できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

これによって、呼び出しの同期または非同期のバージョンのどちらを使用するかに応じて、`Azure.Pageable<T>` または `Azure.AsyncPageable<T>` が返されます。

リレーションシップの一覧を取得する例を次に示します。 より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

これで、このカスタム メソッドを呼び出して、このようなツインの発信リレーションシップを確認できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

取得したリレーションシップを使用して、グラフ内の他のツインに移動できます。 これを行うには、返されたリレーションシップから `target` フィールドを読み取り、それを `GetDigitalTwin()` への次の呼び出しの ID として使用します。

### <a name="find-incoming-relationships-to-a-digital-twin"></a>デジタル ツインとの受信リレーションシップを見つける

Azure Digital Twins には、特定のツインとのすべての **受信** リレーションシップを検索する API もあります。 これは、逆方向のナビゲーションの場合やツインを削除するときに便利です。

>[!NOTE]
> `IncomingRelationship` の呼び出しからは、リレーションシップ全体は返されません。 `IncomingRelationship` クラスの詳細については、その[リファレンス ドキュメント](/dotnet/api/azure.digitaltwins.core.incomingrelationship)を参照してください。

前のセクションのコード サンプルは、ツインからの発信リレーションシップの検索に重点を置いていました。 次の例は同じような構造になってますが、代わりにツインへの "*受信*" リレーションシップを検索します。 この例でも、より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

これで、このカスタム メソッドを呼び出して、このようなツインの受信リレーションシップを確認できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>ツインのすべてのプロパティとリレーションシップを一覧表示する

ツインへの発信と受信のリレーションシップを一覧表示する上記のメソッドを使用すると、ツインのプロパティとその両方の種類のリレーションシップを含む完全なツインの情報を出力するメソッドを作成できます。 この目的のために上記のカスタム メソッドを組み合わせる方法を示すカスタム メソッドの例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

これで、このようにこのカスタム関数を呼び出すことができます。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>リレーションシップの更新

リレーションシップは、`UpdateRelationship` メソッドを使用して更新します。 

>[!NOTE]
>このメソッドは、リレーションシップの **プロパティ** を更新するためのものです。 リレーションシップのソース ツインまたはターゲット ツインを変更する必要がある場合は、[リレーションシップを削除](#delete-relationships)し、新しいツインを使用して[再作成](#create-relationships)する必要があります。

クライアント呼び出しに必要なパラメーターは、ソース ツインの ID (リレーションシップが発生したツイン)、更新するリレーションシップの ID、更新するプロパティと新しい値を含む [JSON Patch](http://jsonpatch.com/) ドキュメントです。

このメソッドの使用方法を示すサンプル コードを次に示します。 この例では、より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

プロパティを更新するための情報を含む JSON Patch ドキュメントを渡して、このカスタム メソッドを呼び出す例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>リレーションシップの削除

最初のパラメーターには、ソース ツイン (リレーションシップの発生元のツイン) を指定します。 もう一方のパラメーターはリレーションシップ ID です。 リレーションシップ ID はツインのスコープ内でのみ一意であるため、ツイン ID とリレーションシップ ID の両方が必要です。

このメソッドの使用方法を示すサンプル コードを次に示します。 この例では、より大きなプログラムのコンテキストで使用される可能性のあるカスタム メソッド内で SDK 呼び出し (強調表示) を使用しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

これで、このカスタム メソッドを呼び出して、このようなリレーションシップを削除できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>実行可能なツインのグラフのサンプル

次の実行可能なコード スニペットでは、この記事のリレーションシップ操作を使用して、デジタル ツインとリレーションシップからツイン グラフを作成します。

### <a name="set-up-the-runnable-sample"></a>実行可能なサンプルを設定する

このスニペットでは、[*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) と [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) モデル定義を使用しています。「[*チュートリアル:サンプル クライアント アプリを使用して Azure Digital Twins を試す*](tutorial-command-line-app.md)」のものです。 これらのリンクを使用してファイルに直接移動するか、[こちら](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)の完全なエンドツーエンドのサンプル プロジェクトの一部としてダウンロードすることができます。 

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

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

上記のプログラムのコンソール出力は次のようになります。 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="ツインの詳細、ツインの受信および発信リレーションシップが表示されているコンソール出力。" lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> ツイン グラフは、ツイン間にリレーションシップを作成する概念です。 ツイン グラフの視覚的表現を表示する場合は、この記事の "[*視覚化*](how-to-manage-graph.md#visualization)" に関するセクションを参照してください。 

## <a name="create-graph-from-a-csv-file"></a>CSV ファイルからグラフを作成する

実際の使用例では、多くの場合、別のデータベース、または場合によってはスプレッドシートまたは CSV ファイルに格納されたデータからツイン階層が作成されます。 このセクションでは、CSV ファイルからデータを読み取り、そこからツイン グラフを作成する方法について説明します。

デジタル ツインのセットとリレーションシップが説明されている次のデータ テーブルがあるとします。

|  モデル ID    | ツイン ID (一意である必要があります) | [リレーションシップ名]  | ターゲット ツイン ID  | ツインの init データ |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1    | Floor1 | contains | Room1 | |
| dtmi:example:Floor;1    | Floor0 | contains | Room0 | |
| dtmi:example:Room;1    | Room1 | | | {"Temperature":80} |
| dtmi:example:Room;1    | Room0 | | | {"Temperature":70} |

このデータを Azure Digital Twins に取り込む方法の 1 つとして、テーブルを CSV ファイルに変換してコードを記述し、ファイルをコマンドに解釈して、ツインやリレーションシップを作成するという方法があります。 次のコード サンプルは、CSV ファイルからのデータの読み取り、および Azure Digital Twins でのツイン グラフの作成を示しています。

次のコードでは、CSV ファイルは *data.csv* と呼ばれ、Azure Digital Twins インスタンスの **hostname** を表すプレースホルダーがあります。 また、このサンプルでは、プロジェクトに追加できるパッケージをいくつか使用すると、このプロセスを実行しやすくなります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>次のステップ

Azure Digital Twins ツイン グラフのクエリについて確認します。
* "[*概念: クエリ言語*](concepts-query-language.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)