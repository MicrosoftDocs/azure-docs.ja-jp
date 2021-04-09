---
title: .NET を使用してビデオ レビューを作成する - Content Moderator
titleSuffix: Azure Cognitive Services
description: この記事では、C# で Content Moderator SDK を使用して、ビデオ レビューの作成をすばやく開始するのに役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: d9f80de5a18e27de4a9f8e85613e3c2eee6c111c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96853477"
---
# <a name="create-video-reviews-using-net"></a>.NET を使用してビデオ レビューを作成する

この記事には、[C# で Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) を使用して、次の操作をすぐに開始するために役立つ情報とコード サンプルが用意されています。

- 人間のモデレーター用のビデオ レビューを作成する
- レビューにフレームを追加する
- レビュー用のフレームを取得する
- レビューの状態と詳細を取得する
- レビューを公開する

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) サイトにサインインするか、アカウントを作成します。
- この記事は、[ビデオがモデレート済み (クイックスタートを参照)](video-moderation-api.md) かつ応答データがあることを前提としています。 これは、人間のモデレーター用のフレームベースのレビューを作成するために必要です。

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>お使いの API キーで、レビュー作成のためにレビュー API の呼び出しが可能であることを確認してください

Azure portal から起動した場合は、前の手順の完了後に Content Moderator のキーが 2 つある状態になることがあります。

SDK サンプルで、Azure から提供される API キーを使用する予定の場合は、[レビュー API での Azure キーの使用](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis)に関するセクションで説明されている手順に従って、アプリケーションがレビュー API を呼び出し、レビューを作成できるようにします。

レビュー ツールによって生成される無料試用版のキーを使用する場合、そのキーは既にレビュー ツール アカウントによって知られています。そのため、追加の手順は必要ありません。

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>レビュー用のビデオとビデオ フレームを準備する

ビデオとサンプルのビデオ フレームをレビューするにはそれらの URL が必要であるため、オンラインに公開されている必要があります。

