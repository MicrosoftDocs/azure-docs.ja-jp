---
title: Digital Twins モデル パーサーについて理解する | Microsoft Docs
description: 開発者は、DTDL パーサーを使用してモデルを検証する方法について理解する必要があります。
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331789"
---
# <a name="understand-the-digital-twins-model-parser"></a>Digital Twins モデル パーサーについて理解する

Digital Twins Definition Language (DTDL) については、[DTDL の仕様](https://github.com/Azure/opendigitaltwins-dtdl)に関する記事で説明されています。 ユーザーは、_Digital Twins モデル パーサー_ NuGet パッケージを使用して、複数のファイルで定義されているモデルを検証し、クエリを実行できます。

## <a name="install-the-dtdl-model-parser"></a>DTDL モデル パーサーをインストールする

このパーサーは、NuGet.org で次の ID と共に使用できます。[Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)。 パーサーをインストールするには、Visual Studio や `dotnet` CLI などで互換性のある NuGet パッケージ マネージャーを使用してください。

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> この記事の執筆時点では、パーサーのバージョンは `3.12.5` です。

## <a name="use-the-parser-to-validate-a-model"></a>パーサーを使用してモデルを検証する

1 つのモデルは、JSON ファイルに記述されている 1 つ以上のインターフェイスで構成されている場合があります。 パーサーを使用すると、特定のフォルダー内のすべてのファイルを読み込み、そのパーサーを使用して、ファイル間の参照を含めたすべてのファイルを全体的に検証することができます。

1. すべてのモデル コンテンツの一覧を含む `IEnumerable<string>` を作成します。

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. `ModelParser` をインスタンス化し、`ParseAsync` を呼び出します。

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. 検証エラーを確認します。 パーサーによってエラーが検出されると、エラーの一覧とともに `ParsingException` がスローされます。

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. `Model` を調べます。 検証が成功した場合は、モデルパーサー API を使用してモデルを検査できます。 次のコード スニペットは、解析されたすべてのモデルに対してどのように反復処理されるか、および既存のプロパティを示しています。

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>次のステップ

この記事で確認したモデル パーサー API を使用すると、DTDL モデルに依存するタスクを自動化または検証する多くのシナリオが可能になります。 たとえば、モデル内の情報から UI を動的に構築できます。
