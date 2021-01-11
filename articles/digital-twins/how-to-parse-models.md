---
title: モデルを解析および検証する
titleSuffix: Azure Digital Twins
description: パーサー ライブラリを使用して DTDL モデルを解析する方法について学習します。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 69b52be3a3eca2ab48ed09f6401780ea033f223c
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723981"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>DTDL パーサー ライブラリを使用してモデルを解析および検証する

Azure Digital Twins の[モデル](concepts-models.md)は、JSON-LD ベースの Digital Twins Definition language (DTDL) を使用して定義されます。 **モデルは、Azure Digital Twins インスタンスにアップロードする前に、オフラインで検証することをお勧めします。**

NuGet には、これを行うのに役立つ .NET クライアント側の DTDL 解析ライブラリが用意されています。[**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

パーサー ライブラリを直接 C# コードで使用することも、パーサー ライブラリに構築された、言語に依存しないコード サンプル プロジェクトを使用することもできます。[**DTDL 検証ツール サンプル**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)

## <a name="use-the-dtdl-validator-sample"></a>DTDL 検証ツール サンプルを使用する

[**DTDL 検証ツール**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator)は、モデル ドキュメントを検証して DTDL が有効であることを確認できるサンプル プロジェクトです。 これは、.NET パーサー ライブラリに基づいて構築されており、言語に依存しません。 これは、サンプル リンクの *[ZIP のダウンロード]* ボタンで取得できます。

ソース コードには、パーサー ライブラリを使用する方法の例が示されています。 この検証ツール サンプルは、DTDL ファイルのディレクトリ ツリーを検証するコマンド ライン ユーティリティとして使用できます。 また、対話モードも用意されています。

サンプルを自己完結型の実行可能ファイルにパッケージ化する方法については、DTDL 検証ツール サンプルのフォルダー内の *readme.md* ファイルを参照してください。

自己完結型のパッケージを作成し、実行可能ファイルをパスに追加したら、お使いのマシンのコンソールで次のコマンドを使用して、検証コントロールを実行できます。

```cmd/sh
DTDLValidator
```

既定のオプションでは、サンプルは現在のディレクトリとすべてのサブディレクトリ内で `*.json` ファイルを検索します。 また、次のオプションを追加すると、指定されたディレクトリおよびすべてのサブディレクトリ内で拡張子が *.dtdl* のファイルを検索できます。

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

サンプルに `-i` オプションを追加すると、対話モードに切り替えることができます。

```cmd/sh
DTDLValidator -i
```

このサンプルの詳細については、ソース コードを参照するか、`DTDLValidator --help` を実行してください。

## <a name="use-the-net-parser-library"></a>.NET パーサー ライブラリを使用する 

[**Microsoft.Azure.DigitalTwins.Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) ライブラリは、DTDL 定義へのモデル アクセスを備えており、本質的に DTDL の C# リフレクションと同等の機能を果たします。 このライブラリは、特にビジュアル エディターまたはテキスト エディターで DTDL 検証を行う場合に、すべての [Azure Digital Twins SDK](how-to-use-apis-sdks.md) から独立して使用できます。 これは、モデル定義ファイルをサービスにアップロードする前にその有効性を確認するのに役立ちます。

パーサー ライブラリを使用するには、DTDL ドキュメントのセットをライブラリに提供します。 通常、これらのモデル ドキュメントはサービスから取得しますが、クライアントが最初にそれらをサービスにアップロードする責任がある場合は、それらをローカルで使用できるようにすることもできます。 

パーサーを使用するための一般的なワークフローを次に示します。
1. サービスから一部またはすべての DTDL ドキュメントを取得します。
2. 返されたメモリ内 DTDL ドキュメントをパーサーに渡します。
3. パーサーが、渡されたドキュメントのセットを検証し、詳細なエラー情報を返します。 この機能は、エディターのシナリオで役立ちます。
4. パーサー API シリーズを使用して、ドキュメント セットに含まれているモデルの分析を続行します。 

パーサーの機能は次のとおりです。
* 実装されているすべてのモデル インターフェイス (インターフェイスの `extends` セクションの内容) を取得します。
* モデルで宣言されているすべてのプロパティ、テレメトリ、コマンド、コンポーネント、およびリレーションシップを取得します。 また、このコマンドでは、これらの定義に含まれるすべてのメタデータが取得され、継承 (`extends` セクション) が考慮されます。
* すべての複雑なモデル定義を取得します。
* モデルを別のモデルから割り当てることができるかどうかを判断します。

> [!NOTE]
> [IoT プラグ アンド プレイ (PnP)](../iot-pnp/overview-iot-plug-and-play.md) デバイスでは、その機能を記述するために小さな構文バリアントを使用します。 この構文バリアントは、Azure Digital Twins で使用される DTDL の意味的に互換性のあるサブセットです。 パーサー ライブラリを使用する場合は、デジタル ツインの DTDL の作成に使用された構文バリアントを把握している必要はありません。 パーサーは、既定で、常に PnP と Azure Digital Twins の両方の構文に対して同じモデルを返します。

### <a name="code-with-the-parser-library"></a>パーサー ライブラリを使用してコードを書く

パーサー ライブラリを直接使用して、独自のアプリケーションでモデルを検証したり、動的なモデル駆動型 UI、ダッシュボード、レポートを生成したりすることができます。

以下のパーサー コードの例をサポートするには、Azure Digital Twins インスタンスに定義されているいくつかのモデルを検討してください。

> [!TIP] 
> `dtmi:com:contoso:coffeeMaker` モデルでは "*機能モデル*" 構文を使用しています。これは、そのモデルを公開する PnP デバイスに接続することによってこれがサービスにインストールされたことを意味します。

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

次のコードは、パーサー ライブラリを使用してこれらの定義を C# で反映する方法の例を示しています。

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

モデルの作成が完了したら、DigitalTwinsModels API シリーズを使用してモデルをアップロードする方法 (およびその他の管理操作を実行する方法) を確認してください。
* [*方法: カスタム モデルを管理する*](how-to-manage-model.md)"