> [!NOTE]
> プログラムは、手動で保存されたビデオのスクリーンショットとランダムな成人向け/きわどさのスコアを使用して、レビュー API の使用を示します。 実際の状況では、[ビデオ モデレーション出力](video-moderation-api.md#run-the-program-and-review-the-output)を使用してイメージを作成し、スコアを割り当てます。 

ビデオについては、レビュー ツールがビデオをプレーヤーのビューで再生するには、ストリーミング エンドポイントが必要です。

![ビデオ デモのサムネイル](images/ams-video-demo-view.PNG)

- マニフェスト URL にはこの [Azure Media Services デモ](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) ページの **URL** をコピーします。

ビデオ フレーム (イメージ) については、次のイメージを使用します。

![ビデオ フレームのサムネイル 1](images/ams-video-frame-thumbnails-1.PNG) | ![ビデオ フレームのサムネイル 2](images/ams-video-frame-thumbnails-2.PNG) | ![ビデオ フレームのサムネイル 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[フレーム 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [フレーム 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [フレーム 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. ソリューションに新しい **コンソール アプリ (.NET Framework)** プロジェクトを追加します。

1. プロジェクトの名前を **VideoReviews** にします。

1. このプロジェクトをソリューションのシングル スタートアップ プロジェクトとして選択します。

### <a name="install-required-packages"></a>必要なパッケージをインストールする

TermLists プロジェクト用に次の NuGet パッケージをインストールします。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>プログラムの using ステートメントを更新する

次のようにプログラムの using ステートメントを修正します。

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>プライベート プロパティを追加する

名前空間 **VideoReviews**、クラス **Program** に、次のプライベート プロパティを追加します。 エンドポイント URL とサブスクリプション キーの値を使用して、`AzureEndpoint` および `CMSubscriptionKey` フィールドを更新します。 これらは、Azure portal 内のリソースの **[クイック スタート]** タブで確認できます。


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Content Moderator クライアントのオブジェクトを作成する

名前空間 **VideoReviews**、クラス **Program** に、次のメソッド定義を追加します。

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>ビデオ レビューを作成する

**ContentModeratorClient.Reviews.CreateVideoReviews** でビデオ レビューを作成します。 詳細については、[API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)に関するページを参照してください。

**CreateVideoReviews** では、次のパラメーターが必要です。
1. MIME の種類が含まれる文字列。"application/json" にしてください。 
1. Content Moderator のチーム名。
1. **IList\<CreateVideoReviewsBodyItem>** オブジェクト。 各 **CreateVideoReviewsBodyItem** オブジェクトはビデオ レビューを表します。 このクイック スタートでは、一度に 1 つのレビューを作成します。

**CreateVideoReviewsBodyItem** にはいくつかのプロパティがあります。 少なくとも、次のプロパティを設定してください。
- **Content**。 レビューするビデオの URL。
- **ContentId**。 ビデオ レビューに割り当てる ID。
- **Status**。 値を "Unpublished" に設定します。 設定しない場合は既定で "Pending" になります。これはビデオ レビューが公開済みで、人間によるレビュー待ちであることを意味します。 ビデオ レビューが公開されると、ビデオ フレーム、トランスクリプト、トランスクリプトのモデレート結果を追加できなくなります。

> [!NOTE]
> **CreateVideoReviews** は IList\<string> を返します。 これらの文字列には、それぞれビデオ レビューの ID が含まれています。 これらの ID は GUID であり、**ContentId** プロパティの値とは異なります。 

名前空間 VideoReviews、クラス Program に次のメソッド定義を追加します。

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> お使いの Content Moderator のサービス キーは 1 秒ごとの要求数 (RPS) が制限されており、その上限を超えると、SDK が 429 エラー コードとともに例外をスローします。
>
> 無料レベルのキーの RPS は 1 に制限されています。

## <a name="add-video-frames-to-the-video-review"></a>ビデオ レビューにビデオ フレームを追加する

**ContentModeratorClient.Reviews.AddVideoFrameUrl** (ビデオ フレームがオンラインにホストされている場合) または **ContentModeratorClient.Reviews.AddVideoFrameStream** (ビデオ フレームがローカルにホストされている場合) を使用して、ビデオ レビューにビデオ フレームを追加します。 このクイック スタートでは、ビデオ フレームがオンラインにホストされていると想定し、**AddVideoFrameUrl** を使用します。 詳細については、[API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd)に関するページを参照してください。

**AddVideoFrameUrl** では、次のパラメーターが必要です。
1. MIME の種類が含まれる文字列。"application/json" にしてください。
1. Content Moderator のチーム名。
1. **CreateVideoReviews** によって返されるビデオ レビュー ID。
1. **IList\<VideoFrameBodyItem>** オブジェクト。 各 **VideoFrameBodyItem** オブジェクトはビデオ フレームを表します。

**VideoFrameBodyItem** には、次のプロパティがあります。
- **Timestamp**。 ビデオ フレームが取得されてからのビデオ内の時間 (秒単位) が含まれる文字列。
- **FrameImage**。 ビデオ フレームの URL。
- **Metadata**。 An IList\<VideoFrameBodyItemMetadataItem>。 **VideoFrameBodyItemMetadataItem** は単純にキーと値のペアです。 有効なキーは次のとおりです。
- **reviewRecommended**。 人間によるビデオ フレームのレビューが推奨される場合は True。
- **adultScore**。 ビデオ フレーム内の成人向けコンテンツの重大度を評価する 0 から 1 の値。
- **a**。 ビデオに成人向けコンテンツが含まれる場合は True。
- **racyScore**。 ビデオ フレーム内のきわどいコンテンツの重大度を評価する 0 から 1 の値。
- **r**。 ビデオ フレームにきわどいコンテンツが含まれている場合は True。
- **ReviewerResultTags**。 An IList\<VideoFrameBodyItemReviewerResultTagsItem>。 **VideoFrameBodyItemReviewerResultTagsItem** は単純にキーと値のペアです。 アプリケーションはこれらのタグを使用してビデオ フレームを整理できます。

> [!NOTE]
> このクイック スタートは、**adultScore** プロパティと **racyScore** プロパティにランダムな値を生成します。 運用環境のアプリケーションでは、これらの値を Azure Media Service としてデプロイされた[ビデオ モデレーション サービス](video-moderation-api.md)から取得します。

名前空間 VideoReviews、クラス Program に次のメソッド定義を追加します。

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>ビデオ レビュー用ビデオ フレームを取得する

**ContentModeratorClient.Reviews.GetVideoFrames** を使用してビデオ レビュー用のビデオ フレームを取得できます。 **GetVideoFrames** では、次のパラメーターが必要です。
1. Content Moderator のチーム名。
1. **CreateVideoReviews** によって返されるビデオ レビュー ID。
1. 取得する最初のビデオ フレームのゼロから始まるインデックス。
1. 取得するビデオ フレームの数。

名前空間 VideoReviews、クラス Program に次のメソッド定義を追加します。

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>ビデオ レビューの情報を取得する

**ContentModeratorClient.Reviews.GetReview** を使用して、ビデオ レビューの情報を取得します。 **GetReview** では、次のパラメーターが必要です。
1. Content Moderator のチーム名。
1. **CreateVideoReviews** によって返されるビデオ レビュー ID。

名前空間 VideoReviews、クラス Program に次のメソッド定義を追加します。

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>ビデオ レビューを公開する

**ContentModeratorClient.Reviews.PublishVideoReview** を使用して、ビデオ レビューを公開します。 **PublishVideoReview** では、次のパラメーターが必要です。
1. Content Moderator のチーム名。
1. **CreateVideoReviews** によって返されるビデオ レビュー ID。

名前空間 VideoReviews、クラス Program に次のメソッド定義を追加します。

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>まとめ

名前空間 VideoReviews、クラス Program に **Main** メソッド定義を追加します。 最後に、Program クラスと VideoReviews 名前空間を閉じます。

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>プログラムを実行して出力をレビューする
アプリケーションを実行すると、次の行に出力が表示されます。

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>ビデオ レビューを確認する

これで、Content Moderator のレビュー ツール アカウントの **[Review]\(レビュー\)** > **[Video]\(ビデオ\)** 画面にビデオ レビューが表示されます。

![人間のモデレーター用のビデオ レビュー](images/ams-video-review.PNG)

## <a name="next-steps"></a>次のステップ

[Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) と、.NET 用のこのクイック スタートや他の Content Moderator のクイックスタートのための [Visual Studio ソリューション](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)をダウンロードする。
