---
title: チュートリアル:クライアント アプリをコーディングする
titleSuffix: Azure Digital Twins
description: .NET (C#) SDK を使用してクライアント アプリの最小限のコードを記述するチュートリアル。
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c000d48043a46ecdbdfee263cc5c8ce877f66b4b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923706"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>チュートリアル:Azure Digital Twins API を使用したコーディング

Azure Digital Twins を使用する開発者は、Azure Digital Twins サービスのインスタンスとのやり取りのために、クライアント アプリケーションを作成するのが一般的です。 この開発者向けのチュートリアルでは、[.NET 用 Azure IoT Digital Twins クライアント ライブラリ (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) を使用した、Azure Digital Twins サービスに対するプログラミングの概要を説明します。 C# コンソール クライアント アプリの作成手順を最初から順を追って説明します。

> [!div class="checklist"]
> * プロジェクトの設定
> * プロジェクト コードでの作業開始   
> * 完全なコード例
> * リソースをクリーンアップする
> * 次のステップ

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、セットアップとプロジェクトの作業にコマンド ラインを使用します。 そのため、任意のコード エディターを使用して演習を進めることができます。

開始するために必要なもの:
* 任意のコード エディター
* 開発マシンにインストールされた **.NET Core 3.1**。 「[.NET Core 3.1 のダウンロード](https://dotnet.microsoft.com/download/dotnet-core/3.1)」から、複数のプラットフォームに対応する .NET Core SDK のこのバージョンをダウンロードできます。

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>プロジェクトの設定

Azure Digital Twins インスタンスを使用する準備ができたら、クライアント アプリ プロジェクトの設定を開始します。 

ご使用のコンピューターでコマンド プロンプトまたはその他のコンソール ウィンドウを開き、このチュートリアルでの作業を格納する空のプロジェクト ディレクトリを作成します。 ディレクトリに任意の名前を指定します (たとえば、*DigitalTwinsCodeTutorial*)。

その新しいディレクトリに移動します。

プロジェクト ディレクトリに移動したら、空の .NET コンソール アプリ プロジェクトを作成します。 コマンド ウィンドウで次のコマンドを実行して、コンソール用の最小限の C# プロジェクトを作成します。

```cmd/sh
dotnet new console
```

これにより、コードの大部分を記述する *Program.cs* と呼ばれるものを含む、いくつかのファイルがこのディレクトリ内に作成されます。

次に、Azure Digital Twins を使用するために必要な 2 つの依存関係を追加します。

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity
```

最初の依存関係は、[.NET 用 Azure IoT Digital Twins クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)です。 2 つ目の依存関係では、Azure に対する認証を支援するツールが提供されます。

コマンド ウィンドウは、チュートリアル全体を通して使用するため、開いたままにしておきます。

## <a name="get-started-with-project-code"></a>プロジェクト コードでの作業開始

このセクションでは、Azure Digital Twins とやり取りする新しいアプリ プロジェクトのコードの記述を開始します。 説明するアクションは次のとおりです。
* サービスに対する認証
* モデルのアップロード
* エラーのキャッチ
* デジタル ツインの作成
* リレーションシップの作成
* デジタル ツインのクエリ

チュートリアルの最後にコード全体を示すセクションもあります。 これを参考にして、ご自分のプログラムを確認しながら進めることができます。

最初に、任意のコード エディターで *Program.cs* ファイルを開きます。 次のような最小限のコードのテンプレートが表示されます。

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

まず、コードの先頭に `using` 行をいくつか追加して、必要な依存関係を取得します。

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

次に、何らかの機能を記述するコードをこのファイルに追加します。 

### <a name="authenticate-against-the-service"></a>サービスに対する認証

アプリで最初に行う必要があるのは、Azure Digital Twins サービスに対して認証することです。 その後、SDK 関数にアクセスするためのサービス クライアント クラスを作成できます。

認証のためには、次の 3 つの情報が必要です。
* サブスクリプションの "*ディレクトリ (テナント) ID*"
* 前の手順で Azure Digital Twins インスタンスを設定したときに作成した "*アプリケーション (クライアント) ID*"
* Azure Digital Twins インスタンスの *hostName*

>[!TIP]
> "*ディレクトリ (テナント) ID*" が不明な場合は、[Azure Cloud Shell](https://shell.azure.com) で次のコマンドを実行することで確認できます。
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

*Program.cs* で、次のコードを "Hello, World!" の 出力行 (`Main` メソッド内) の下に貼り付けます。 `adtInstanceUrl` の値を Azure Digital Twins インスタンスの *hostName* に、`clientId` を "*アプリケーション ID*" に、`tenantId` を "*ディレクトリ ID*" に設定します。

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

ファイルを保存します。 

この例では、対話型のブラウザー資格情報を使用していることに注意してください。
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

この種類の資格情報を使用すると、ブラウザー ウィンドウが開き、Azure の資格情報を入力するように求められます。 

>[!NOTE]
> その他の種類の資格情報については、「[Microsoft ID プラットフォームの認証ライブラリ](../active-directory/develop/reference-v2-libraries.md)」のドキュメントを参照してください。

コマンド ウィンドウで、次のコマンドを使用してコードを実行します。 

```cmd/sh
dotnet run
```

これにより、最初の実行での依存関係が復元されてから、プログラムが実行されます。 
* エラーが発生しなかった場合は、プログラムで "*Service client created - ready to go*" が出力されます。
* このプロジェクトにはまだエラー処理がないため、何らかの問題が発生すると、コードによってスローされた例外が表示されます。

### <a name="upload-a-model"></a>モデルのアップロード

Azure Digital Twins には、組み込みのドメイン ボキャブラリはありません。 Azure Digital Twins で表すことができる環境内の要素の種類は、**モデル**を使用してご自分で定義します。 [モデル](concepts-models.md)は、オブジェクト指向プログラミング言語におけるクラスに似ています。つまりモデルは、[デジタル ツイン](concepts-twins-graph.md)のユーザー定義のテンプレートとなります。デジタル ツインは、そのテンプレートに従ってインスタンス化されることになります。 それらは、JSON に似た **Digital Twins Definition Language (DTDL)** と呼ばれる言語で記述されます。

Azure Digital Twins ソリューションを作成するにあたり最初にすべきことは、DTDL ファイル内に少なくとも 1 つのモデルを定義することです。

プロジェクトを作成したディレクトリに、*SampleModel.json* という名前の新しい *.json* ファイルを作成します。 次のファイル本文を貼り付けます。 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> このチュートリアルのために Visual Studio を使用している場合は、新しく作成した JSON ファイルを選択し、プロパティ インスペクターで *[出力ディレクトリにコピー]* プロパティを *[新しい場合はコピーする]* または *[常にコピーする]* に設定することができます。 これにより、チュートリアルの残りの部分で **F5** キーを使用してプログラムを実行するときに、Visual Studio の既定のパスで JSON ファイルが検出されるようになります。

> [!TIP] 
> あらゆる言語に対応した [DTDL Validator サンプル](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)でモデル ドキュメントをチェックし、DTDL が有効であることを確認できます。 これは DTDL パーサー ライブラリをベースに構築されています。詳細については、"[*モデルを解析および検証する方法*](how-to-parse-models.md)" に関するページを参照してください。

次に、先ほど作成したモデルを Azure Digital Twins インスタンスにアップロードするためのコードを *Program.cs* に追加します。

まず、いくつかの `using` ステートメントをファイルの先頭に追加します。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

次に、C# サービス SDK の非同期メソッドを使用する準備として、非同期実行を許可するように `Main` メソッドのシグネチャを変更します。 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> SDK では、すべての呼び出しの同期バージョンも提供されているため、`async` の使用は厳密には必須ではありません。 このチュートリアルでは、`async` の使用を実践しています。

次に、Azure Digital Twins サービスとやり取りするコードの最初の部分があります。 このコードによって、作成した DTDL ファイルがディスクから読み込まれ、Azure Digital Twins サービス インスタンスにアップロードされます。 

前の手順で追加した承認コードの下に、次のコードを貼り付けます。

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

コマンド ウィンドウで、次のコマンドを使用してプログラムを実行します。 

```cmd/sh
dotnet run
```
"Upload a model" が出力に記録されますが、モデルが正常にアップロードされたかどうかを示す出力はまだありません。

モデルが実際に正常にアップロードされているかどうかを示す出力ステートメントを追加するには、前のセクションの直後に次のコードを追加します。

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

この新しいコードをテストするためにプログラムをもう一度実行する前に、前回のプログラム実行時にモデルを既にアップロードしたことを思い出してください。 Azure Digital Twins では、同じモデルを 2 回アップロードすることはできません。そのため、プログラムを再実行すると、例外が表示されることを予想しておいてください。

ここで、コマンド ウィンドウで次のコマンドを使用してプログラムを再度実行します。

```cmd/sh
dotnet run
```

プログラムで例外がスローされます。 既にアップロード済みのモデルをアップロードしようとすると、サービスから REST API を介して "無効な要求" エラーが返されます。 その結果、今度は Azure Digital Twins クライアント SDK で、成功以外のすべてのサービス リターン コードに対して例外がスローされます。 

次のセクションでは、このような例外と、ご自分のコードでそれらを処理する方法について説明します。

### <a name="catch-errors"></a>エラーのキャッチ

プログラムのクラッシュを防ぐために、モデル アップロードのコードの周囲に例外コードを追加できます。 次のように、既存のクライアント呼び出し `client.CreateModelsAsync` を try/catch ハンドラー内にラップします。

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

この時点で、コマンド ウィンドウで `dotnet run` を使用してプログラムを実行すると、エラー コードが返されることがわかります。 次のような画面が出力されます。

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

この時点以降、このチュートリアルでは、サービス メソッドへのすべての呼び出しを try/catch ハンドラー内にラップします。

### <a name="create-digital-twins"></a>デジタル ツインの作成

これで、Azure Digital Twins にモデルがアップロードされたので、このモデル定義を使用して**デジタル ツイン**を作成できます。 [デジタル ツイン](concepts-twins-graph.md)はモデルのインスタンスであり、農場のセンサー、建物内の部屋、車内の照明など、対象となるビジネス環境内のエンティティを表します。 このセクションでは、先ほどアップロードしたモデルに基づいて、いくつかのデジタル ツインを作成します。

`System.Text.Json` の組み込みの .NET Json シリアライザーが必要になるため、先頭に新しい `using` ステートメントを追加します。

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

次に、`Main` メソッドの末尾に次のコードを追加し、このモデルに基づいて 3 つのデジタル ツインを作成して初期化します。

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 次に、プログラムの実行を再度繰り返します。 

最初の実行後にツインが既に存在していても、ツインを 2 回目に作成したときにエラーがスローされないことに注意してください。 モデルの作成とは異なり、ツインの作成では、REST レベルで *upsert* セマンティクスを使用して *PUT* 呼び出しを行います。 つまり、既に存在するツインを再作成しようとした場合、そのツインが置き換えられるだけです。 必然的にエラーにはなりません。

### <a name="create-relationships"></a>リレーションシップの作成

次に、作成したツイン間に**リレーションシップ**を作成することで、それらのツインを接続し、**ツイン グラフ**を形成することができます。 [ツイン グラフ](concepts-twins-graph.md)は、ご自分の環境全体を表すために使用されます。

リレーションシップを作成できるようにするには、SDK にあるリレーションシップの基本データ型を対象にした `using` ステートメントを追加します (追加済みの場合はスキップします)。
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

次に、新しい静的メソッドを `Program` クラスの `Main` メソッドの下に追加します。
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

`Main` メソッドの末尾に、`CreateRelationship` コードを呼び出す次のコードを追加します。
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。

同じ ID のものが既に存在する場合、Azure Digital Twins ではリレーションシップを作成できないことに注意してください。そのため、プログラムを複数回実行すると、リレーションシップの作成時に例外が表示されます。 このコードでは、例外をキャッチして無視します。 

### <a name="list-relationships"></a>リレーションシップの一覧表示

次に追加するコードを使用して、作成したリレーションシップの一覧を表示できます。

次の新しいメソッドを `Program` クラスに追加します。

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

`Main` メソッドの末尾に、`ListRelationships` コードを呼び出す次のコードを追加します。

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 作成したすべてのリレーションシップの一覧が表示されます。

### <a name="query-digital-twins"></a>デジタル ツインのクエリ

Azure Digital Twins の主な機能は、環境についての質問に答える[クエリ](concepts-query-language.md)をツイン グラフに対して容易に、かつ効率よく実行できることです。

このチュートリアルで追加するコードの最後のセクションでは、Azure Digital Twins インスタンスに対してクエリを実行します。 この例で使用するクエリでは、インスタンス内のすべてのデジタル ツインが返されます。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

コマンド ウィンドウで、`dotnet run` を使用してプログラムを実行します。 出力には、このインスタンスのすべてのデジタル ツインが表示されます。

## <a name="complete-code-example"></a>完全なコード例

このチュートリアルのこの時点で、Azure Digital Twins に対して基本的なアクションを実行できる完全なクライアント アプリができました。 参照用に、*Program.cs* 内のプログラムの完全なコードを次に示します。

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

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
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする
 
このチュートリアルで使用したインスタンスは、次に取り組む "[*サンプル クライアント アプリを使用した基本事項の確認に関するチュートリアル*](tutorial-command-line-app.md)" で再利用できます。 次のチュートリアルに進む場合は、ここで設定した Azure Digital Twins インスタンスを残しておくことができます。
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

最後に、ローカル コンピューター上に作成したプロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、.NET コンソール クライアント アプリケーションをゼロから作成しました。 このクライアント アプリのコードを記述して、Azure Digital Twins インスタンスに対する基本的なアクションを実行しました。

次のチュートリアルに進み、このようなサンプル クライアント アプリを使用して実行できることを確認してください。 

> [!div class="nextstepaction"]
> [*チュートリアル:サンプル クライアント アプリを使用した基本事項の確認*](tutorial-command-line-app.md)"