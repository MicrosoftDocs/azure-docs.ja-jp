---
title: .NET を使用してレビューを作成する - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator SDK for .NET を使用してレビューを作成する方法。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: 153d42bf4ce4322536d6837be3058d1f9bfb49a2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758637"
---
# <a name="create-human-reviews-net"></a>人によるレビューの作成 (.NET)

レビューでは、人間のモデレーターが評価するコンテンツを格納および表示します。 ユーザーがレビューを完了すると、結果が指定のコールバック エンドポイントに送信されます。 このガイドでは、[Content Moderator SDK for .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) を使用して次の操作をすぐに開始するために役立つ情報とコード サンプルを提供します。

- 人によるモデレーション用にレビューのセットを作成する
- 人によるモデレーション用に既存レビューの状態を取得する

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) サイトにサインインするか、アカウントを作成します。

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>お使いの API キーで、レビュー作成のためにレビュー API の呼び出しが可能であることを確認してください

Azure portal から起動した場合は、前の手順の完了後に Content Moderator のキーが 2 つある状態になることがあります。

SDK サンプルで、Azure から提供される API キーを使用する予定の場合は、[レビュー API での Azure キーの使用](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis)に関するセクションで説明されている手順に従って、アプリケーションがレビュー API を呼び出し、レビューを作成できるようにします。

レビュー ツールによって生成される無料試用版のキーを使用する場合、そのキーは既にレビュー ツール アカウントによって知られています。そのため、追加の手順は必要ありません。

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. ソリューションに新しい**コンソール アプリ (.NET Framework)** プロジェクトを追加します。

   サンプル コードで、プロジェクトに **CreateReviews** と名前を付けます。

1. このプロジェクトをソリューションのシングル スタートアップ プロジェクトとして選択します。

### <a name="install-required-packages"></a>必要なパッケージをインストールする

次の NuGet パッケージをインストールします。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

プログラムの using ステートメントを変更します。


```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator クライアントを作成する

次のコードを追加して、サブスクリプションの Content Moderator クライアントを作成します。

> [!IMPORTANT]
> **AzureRegion** および **CMSubscriptionKey** フィールドをリージョン識別子とサブスクリプション キーの値で更新します。

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account,
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>内部コンテンツ情報をレビュー ID と関連付けるためのクラスを作成する

**Program** クラスに次のクラスを追加します。 このクラスを使用して、レビュー ID を項目の内部コンテンツ ID に関連付けます。

```csharp
/// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }
```

### <a name="initialize-application-specific-settings"></a>アプリケーション固有の設定を初期化する

> [!NOTE]
> お使いの Content Moderator のサービス キーは 1 秒ごとの要求数 (RPS) が制限されており、その上限を超えると、SDK が 429 エラー コードとともに例外をスローします。
>
> 無料レベルのキーの RPS は 1 に制限されています。

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>次の定数を Program.cs 内の **Program** クラスに追加します

```csharp
/// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";
```

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>次の定数と静的フィールドを Program.cs 内の **Program** クラスに追加します

これらの値を、お客様のサブスクリプションとチームに固有の情報を含むように更新します。

> [!NOTE]
> TeamName 定数は、[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)のサブスクリプションの作成時に使用した名前に設定します。 TeamName は、**[設定]** (歯車) メニューの **[資格情報]** セクションから取得します。
>
> チーム名は、**[API]** セクションの **[Id]** フィールドの値です。

```csharp
/// <summary>
/// The name of the team to assign the review to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "YOUR REVIEW TEAM ID";

/// <summary>
/// The optional name of the subteam to assign the review to.
/// </summary>
private const string Subteam = null;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team. 
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "YOUR API ENDPOINT";

/// <summary>
/// The media type for the item to review.
/// </summary>
/// <remarks>Valid values are "image", "text", and "video".</remarks>
private const string MediaType = "image";

/// <summary>
/// The URLs of the images to create review jobs for.
/// </summary>
private static readonly string[] ImageUrls = new string[] {
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
    // add more if you want
};

/// <summary>
/// The metadata key to initially add to each review item.
/// </summary>
private const string MetadataKey = "sc";

/// <summary>
/// The metadata value to initially add to each review item.
/// </summary>
private const string MetadataValue = "true";
```

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>次の静的フィールドを Program.cs 内の **Program** クラスに追加します

これらのフィールドは、アプリケーション状態を追跡するために使用します。

```csharp
/// <summary>
/// A static reference to the text writer to use for logging.
/// </summary>
private static TextWriter writer;

/// <summary>
/// The cached review information, associating a local content ID
/// to the created review ID for each item.
/// </summary>
private static List<ReviewItem> reviewItems =
    new List<ReviewItem>();
