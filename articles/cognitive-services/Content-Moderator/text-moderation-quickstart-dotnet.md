---
title: クイック スタート:C# でテキストの内容を分析する - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator SDK for .NET を使用して、さまざまな好ましくない素材のテキスト コンテンツを分析する方法
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: f848ea0d1c86b9c12d26c59b8f0e92d1e5d70094
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604057"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>クイック スタート:C# で好ましくない要素を検出するためにテキストの内容を分析する

この記事では、[Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) の使用を開始するために役立つ情報とコード サンプルを提供します。 好ましくない可能性がある素材のモデレートを目的として、テキスト コンテンツの用語ベースのフィルター処理と分類を実行する方法について学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件
- Content Moderator のサブスクリプション キー。 [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Content Moderator をサブスクライブし、キーを取得します。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション

> [!NOTE]
> このガイドでは、Free レベルの Content Moderator サブスクリプションを使用します。 各サブスクリプション レベルで提供される内容については、[価格と制限](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)に関するページを参照してください。

## <a name="create-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

1. Visual Studio で、新しい**コンソール アプリ (.NET Framework)** プロジェクトを作成し、**TextModeration** という名前を付けます。 
1. ソリューションに他のプロジェクトがある場合は、これを単一のスタートアップ プロジェクトとして選択します。
1. 必須の NuGet パッケージを入手します。 ソリューション エクスプローラーでプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。次のパッケージを見つけてインストールします。
    - `Microsoft.Azure.CognitiveServices.ContentModerator`
    - `Microsoft.Rest.ClientRuntime`
    - `Newtonsoft.Json`

## <a name="add-text-moderation-code"></a>テキスト モデレーション コードの追加

次に、基本的なコンテンツ モデレーション シナリオを実装するために、このガイドからコードをコピーしてプロジェクトに貼り付けます。

### <a name="include-namespaces"></a>名前空間を含める

*Program.cs* ファイルの先頭に次の `using` ステートメントを追加します。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Content Moderator クライアントを作成する

サブスクリプション用の Content Moderator クライアント プロバイダーを作成するために、*Program.cs* ファイルに次のコードを追加します。 コードは、同じ名前空間内の **Program** クラスの近くに追加します。 **AzureRegion** フィールドと **CMSubscriptionKey** フィールドを、リージョン識別子とサブスクリプション キーの値で更新する必要があります。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>入力と出力のターゲットを設定する

次の静的フィールドを _Program.cs_ 内の **Program** クラスに追加します。 これらのフィールドでは、入力テキスト コンテンツと出力 JSON コンテンツ用のファイルを指定します。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

*TextFile.txt* 入力ファイルを作成し、そのパスを更新する必要があります (パスは実行ディレクトリを基準にしています)。 _TextFile.txt_ を開き、モデレートするテキストを追加します。 このクイック スタートでは、次のサンプル テキストを使用します。

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>入力テキストを読み込む

次のコードを **Main** メソッドに追加します。 **ScreenText** メソッドは必須の操作です。 そのパラメーターには、実行するコンテンツ モデレーション操作を指定します。 この例では、次の操作を実行するようにメソッドを構成しています。
- テキスト内の潜在的な冒涜的表現を検出する。
- テキストを正規化し、入力ミスを自動修正する。
- 米国および英国の電話番号、メール アドレス、米国の住所など、個人データを検出します。
- 機械学習ベースのモデルを使用して、3 つのカテゴリにテキストを分類する。

これらの操作の詳細については、「[次の手順](#next-steps)」セクションのリンク先を参照してください。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>プログラムの実行

このプログラムは、JSON 文字列データを _TextModerationOutput.txt_ ファイルに書き出します。 このクイック スタートで使用したサンプル テキストでは、次の出力が得られます。

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Content Moderator サービスを使用して特定のサンプル テキストに関する関連情報を返す単純な .NET アプリケーションを開発しました。 次は、必要なデータを判断し、アプリを使ってそれをどのように処理するかを判断できるように、さまざまなフラグと分類の意味について学習します。

> [!div class="nextstepaction"]
> [テキスト モデレーション ガイド](text-moderation-api.md)
