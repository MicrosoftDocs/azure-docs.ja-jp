---
title: Video Indexer を使用してビデオをアップロードしてインデックスを付ける
titleSuffix: Azure Media Services
description: このトピックでは、API を使用して Video Indexer でビデオをアップロードし、インデックスを付ける方法を示します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/04/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a3c2812a4ecfa1a80539804122042bc2dc2f3a2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199188"
---
# <a name="upload-and-index-your-videos"></a>ビデオのアップロードとインデックス作成  

ビデオがアップロードされると、Video Indexer は (必要に応じて) ビデオをエンコードします (後述)。 Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される](connect-to-azure.md) Video Indexer アカウントを作成します。 インデックス作成にかかった時間 (分) に対して支払います。詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。

Video Indexer API でビデオをアップロードする場合、次のアップロード オプションがあります。 

* URL からビデオをアップロードする方法 (推奨) と、
* 要求本文のバイト配列としてビデオ ファイルを送信する方法です。
* [アセット ID](../latest/assets-concept.md) (有料アカウントでのみサポート) を指定して、既存の Azure Media Services アセットを使用します。

この記事では、次のオプションを使用してビデオをアップロードし、インデックスを作成する方法について説明します。

* [Video Indexer Web サイト](#upload-and-index-a-video-using-the-video-indexer-website) 
* [Video Indexer API](#upload-and-index-with-api)

## <a name="supported-file-formats-for-video-indexer"></a>Video Indexer でサポートされているファイル形式

Video Indexer で使用できるファイル形式の一覧については、「[入力コンテナー/ファイル形式](../latest/media-encoder-standard-formats.md#input-containerfile-formats)」を参照してください。

## <a name="video-files-storage"></a>ビデオ ファイルのストレージ

- Video Indexer の有料アカウントでは、ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される Video Indexer アカウントを作成します。 詳細については、「[Azure に接続された Video Indexer アカウントを作成する](connect-to-azure.md)」を参照してください。
- ビデオ ファイルは、Azure Media Services によって Azure storage に格納されます。 時間に制限はありません。
- ビデオ ファイルやオーディオ ファイルだけでなく、Video Indexer によってそれらのファイルから抽出されたメタデータや分析情報も削除することができます。 Video Indexer からファイルを削除すると、そのファイルとそのメタデータおよび分析情報は Video Indexer から完全に削除されます。 ただし、Azure ストレージに独自のバックアップ ソリューションを実装している場合、ファイルは Azure ストレージに残ります。
- アップロードが Video Indexer Web サイトで行われるか、Upload API を使用して行われるかに関係なく、ビデオの永続性は同じです。
   
## <a name="upload-and-index-a-video-using-the-video-indexer-website"></a>Video Indexer Web サイトを使用したビデオのアップロードとインデックス作成

> [!NOTE]
> ビデオの名前は、80 文字以下にする必要があります。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトにサインインします。
1. ビデオをアップロードするには、 **[アップロード]** ボタンまたはリンクを押します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="アップロード":::
1. ビデオがアップロードされると、Video Indexer がビデオのインデックス作成と分析を開始します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="アップロードの進行状況":::
1. Video Indexer で分析が完了すると、ビデオへのリンクとビデオの内容の簡単な説明が記載されたメールが届きます。 たとえば、人物、トピックス、OCR などが表示されます。

## <a name="upload-and-index-with-api"></a>API を使用したアップロードとインデックス作成

[Upload Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API を使用して、URL に基づいてビデオのアップロードとインデックス作成を行います。 後述のコード サンプルには、バイト配列をアップロードする方法を示すコメント アウトされたコードが含まれています。 

### <a name="configurations-and-params"></a>構成とパラメーター

このセクションでは、いくつかの省略可能なパラメーターと、それらを設定する場合について説明します。 最新のパラメーター情報については、[ビデオのアップロード](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)の API に関するページを参照してください。

#### <a name="externalid"></a>externalID 

このパラメーターでは、ビデオに関連付けられる ID を指定できます。 この ID を、外部の "ビデオ コンテンツ管理" (VCM) システムの統合に適用できます。 指定した外部 ID を使用して、Video Indexer ポータル内にあるビデオを検索できます。

#### <a name="callbackurl"></a>callbackUrl

[!INCLUDE [callback url](./includes/callback-url.md)]

##### <a name="other-considerations"></a>その他の考慮事項

- Video Indexer では、元の URL で指定された既存のすべてのパラメーターが返されます。
- 指定される URL は、エンコードする必要があります。

#### <a name="indexingpreset"></a>indexingPreset

このパラメーターを使用して、オーディオ ファイルまたはビデオ ファイルに適用する AI バンドルを定義します。 このパラメーターは、インデックス作成プロセスの構成に使用されます。 次の値を指定できます。

- `AudioOnly` – オーディオのみを使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います。
- `VideoOnly` – ビデオのみを使用 (オーディオは無視) して、分析情報のインデックス作成と抽出を行います。
- `Default` - オーディオとビデオの両方を使用して、分析情報のインデックス作成と抽出を行います。
- `DefaultWithNoiseReduction` - オーディオ ストリームにノイズ低減アルゴリズムを適用しながら、オーディオとビデオの両方から分析情報のインデックス作成と抽出を行います。

    `DefaultWithNoiseReduction` 値が既定のプリセット (非推奨) にマップされるようになりました。
- `BasicAudio` - オーディオのみ (文字起こし、翻訳、出力キャプションの書式設定、字幕などの基本的なオーディオ機能のみ) を使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います。
 - `AdvancedAudio` -標準のオーディオ分析に加え、高度なオーディオ機能 (オーディオ イベントの検出) など、オーディオのみを使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います。

> [!NOTE]
> Video Indexer では、最大 2 つのオーディオ トラックがカバーされます。 ファイル内にこれより多いオーディオ トラックがある場合、それらは 1 つのトラックとして扱われます。<br/>
トラックに個別にインデックスを付ける場合は、関連するオーディオ ファイルを抽出し、それに `AudioOnly` とインデックスを付ける必要があります。

料金は、選択したインデックス作成オプションによって異なります。 詳細については、[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)に関する記事を参照してください。

#### <a name="priority"></a>priority

ビデオには、優先度に従って、Video Indexer によってインデックスが付けられます。 インデックスの優先度を指定するには、**priority** パラメーターを使用します。 有効な値は、**Low**、**Normal** (既定)、**High** です。

**Priority** パラメーターは、有料アカウントだけでサポートされています。

#### <a name="streamingpreset"></a>streamingPreset

ビデオがアップロードされると、Video Indexer は必要に応じてビデオをエンコードします。 その後、インデックス作成とビデオの分析を行います。 Video Indexer が分析を完了すると、ビデオ ID を含んだ通知が送信されます。  

[Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API または [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API を使用するときの省略可能なパラメーターの 1 つに、`streamingPreset` があります。 `streamingPreset` を `Default`、`SingleBitrate`、または `AdaptiveBitrate` に設定すると、エンコード プロセスがトリガーされます。 インデックス作成ジョブとエンコード ジョブが完了すると、ビデオが公開され、ビデオをストリームできるようになります。 ビデオのストリーム元のストリーミング エンドポイントは、**実行中** 状態である必要があります。

SingleBitrate の場合、出力ごとに Standard Encoder コストが適用されます。 ビデオの高さが 720 以上の場合、Video Indexer によって 1280 x 720 としてエンコードされます。 それ以外の場合は、640 x 468 と指定されます。
既定の設定は、[コンテンツに対応したエンコード](../latest/content-aware-encoding.md)です。

インデックス作成ジョブとエンコード ジョブを実行するには、[Video Indexer アカウントに接続されている Azure Media Services アカウント](connect-to-azure.md)に予約ユニットが必要です。 詳細については、[メディア処理のスケール設定](../previous/media-services-scale-media-processing-overview.md)に関するページを参照してください。 これらはコンピューティング集中型のジョブであるため、ユニットの種類は S3 にすることを強くお勧めします。 RU の数によって、並列で実行できるジョブの最大数が定義されます。 ベースラインの推奨設定は、10 個の S3 RU です。 

ビデオのインデックス作成のみを行い、エンコードは行わない場合は、`streamingPreset` を `NoStreaming` に設定します。

#### <a name="videourl"></a>videoUrl

インデックスを作成するビデオ/音声ファイルの URL。 URL はメディア ファイルを示している必要があります (HTML ページはサポートされていません)。 このファイルは URI の一部として提供されるアクセス トークンで保護することができます。また、ファイルを提供するエンドポイントは TLS 1.2 以降を使用してセキュリティで保護する必要があります。 URL はエンコードする必要があります。 

`videoUrl` が指定されていない場合、ファイルを multipart/form 本文のコンテンツとして渡すことが Video Indexer で想定されます。

### <a name="code-sample"></a>コード サンプル

次の C# コード スニペットは、すべての Video Indexer API の使用方法を示しています。

**次のコード サンプルを実行するための手順**

このコードをお使いの開発プラットフォームにコピーした後、次の 2 つのパラメーターを指定する必要があります。API Management 認証キーとビデオの URL。

* API キー – API キーは、個人の API Management サブスクリプション キーです。これにより、Video Indexer アカウントで操作を実行するために、アクセス トークンを取得できます。 

    API キーを取得するには、次のフローを実行します。

    * https://api-portal.videoindexer.ai/ に移動します
    * ログイン
    * **[製品]**  ->  **[承認]**  ->  **[[Authorization subscription]\(承認サブスクリプション\)]** に移動します。
    * **主キー** をコピーします。
* ビデオの URL – インデックスを作成するビデオ/音声ファイルの URL。 URL はメディア ファイルを示している必要があります (HTML ページはサポートされていません)。 このファイルは URI の一部として提供されるアクセス トークンで保護することができます。また、ファイルを提供するエンドポイントは TLS 1.2 以降を使用してセキュリティで保護する必要があります。 URL はエンコードする必要があります。

コード サンプルを正常に実行した結果には、分析情報ウィジェットの URL とプレーヤー ウィジェットの URL が含まれます。これにより、アップロードされた分析情報とビデオをそれぞれ調べることができます。 


```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var apiKey = "..."; // replace with API key taken from https://aka.ms/viapi

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    
    // take the relevant account, here we simply take the first, 
    // you can also get the account via accounts.First(account => account.Id == <GUID>);
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Console.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =new byte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(new ByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video ID from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Console.WriteLine("Uploaded");
    Console.WriteLine("Video ID:");
    Console.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Console.WriteLine("");
        Console.WriteLine("State:");
        Console.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Console.WriteLine("");
            Console.WriteLine("Full JSON:");
            Console.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Console.WriteLine("");
    Console.WriteLine("Search:");
    Console.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Console.WriteLine("Insights Widget url:");
    Console.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
     Console.WriteLine("");
     Console.WriteLine("Player Widget url:");
     Console.WriteLine(playerWidgetLink);
     Console.WriteLine("\nPress Enter to exit...");
     String line = Console.ReadLine();
     if (line == "enter")
     {
         System.Environment.Exit(0);
     }

}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```

### <a name="common-errors"></a>一般的なエラー

アップロード操作によって返される場合がある状態コードを次の表に示します。

|status code|ErrorType (応答本体内)|説明|
|---|---|---|
|409|VIDEO_INDEXING_IN_PROGRESS|指定されたアカウントで既に同じビデオの処理が進行中です。|
|400|VIDEO_ALREADY_FAILED|指定されたアカウントで 2 時間以内に同じビデオの処理に失敗しました。 API クライアントは、ビデオを再アップロードする前に少なくとも 2 時間待つ必要があります。|
|429||試用版アカウントでは、1 分あたり 5 回のアップロードが許可されます。 有料アカウントでは、1 分あたり 50 回のアップロードが許可されます。|

## <a name="uploading-considerations-and-limitations"></a>アップロードに関する考慮事項と制限事項
 
- ビデオの名前は、80 文字以下にする必要があります。
- URL に基づいてビデオをアップロードする場合 (推奨)、エンドポイントは TLS 1.2 (またはそれ以降) を使用してセキュリティで保護する必要があります。
- URL オプションでのアップロード サイズは、30 GB に制限されます。
- 要求 URL の長さは 6144 文字に制限されており、クエリ文字列の URL の長さは 4096 文字に制限されています。
- バイト配列オプションでのアップロード サイズは、2 GB に制限されます。
- バイト配列オプションでは 30 分後にタイムアウトします。
- `videoURL` パラメーターに指定する URL はエンコードする必要があります
- Media Services アセットのインデックス作成には、URL からのインデックス作成と同じ制限が適用されます。
- Video Indexer では、1 つのファイルの最大時間制限は 4 時間です。
- URL にアクセスできる必要があります (たとえば、パブリック URL)。 

    プライベート URL の場合は、要求でアクセス トークンが提供される必要があります。
- URL では、Web ページ (`www.youtube.com` へのリンクなど) ではなく、有効なメディア ファイルを指す必要があります。
- 有料アカウントでは、1 分あたり最大 50 個のムービーをアップロードできます。試用版アカウントでは、1 分あたり最大 5 個のムービーです。

> [!Tip]
> .NET Framework バージョン 4.6.2 以上を使用することをお勧めします。 これは、それ以前の .NET Framework では既定で TLS 1.2 に設定されていないためです。
>
> 以前の .NET Framework を使用する必要がある場合は、REST API の呼び出しを行う前に、コードに次の 1 行を追加します。  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="next-steps"></a>次のステップ

[API によって生成される Azure Video Indexer の出力を調べる](video-indexer-output-json-v2.md)