```

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>ログ ファイルにメッセージを書き込むメソッドを作成する

**Program** クラスに次のメソッドを追加します。

```csharp
/// <summary>
/// Writes a message to the log file, and optionally to the console.
/// </summary>
/// <param name="message">The message.</param>
/// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
private static void WriteLine(string message = null, bool echo = false)
{
    writer.WriteLine(message ?? String.Empty);

    if (echo)
    {
        Console.WriteLine(message ?? String.Empty);
    }
}
```

## <a name="create-a-method-to-create-a-set-of-reviews"></a>レビューのセットを作成するメソッドを作成する

通常は、レビューを必要とする着信イメージ、テキスト、またはビデオを識別するための何らかのビジネス ロジックが存在します。 しかしここでは、イメージの固定リストのみを使用します。

**Program** クラスに次のメソッドを追加します。

```csharp
/// <summary>
/// Create the reviews using the fixed list of images.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
private static void CreateReviews(ContentModeratorClient client)
{
    WriteLine(null, true);
    WriteLine("Creating reviews for the following images:", true);

    // Create the structure to hold the request body information.
    List<CreateReviewBodyItem> requestInfo =
        new List<CreateReviewBodyItem>();

    // Create some standard metadata to add to each item.
    List<CreateReviewBodyItemMetadataItem> metadata =
        new List<CreateReviewBodyItemMetadataItem>(
        new CreateReviewBodyItemMetadataItem[] {
            new CreateReviewBodyItemMetadataItem(
                MetadataKey, MetadataValue)
        });

    // Populate the request body information and the initial cached review information.
    for (int i = 0; i < ImageUrls.Length; i++)
    {
        // Cache the local information with which to create the review.
        var itemInfo = new ReviewItem()
        {
            Type = MediaType,
            ContentId = i.ToString(),
            Url = ImageUrls[i],
            ReviewId = null
        };

        WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

        // Add the item informaton to the request information.
        requestInfo.Add(new CreateReviewBodyItem(
            itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
            CallbackEndpoint, metadata));

        // Cache the review creation information.
        reviewItems.Add(itemInfo);
    }

    var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
        "application/json", TeamName, requestInfo);

    // Update the local cache to associate the created review IDs with
    // the associated content.
    var reviewIds = reviewResponse.Result.Body;
    for (int i = 0; i < reviewIds.Count; i++)
    {
        Program.reviewItems[i].ReviewId = reviewIds[i];
    }

    WriteLine(JsonConvert.SerializeObject(
    reviewIds, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>既存のレビューの状態を取得するメソッドを作成する

**Program** クラスに次のメソッドを追加します。

> [!Note]
> 実際には、手動レビューの結果を (HTTP POST 要求を通じて) 受け取る URL へのコールバック URL `CallbackEndpoint` を設定します。 このメソッドを、保留中のレビューの状態を調べるために変更できます。

```csharp
/// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }
```

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>レビューのセットを作成してその状態を調べるコードを追加する

次のコードを **Main** メソッドに追加します。

このコードは、リストを定義および管理するときに実行する多くの操作をシミュレートするだけでなく、リストを使用して画像をスクリーニングします。 ログ記録機能を使用すると、SDK から Content Moderator サービスへの呼び出しによって生成された応答オブジェクトを確認できます。

```csharp
using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
{
    writer = outputWriter;
    using (var client = Clients.NewClient())
    {
        CreateReviews(client);
        GetReviewDetails(client);

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
        Thread.Sleep(latencyDelay * 1000);

        GetReviewDetails(client);
    }

    writer = null;
    outputWriter.Flush();
    outputWriter.Close();
}

Console.WriteLine();
Console.WriteLine("Press any key to exit...");
Console.ReadKey();
```

## <a name="run-the-program-and-review-the-output"></a>プログラムを実行して出力をレビューする

次のサンプル出力が表示されます。

```console
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
```

Content Moderator レビュー ツールにサインインすると、**sc** ラベルが **true** に設定された状態で、保留中のイメージ レビューが表示されます。 また、既定の **a**、**r** タグと、レビュー ツールで定義済みのすべてのカスタム タグも表示されます。

**[次へ]** ボタンを使用して送信します。

![人によるモデレーション用のイメージ レビュー](images/moderation-reviews-quickstart-dotnet.PNG)

いずれかのキーを押して続行します。

```console
Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...
```

## <a name="check-out-the-following-output-in-the-log-file"></a>ログ ファイルで、次の出力を確認します。

> [!NOTE]
> 出力ファイルで、文字列 "\{teamname}" と "\{callbackUrl}" に `TeamName` フィールドと `CallbackEndpoint` フィールドの値がそれぞれ反映されています。

レビュー ID とイメージ コンテンツ URL は、アプリケーションを実行するたびに異なります。レビューが完了すると、レビュー担当者が項目にどのようにタグ付けしたかが `reviewerResultTags` フィールドに反映されます。

```json
Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
              "key": "a",
              "value": "False"
        },
        {
              "key": "r",
              "value": "True"
        },
        {
              "key": "sc",
              "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
              "key": "sc",
              "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>この応答がコールバック URL (指定した場合) に送信される

次の例のような応答が表示されます。

```json
{
    "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
    "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
    "ModifiedBy": "yourusername",
    "CallBackType": "Review",
    "ContentId": "0",
    "ContentType": "Image",
    "Metadata": {
        "sc": "true"
        },
    "ReviewerResultTags": {
        "a": "False",
        "r": "False",
    }
}
```

## <a name="next-steps"></a>次の手順

[Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) を入手し、.NET 用のこの Content Moderator クイック スタートや他のクイック スタート用の [Visual Studio ソリューション](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)をダウンロードして、統合を開始します。