---
title: .NET を使用してビデオ トランスクリプト レビューを作成する - Content Moderator
titleSuffix: Azure Cognitive Services
description: .NET 用の Azure Cognitive Services Content Moderator SDK を使用してビデオ トランスクリプト レビューを作成する方法を説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853460"
---
# <a name="create-video-transcript-reviews-using-net"></a>.NET を使用してビデオ トランスクリプト レビューを作成する

この記事には、[C# で Content Moderator SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) を使用して、次の操作をすぐに開始するために役立つ情報とコード サンプルが用意されています。

- 人間のモデレーター用のビデオ レビューを作成する
- モデレートされたトランスクリプトをレビューに追加する
- レビューを公開する

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)サイトにサインインするか、アカウントを作成します (まだこの操作を行っていない場合)。
- この記事では、人間が意思決定できるようにレビューツールで[ビデオをモデレート](video-moderation-api.md)し、[ビデオ レビューを作製](video-reviews-quickstart-dotnet.md)していると想定しています。 現在、モデレートしたビデオ トランスクリプトをレビュー ツールで追加しようと考えています。

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>お使いの API キーで、レビュー API (ジョブの作成) の呼び出しが可能であることを確認してください。

Azure portal から起動した場合は、前の手順の完了後に Content Moderator のキーが 2 つある状態になることがあります。

SDK サンプルで、Azure から提供される API キーを使用する予定の場合は、[レビュー API での Azure キーの使用](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis)に関するセクションで説明されている手順に従って、アプリケーションがレビュー API を呼び出し、レビューを作成できるようにします。

レビュー ツールによって生成される無料試用版のキーを使用する場合、そのキーは既にレビュー ツール アカウントによって知られています。そのため、追加の手順は必要ありません。

## <a name="prepare-your-video-for-review"></a>レビュー用にビデオを準備する

トランスクリプトをビデオ レビューに追加します。 ビデオはオンラインで公開する必要があります。 そのストリーミング エンドポイントが必要です。 ストリーミング エンドポイントでは、レビュー ツールのビデオ プレーヤーでビデオを再生できます。

![ビデオ デモのサムネイル](images/ams-video-demo-view.PNG)

- マニフェスト URL にはこの [Azure Media Services デモ](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) ページの **URL** をコピーします。

## <a name="create-your-visual-studio-project"></a>Visual Studio プロジェクトを作成する

1. ソリューションに新しい **コンソール アプリ (.NET Framework)** プロジェクトを追加します。

1. プロジェクトに **VideoTranscriptReviews** という名前を付けます。

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

名前空間 **VideoTranscriptReviews**、クラス **Program** に次のプライベート プロパティを追加します。 エンドポイント URL とサブスクリプション キーの値を使用して、`AzureEndpoint` および `CMSubscriptionKey` フィールドを更新します。 これらは、Azure portal 内のリソースの **[クイック スタート]** タブで確認できます。

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

名前空間 VideotranscriptReviews、クラス Program に次のメソッドの定義を追加します。

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
> Content Moderator サービス キーには、秒単位の要求数 (RPS) の制限があります。 この制限を超えた場合、SDK は 429 エラー コードを使用して例外をスローします。
>
> 無料レベルのキーの RPS は 1 に制限されています。

## <a name="add-transcript-to-video-review"></a>トランスクリプトをビデオ レビューに追加する

**ContentModeratorClient.Reviews.AddVideoTranscript** でビデオ レビューにトランスクリプトを追加します。 **AddVideoTranscript** では、次のパラメーターが必要です。
1. Content Moderator のチーム ID。
1. **CreateVideoReviews** によって返されるビデオ レビュー ID。
1. トランスクリプトを含む **ストリーム** オブジェクト。

トランスクリプトは、WebVTT 形式である必要があります。 詳細については、[WebVTT: Web ビデオ テキスト トラック形式](https://www.w3.org/TR/webvtt1/)に関するページをご覧ください。

> [!NOTE]
> プログラムは、VTT 形式でサンプル トランスクリプトを使用します。 実際のソリューションでは、Azure Media Indexer サービスを使用してビデオから[トランスクリプトを生成](../../media-services/previous/media-services-index-content.md)します。

名前空間 VideotranscriptReviews、クラス Program に次のメソッドの定義を追加します。

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>レビューのビデオ トランスクリプト モデレーション結果を追加する

ビデオ レビューにトランスクリプトを追加するだけでなく、そのトランスクリプトをモデレートした結果も追加します。 これを行うには、**ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult** を使用します。 詳細については、[API リファレンス](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff)に関するページを参照してください。

**AddVideoTranscriptModerationResult** では、次のパラメーターが必要です。
1. MIME の種類が含まれる文字列。"application/json" にしてください。 
1. Content Moderator のチーム名。
1. **CreateVideoReviews** によって返されるビデオ レビュー ID。
1. An IList\<TranscriptModerationBodyItem>。 **TranscriptModerationBodyItem** には次のプロパティがあります。
1. **Terms**。 An IList\<TranscriptModerationBodyItemTermsItem>。 **TranscriptModerationBodyItemTermsItem** には次のプロパティがあります。
1. **Index**。 用語のゼロベースのインデックスです。
1. **Term**。 用語を含む文字列です。
1. **Timestamp**。 用語が見つかったトランスクリプトの時間 (秒単位) を含む文字列です。

トランスクリプトは、WebVTT 形式である必要があります。 詳細については、[WebVTT: Web ビデオ テキスト トラック形式](https://www.w3.org/TR/webvtt1/)に関するページをご覧ください。

名前空間 VideotranscriptReviews、クラス Program に次のメソッドの定義を追加します。 このメソッドは、**ContentModeratorClient.TextModeration.ScreenText** メソッドにトランスクリプトを送信します。 また、IList\<TranscriptModerationBodyItem> に結果を変換し、**AddVideoTranscriptModerationResult** に送信します。

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

名前空間 VideoTranscriptReviews、クラス Program に **Main** メソッドの定義を追加します。 最後に、Program クラスと VideoTranscriptReviews 名前空間を閉じます。

> [!NOTE]
> プログラムは、VTT 形式でサンプル トランスクリプトを使用します。 実際のソリューションでは、Azure Media Indexer サービスを使用してビデオから[トランスクリプトを生成](../../media-services/previous/media-services-index-content.md)します。

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>ビデオ トランスクリプト レビューに移動する

**[確認]** > **[ビデオ]** > **[トランスクリプト]** 画面上の Content Moderator レビュー ツールでビデオ トランスクリプト レビューに移動します。

次の機能が表示されます。
- 追加した 2 行のトランスクリプト
- テキスト モデレーション サービスにより検出され強調表示された不適切な用語
- 文字起こしテキストを選択すると、そのタイムスタンプからビデオが開始する

![ヒューマン モデレーター用のビデオ トランスクリプト レビュー](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>次のステップ

[Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) と、.NET 用のこのクイック スタートや他の Content Moderator のクイックスタートのための [Visual Studio ソリューション](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)をダウンロードする。

レビュー ツールで[ビデオ レビュー](video-reviews-quickstart-dotnet.md)を生成する方法について学習する。
