---
title: AutoRest を使用してカスタム言語の SDK を作成する
titleSuffix: Azure Digital Twins
description: AutoRest を使用して、公開されている SDK がない他の言語で Azure Digital Twins コードを書くためのカスタム言語 SDK を生成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561842"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>AutoRest を使用して Azure Digital Twins 用のカスタム言語 SDK を作成する

[公開されている Azure Digital Twins SDK](how-to-use-apis-sdks.md) がない言語を使用して Azure Digital Twins を操作する必要がある場合、この記事では、AutoRest を使用して、任意の言語で独自の SDK を生成する方法を説明します。 

この記事の例では、[データ プレーン SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) の作成方法を示していますが、このプロセスは[コントロール プレーン SDK](how-to-use-apis-sdks.md#overview-control-plane-apis) の生成にも使用できます。

## <a name="prerequisites"></a>前提条件

SDK を生成するには、まず、ご使用のローカル コンピューターで次のセットアップを完了する必要があります。
* [**AutoRest**](https://github.com/Azure/autorest)、バージョン 2.0.4413 (バージョン 3 は現在サポートされていません) をインストールします
* AutoRest を使用するための前提条件である [**Node.js**](https://nodejs.org) をインストールします
* [**Visual Studio**](https://visualstudio.microsoft.com/downloads/) をインストールします
* [データ プレーンの Swagger フォルダー](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)から最新の Azure Digital Twins **データ プレーン Swagger** (OpenAPI) ファイルと、それに付随する例を含むフォルダーをダウンロードします。 Swagger ファイルは、*digitaltwins.json* と呼ばれるファイルです。

>[!TIP]
> 代わりに **コントロール プレーン SDK** 作成するには、データ プレーンではなく、[コントロール プレーンの Swagger フォルダー](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/)の最新の **コントロール プレーン Swagger** (OpenAPI) ファイルを使用して、この記事の手順を行います。

お使いのマシンに上記の一覧にあるものがすべて備わったら、AutoRest を使用して SDK を作成することができます。

## <a name="create-the-sdk-using-autorest"></a>AutoRest を使用して SDK を作成する 

Node.js をインストールしたら、次のコマンドを実行して、必要なバージョンの AutoRest がインストールされていることを確認できます。
```cmd/sh
npm install -g autorest@2.0.4413
```

Azure Digital Twins Swagger ファイルに対して AutoRest を実行するには、次の手順に従います。
1. Azure Digital Twins Swagger ファイルとそれに付属する例のフォルダーを、作業ディレクトリにコピーします。
2. コマンド プロンプト ウィンドウを使用して、その作業ディレクトリに切り替えます。
3. 次のコマンドを使用して、AutoRest を実行します。 `<language>` プレースホルダーを、`python`、`java`、`go` などの任意の言語に置き換えてください。 (オプションの完全な一覧は [AutoRest の README](https://github.com/Azure/autorest) に記載されています。)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

その結果、作業ディレクトリに *DigitalTwinsApi* という名前の新しいフォルダーが表示されます。 生成された SDK ファイルの名前空間は *DigitalTwinsApi* になります。 この記事の残りの使用例では、その名前空間を使用し続けます。

AutoRest では、幅広い言語コード ジェネレーターがサポートされています。

## <a name="make-the-sdk-into-a-class-library"></a>SDK をクラス ライブラリにする

AutoRest によって生成されたファイルは、.NET ソリューションに直接追加することができます。 ただし、Azure Digital Twins SDK を複数の異なるプロジェクト (クライアント アプリ、Azure Functions アプリなど) に含めることが必要になる場合があります。 このため、生成されたファイルから個別のプロジェクト (.NET クラス ライブラリ) をビルドすると便利な場合があります。 その後、このクラス ライブラリ プロジェクトを、プロジェクト参照として複数のソリューションに含めることができます。

このセクションでは、SDK をクラス ライブラリとしてビルドする方法について説明します。これは独自のプロジェクトであり、他のプロジェクトに含めることができます。 これらの手順は **Visual Studio** に依存しています。

次に手順を示します。

1. クラス ライブラリ用の新しい Visual Studio ソリューションを作成します
2. プロジェクト名として *DigitalTwinsApi* を使用します
3. ソリューション エクスプローラーで、生成されたソリューションの *DigitalTwinsApi* プロジェクトを右クリックし、 *[追加] > [既存の項目...]* の順に選択します
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

## <a name="tips-for-using-the-sdk"></a>SDK を使用するためのヒント

このセクションでは、生成された SDK の使用に関する一般的な情報とガイドラインについて説明します。

### <a name="synchronous-and-asynchronous-calls"></a>同期呼び出しと非同期呼び出し

すべての SDK 関数に同期バージョンと非同期バージョンがあります。

### <a name="typed-and-untyped-data"></a>型指定されたデータと型指定のないデータ

通常は、REST API を呼び出すと厳密に型指定されたオブジェクトが返されます。 しかし、Azure Digital Twins ではユーザーがツイン用に独自のカスタム型を定義できるため、多くの Azure Digital Twins 呼び出しのために、返される静的データを事前に定義する方法はありません。 代わりに、API では、必要に応じて厳密に型指定されたラッパー型を返します。また、カスタム型のツイン データは Json.NET オブジェクトに含まれます (API シグネチャにデータ型 "object" が表示される場所で常に使用されます)。 これらのオブジェクトは、コード内で適切にキャストできます。

### <a name="error-handling"></a>エラー処理

SDK でエラーが発生するたびに (404 などの HTTP エラーを含む)、SDK によって例外がスローされます。 このため、すべての API 呼び出しを try/catch ブロック内に含めることが重要です。

ツインの追加を試み、この処理で発生するエラーをキャッチするコード スニペットを次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Paging

AutoRest では、SDK に対して 2 種類のページング パターンが生成されます。
* クエリ API を除くすべての API 向け
* クエリ API 向け

非クエリのページング パターンでは、次のサンプル メソッドは、Azure Digital Twins から外部へのリレーションシップの一覧をページ単位で取得する方法を示しています。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

2 番目のパターンは、クエリ API に対してのみ生成されます。 `continuationToken` が明示的に使用されます。

>[!TIP]
> ページを取得する主な理由は、クエリ API 呼び出しの[クエリ ユニットの料金](concepts-query-units.md)を計算するためです。

このパターンの使用例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>次のステップ

SDK を使用できるクライアント アプリの作成手順を確認します。
* [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)
