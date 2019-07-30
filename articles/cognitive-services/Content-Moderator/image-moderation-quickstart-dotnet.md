---
title: クイック スタート:画像を分析して好ましくない内容を検出する (C#) - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator SDK for .NET を使用して、さまざまな好ましくない素材の画像コンテンツを分析する方法
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: d9a28dcd4af68cf9c00eb3d338c4bd83c8d89ecc
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604115"
---
# <a name="quickstart-analyze-images-for-objectionable-content-in-c"></a>クイック スタート:画像を分析して好ましくない内容を検出する (C#)

この記事では、[Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) の使用を開始するために役立つ情報とコード サンプルを提供します。 好ましくない可能性がある素材のモデレートを目的として、成人向けまたはきわどいコンテンツ、抽出可能なテキスト、人の顔をスキャンする方法について学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="prerequisites"></a>前提条件

- Content Moderator のサブスクリプション キー。 [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Content Moderator をサブスクライブし、キーを取得します。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション


> [!NOTE]
> このガイドでは、Free レベルの Content Moderator サブスクリプションを使用します。 各サブスクリプション レベルで提供される内容については、[価格と制限](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)に関するページを参照してください。

## <a name="create-the-visual-studio-project"></a>Visual Studio プロジェクトの作成

1. Visual Studio で、新しい**コンソール アプリ (.NET Framework)** プロジェクトを作成し、**ImageModeration** という名前を付けます。 
1. ソリューションに他のプロジェクトがある場合は、これを単一のスタートアップ プロジェクトとして選択します。
1. 必須の NuGet パッケージを入手します。 ソリューション エクスプローラーでプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。次のパッケージを見つけてインストールします。
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-image-moderation-code"></a>画像モデレーション コードの追加

次に、基本的なコンテンツ モデレーション シナリオを実装するために、このガイドからコードをコピーしてプロジェクトに貼り付けます。

### <a name="include-namespaces"></a>名前空間を含める

*Program.cs* ファイルの先頭に次の `using` ステートメントを追加します。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Content Moderator クライアントを作成する

サブスクリプション用の Content Moderator クライアント プロバイダーを作成するために、*Program.cs* ファイルに次のコードを追加します。 コードは、同じ名前空間内の **Program** クラスの近くに追加します。 **AzureRegion** フィールドと **CMSubscriptionKey** フィールドを、リージョン識別子とサブスクリプション キーの値で更新する必要があります。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=84-107)]


### <a name="set-up-input-and-output-targets"></a>入力と出力のターゲットを設定する

次の静的フィールドを _Program.cs_ 内の **Program** クラスに追加します。 これらのフィールドでは、入力画像コンテンツ用および出力 JSON コンテンツ用のファイルを指定します。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=49-53)]

*_ImageFiles.txt* 入力ファイルを作成し、それに合わせてパスを更新する必要があります (相対パスは実行ディレクトリを基準にしています)。 _ImageFiles.txt_ を開き、モデレートする画像の URL を追加します。 このクイック スタートでは、サンプル入力として次の URL を使用します。

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="create-a-class-to-handle-results"></a>結果を処理するためのクラスを作成する

次のコードを *Program.cs* に追加します (同じ名前空間内の **Program** クラスの近く)。 レビューした各画像のモデレーション結果は、このクラスのインスタンスを使用して記録することになります。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=109-124)]


### <a name="define-the-image-evaluation-method"></a>画像の評価メソッドを定義する

**Program** クラスに次のメソッドを追加します。 このメソッドでは、3 つの異なる方法で単一の画像が評価され、評価結果が返されます。 個々の操作について詳しく知りたい場合は、「[次の手順](#next-steps)」セクションのリンク先に進んでください。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=55-81)]

### <a name="load-the-input-images"></a>入力画像を読み込む

**Program** クラスの **Main** メソッドに次のコードを追加します。 このコードにより、入力ファイル内の各画像 URL に関する評価データを取得するようにプログラムが設定されます。 これで、このデータが単一の出力ファイルに書き込まれます。

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/image-moderation-quickstart-dotnet.cs?range=17-46)]

## <a name="run-the-program"></a>プログラムの実行

プログラムを実行すると、JSON 文字列データが _ModerationOutput.json_ ファイルに書き出されます。 このクイック スタートで使用したサンプル画像では、次の出力が得られます。 画像ごとに `ImageModeration`、`FaceDetection`、`TextDetection` 用の異なるセクションがあり、それぞれ **EvaluateImage** メソッドの 3 つの API 呼び出しに対応しています。

```json
[{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg",
  "ImageModeration": {
    "cacheID": "7733c303-3b95-4710-a41e-7a322ae81a15_636488005858745661",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_687c356d-0f00-4aeb-ae5f-c7555af80247",
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "116"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "12"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_814fa162-c5d6-4ca6-997b-30ed0686ca83",
    "cacheId": "3fb69496-c64b-4de9-affd-6dd6d23f3e78_636488005876558920",
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_a2c40dbe-609d-4eb8-b01c-9988388804ea",
    "cacheId": "e4c0b500-ea8e-4a31-8fb3-35f98c4fbd65_636488005889528303",
    "result": false,
    "count": 0,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "11"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "273405"
      }
    ],
    "faces": []
  }
},
{
  "ImageUrl": "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png",
  "ImageModeration": {
    "cacheID": "b4866aa2-5e69-44ed-806a-f9a5d618c8ae_636488005930693926",
    "result": false,
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_fdce5510-f689-4791-b081-c2ad54dcfe78",
    "adultClassificationScore": 0.0035635426174849272,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.021369094029068947,
    "isImageRacyClassified": false,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "109"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    }
  },
  "TextDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "metadata": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "46"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_08a4bc19-6010-41bb-a440-a77278e167d8",
    "cacheId": "28b37471-41b3-4f79-bd23-965498bcff51_636488005950851288",
    "language": "eng",
    "text": "",
    "candidates": []
  },
  "FaceDetection": {
    "status": {
      "code": 3000.0,
      "description": "OK",
      "exception": null
    },
    "trackingId": "WE_e1f20803b4ed471fb5de7df551f5bd9f_ContentModerator.Preview_40f2ce07-14ba-47cd-ba09-58b557a89854",
    "cacheId": "ec9c1be3-99b7-4bd9-8bc4-dc958c74459f_636488005964914299",
    "result": true,
    "count": 6,
    "advancedInfo": [
      {
        "key": "ImageDownloadTimeInMs",
        "value": "60"
      },
      {
        "key": "ImageSizeInBytes",
        "value": "2278902"
      }
    ],
    "faces": [
      {
        "bottom": 598,
        "left": 44,
        "right": 268,
        "top": 374
      },
      {
        "bottom": 620,
        "left": 308,
        "right": 532,
        "top": 396
      },
      {
        "bottom": 575,
        "left": 594,
        "right": 773,
        "top": 396
      },
      {
        "bottom": 563,
        "left": 812,
        "right": 955,
        "top": 420
      },
      {
        "bottom": 611,
        "left": 972,
        "right": 1151,
        "top": 432
      },
      {
        "bottom": 510,
        "left": 1232,
        "right": 1456,
        "top": 286
      }
    ]
  }
}]
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Content Moderator サービスを使用して特定のサンプル画像に関する関連情報を返す単純な .NET アプリケーションを開発しました。 次は、必要なデータを判断し、アプリを使ってそれをどのように処理するかを判断できるように、さまざまなフラグと分類の意味について学習します。

> [!div class="nextstepaction"]
> [画像モデレーション ガイド](image-moderation-api.md)
