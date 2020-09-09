---
title: AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する
titleSuffix: Azure Digital Twins
description: C# 以外の言語で Azure Digital Twins を使用するために、カスタム SDK を生成する方法を確認します。
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 3cf14ce3e8ef9b1d783191fe6c01c5e311d57786
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855949"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>AutoRest を使用して Azure Digital Twins 用のカスタム SDK を作成する

現時点では、Azure Digital Twins API と対話するために公開されている唯一のデータ プレーン SDK は、.NET (C#) 用のものです。 .NET SDK と一般的な API については、「[*Azure Digital Twins の API および SDK を使用する*](how-to-use-apis-sdks.md)」を参照してください。 別の言語で作業している場合、この記事では、AutoRest を使用して、任意の言語で独自のデータ プレーン SDK を生成する方法について説明します。

>[!NOTE]
> 必要に応じて、AutoRest を使用して、コントロール プレーン SDK を生成することもできます。 これを行うには、データ プレーンではなく[コントロール プレーンの Swagger (OpenAPI) ファイル](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview)を使用して、この記事の手順を実行します。

## <a name="set-up-your-machine"></a>コンピューターをセットアップする

SDK を生成するには、次のものが必要です。
* [AutoRest](https://github.com/Azure/autorest)、バージョン 2.0.4413 (バージョン 3 は現在サポートされていません)
* [Node.js](https://nodejs.org) (AutoRest の前提条件として)
* Azure Digital Twins [データ プレーン Swagger (OpenAPI) ファイル](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins/preview/2020-05-31-preview) (*digitaltwins.json* という名前)、および例を含む付属のフォルダー。 ローカル コンピューターに Swagger ファイルと例のフォルダーをダウンロードします。

お使いのコンピューターに上記の一覧にあるものがすべて備わったら、AutoRest を使用して SDK を作成することができます。

## <a name="create-the-sdk-with-autorest"></a>AutoRest を使用して SDK を作成する 

Node.js がインストールされている場合は、次のコマンドを実行して、適切なバージョンの AutoRest がインストールされていることを確認できます。
```cmd/sh
npm install -g autorest@2.0.4413
```

Azure Digital Twins Swagger ファイルに対して AutoRest を実行するには、次の手順に従います。
1. Azure Digital Twins Swagger ファイルとそれに付属する例のフォルダーを、作業ディレクトリにコピーします。
2. コマンド プロンプト ウィンドウを使用して、その作業ディレクトリに切り替えます。
3. 次のコマンドを使用して、AutoRest を実行します。 `<language>` プレースホルダーを、`python`、`java`、`go` などの任意の言語に置き換えてください。 (オプションの完全な一覧は [AutoRest の README](https://github.com/Azure/autorest) に記載されています。)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

その結果、作業ディレクトリに *ADTApi* という名前の新しいフォルダーが表示されます。 生成された SDK ファイルの名前空間は *ADTApi* になります。 この記事の残りの使用例では、その名前空間を使用し続けます。

AutoRest では、幅広い言語コード ジェネレーターがサポートされています。

## <a name="add-the-sdk-to-a-visual-studio-project"></a>SDK を Visual Studio プロジェクトに追加する

AutoRest によって生成されたファイルは、.NET ソリューションに直接追加することができます。 ただし、Azure Digital Twins SDK を複数の異なるプロジェクト (クライアント アプリ、Azure Functions アプリなど) に含めることが必要になる場合があります。 このため、生成されたファイルから個別のプロジェクト (.NET クラス ライブラリ) をビルドすると便利な場合があります。 その後、このクラス ライブラリ プロジェクトを、プロジェクト参照として複数のソリューションに含めることができます。

このセクションでは、SDK をクラス ライブラリとしてビルドする方法について説明します。これは独自のプロジェクトであり、他のプロジェクトに含めることができます。 以下の手順は **Visual Studio** に依存しています ([こちら](https://visualstudio.microsoft.com/downloads/)から最新バージョンをインストールできます)。

次に手順を示します。

1. クラス ライブラリ用の新しい Visual Studio ソリューションを作成します
2. プロジェクト名として「*ADTApi*」を使用します
3. ソリューション エクスプローラーで、生成されたソリューションの *ADTApi* プロジェクトを右クリックし、 *[追加] > [既存の項目...]* の順に選択します
4. SDK を生成したフォルダーを検索し、ルート レベルにあるファイルを選択します
5. [OK] を押します
6. プロジェクトにフォルダーを追加します (ソリューション エクスプローラーでプロジェクトを右クリックし、 *[追加] > [新しいフォルダー]* の順に選択)
7. フォルダーに「*Models*」という名前を付けます
8. ソリューション エクスプローラーで *Models* フォルダーを右クリックし、 *[追加] > [既存の項目...]* の順に選択します
9. 生成された SDK の *Models* フォルダー内のファイルを選択し、[OK] を押します

SDK を正常にビルドするには、プロジェクトに次の参照が必要です。
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

これらを追加するには、 *[ツール] > [NuGet パッケージ マネージャー] > [ソリューションの NuGet パッケージの管理...]* の順に選択します。

1. パネルで、 *[参照]* タブが選択されていることを確認します
2. 「*Microsoft.Rest*」を検索します
3. `ClientRuntime` パッケージと `ClientRuntime.Azure` パッケージを選択し、ソリューションに追加します

これで、プロジェクトをビルドし、作成した Azure Digital Twins アプリケーションにプロジェクト参照として含めることができます。

## <a name="general-guidelines-for-generated-sdks"></a>生成された SDK に関する一般的なガイドライン

このセクションでは、生成された SDK の使用に関する一般的な情報とガイドラインについて説明します。

### <a name="synchronous-and-asynchronous-calls"></a>同期呼び出しと非同期呼び出し

すべての SDK 関数に同期バージョンと非同期バージョンがあります。

### <a name="typed-and-untyped-data"></a>型指定されたデータと型指定のないデータ

通常は、REST API を呼び出すと厳密に型指定されたオブジェクトが返されます。 しかし、Azure Digital Twins ではユーザーがツイン用に独自のカスタム型を定義できるため、多くの Azure Digital Twins 呼び出しのために、返される静的データを事前に定義する方法はありません。 代わりに、API では、必要に応じて厳密に型指定されたラッパー型を返します。また、カスタム型のツイン データは Json.NET オブジェクトに含まれます (API シグネチャにデータ型 "object" が表示される場所で常に使用されます)。 これらのオブジェクトは、コード内で適切にキャストできます。

### <a name="error-handling"></a>エラー処理

SDK でエラーが発生するたびに (404 などの HTTP エラーを含む)、SDK によって例外がスローされます。 このため、すべての API 呼び出しを try/catch ブロック内に含めることが重要です。

ツインの追加を試み、この処理で発生するエラーをキャッチするコード スニペットを次に示します。

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Paging

AutoRest では、SDK に対して 2 種類のページング パターンが生成されます。
* クエリ API を除くすべての API 向け
* クエリ API 向け

クエリ以外のページング パターンでは、それぞれの呼び出しに 2 つのバージョンがあります。
* 最初の呼び出しを行うバージョン (`DigitalTwins.ListEdges()` など)
* 次のページを取得するためのバージョン。 これらの呼び出しには、"Next" というサフィックスが付いています (`DigitalTwins.ListEdgesNext()`)

次のコード スニペットは、Azure Digital Twins から外部へのリレーションシップの一覧をページ単位で取得する方法を示しています。
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

2 番目のパターンは、クエリ API に対してのみ生成されます。 `continuationToken` が明示的に使用されます。

このパターンの使用例を次に示します。

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>次のステップ

SDK を使用できるクライアント アプリの作成手順を確認します。
* [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)