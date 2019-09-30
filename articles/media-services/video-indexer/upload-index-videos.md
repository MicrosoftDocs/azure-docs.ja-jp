---
title: Video Indexer を使用してビデオをアップロードしてインデックスを付ける
titlesuffix: Azure Media Services
description: このトピックでは、API を使用して Video Indexer でビデオをアップロードし、インデックスを付ける方法を示します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 7233bea4a030b814a5332284a80f07a71f288dba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128215"
---
# <a name="upload-and-index-your-videos"></a>ビデオのアップロードとインデックス作成  

Video Indexer API でビデオをアップロードする場合、次のアップロード オプションがあります。 

* URL からビデオをアップロードする方法 (推奨) と、
* 要求本文のバイト配列としてビデオ ファイルを送信する方法です。
* [アセット ID](https://docs.microsoft.com/azure/media-services/latest/assets-concept) (有料アカウントでのみサポート) を指定して、既存の Azure Media Services アセットを使用します。

この記事では、[Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API を使用して、URL に基づいてビデオのアップロードとインデックス作成を行う方法について説明します。 この記事のコード サンプルには、バイト配列をアップロードする方法を示すコメント アウトされたコードが含まれています。 <br/>この記事では、API の出力を変更および処理するために API で設定できるいくつかのパラメーターについても説明します。

ビデオがアップロードされると、Video Indexer は必要に応じてビデオをエンコードします (後述)。 Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される](connect-to-azure.md) Video Indexer アカウントを作成します。 Media アカウント関連の料金と同様に、インデックス作成時間 (分単位) の料金がかかります。 

## <a name="uploading-considerations"></a>アップロードに関する考慮事項
 
- URL に基づいてビデオをアップロードする場合 (推奨)、エンドポイントは TLS 1.2 (またはそれ以降) を使用してセキュリティで保護する必要があります。
- URL オプションでのアップロード サイズは、30 GB に制限されます。
- 要求 URL の長さは 6144 文字に制限されており、クエリ文字列の URL の長さは 4096 文字に制限されています。
- バイト配列オプションでのアップロード サイズは、2 GB に制限されます。
- バイト配列オプションでは 30 分後にタイムアウトします。
- `videoURL` パラメーターに指定する URL はエンコードする必要があります
- Media Services アセットのインデックス作成には、URL からのインデックス作成と同じ制限が適用されます。
- Video Indexer では、1 つのファイルの最大時間制限は 4 時間です。

> [!Tip]
> .NET Framework バージョン 4.6.2 以上を使用することをお勧めします。 これは、それ以前の .NET Framework では既定で TLS 1.2 に設定されていないためです。
>
> 以前の .NET Framework を使用する必要がある場合は、REST API の呼び出しを行う前に、コードに次の 1 行を追加します。  <br/> System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls12;

## <a name="configurations-and-params"></a>構成とパラメーター

このセクションでは、いくつかの省略可能なパラメーターと、それらを設定する場合について説明します。

### <a name="externalid"></a>externalID 

このパラメーターでは、ビデオに関連付けられる ID を指定できます。 この ID を、外部の "ビデオ コンテンツ管理" (VCM) システムの統合に適用できます。 指定した外部 ID を使用して、Video Indexer ポータル内にあるビデオを検索できます。

### <a name="callbackurl"></a>callbackUrl

以下のイベントについてのユーザーへの通知 (POST 要求を使用) に使われる URL です。

- インデックス状態の変更: 
    - プロパティ:    
    
        |名前|説明|
        |---|---|
        |id|ビデオ ID|
        |state|ビデオの状態|  
    - 例: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed
- ビデオで特定された人物:
  - properties
    
      |名前|説明|
      |---|---|
      |id| ビデオ ID|
      |faceId|ビデオ インデックスに表示される顔 ID|
      |knownPersonId|顔モデル内で一意の人物 ID|
      |personName|人物の名前|
        
    - 例: https:\//test.com/notifyme?projectName=MyProject&id=1234abcd&faceid=12&knownPersonId=CCA84350-89B7-4262-861C-3CAC796542A5&personName=Inigo_Montoya 

#### <a name="notes"></a>メモ

- Video Indexer では、元の URL で指定された既存のすべてのパラメーターが返されます。
- 指定される URL は、エンコードする必要があります。

### <a name="indexingpreset"></a>indexingPreset

未加工の録画または外部の録画に背景ノイズが入っている場合は、このパラメーターを使用します。 このパラメーターは、インデックス作成プロセスの構成に使用されます。 次の値を指定できます。

- `Default` – オーディオとビデオの両方を使用して、分析情報のインデックス作成と抽出を行います
- `AudioOnly` – オーディオのみを使用 (ビデオは無視) して、分析情報のインデックス作成と抽出を行います
- `DefaultWithNoiseReduction` – オーディオ ストリームにノイズ低減アルゴリズムを適用しながら、オーディオとビデオの両方からインデックス作成と抽出を行います

料金は、選択したインデックス作成オプションによって異なります。  

### <a name="priority"></a>priority

ビデオには、優先度に従って、Video Indexer によってインデックスが付けられます。 インデックスの優先度を指定するには、**priority** パラメーターを使用します。 有効な値は、**Low**、**Normal** (既定)、**High** です。

**Priority** パラメーターは、有料アカウントだけでサポートされています。

### <a name="streamingpreset"></a>streamingPreset

ビデオがアップロードされると、Video Indexer は必要に応じてビデオをエンコードします。 その後、インデックス作成とビデオの分析を行います。 Video Indexer が分析を完了すると、ビデオ ID を含んだ通知が送信されます。  

[Upload video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) API または [Re-Index Video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) API を使用するときの省略可能なパラメーターの 1 つに、`streamingPreset` があります。 `streamingPreset` を `Default`、`SingleBitrate`、または `AdaptiveBitrate` に設定すると、エンコード プロセスがトリガーされます。 インデックス作成ジョブとエンコード ジョブが完了すると、ビデオが公開され、ビデオをストリームできるようになります。 ビデオのストリーム元のストリーミング エンドポイントは、**実行中**状態である必要があります。

インデックス作成ジョブとエンコード ジョブを実行するには、[Video Indexer アカウントに接続されている Azure Media Services アカウント](connect-to-azure.md)に予約ユニットが必要です。 詳細については、[メディア処理のスケール設定](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)に関するページを参照してください。 これらはコンピューティング集中型のジョブであるため、ユニットの種類は S3 にすることを強くお勧めします。 RU の数によって、並列で実行できるジョブの最大数が定義されます。 ベースラインの推奨設定は、10 個の S3 RU です。 

ビデオのインデックス作成のみを行い、エンコードは行わない場合は、`streamingPreset` を `NoStreaming` に設定します。

### <a name="videourl"></a>videoUrl

インデックスを作成するビデオ/音声ファイルの URL。 URL はメディア ファイルを示している必要があります (HTML ページはサポートされていません)。 このファイルは URI の一部として提供されるアクセス トークンで保護することができます。また、ファイルを提供するエンドポイントは TLS 1.2 以降を使用してセキュリティで保護する必要があります。 URL はエンコードする必要があります。 

`videoUrl` が指定されていない場合、ファイルを multipart/form 本文のコンテンツとして渡すことが Video Indexer で想定されます。

## <a name="code-sample"></a>サンプル コード

次の C# コード スニペットは、すべての Video Indexer API の使用方法を示しています。

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

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
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
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
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

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

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
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
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

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
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
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
## <a name="common-errors"></a>一般的なエラー

アップロード操作によって返される場合がある状態コードを次の表に示します。

|status code|ErrorType (応答本体内)|説明|
|---|---|---|
|400|VIDEO_ALREADY_IN_PROGRESS|指定されたアカウントで既に同じビデオの処理が進行中です。|
|400|VIDEO_ALREADY_FAILED|指定されたアカウントで 2 時間以内に同じビデオの処理に失敗しました。 API クライアントは、ビデオを再アップロードする前に少なくとも 2 時間待つ必要があります。|

## <a name="next-steps"></a>次の手順

[API によって生成される Azure Video Indexer の出力を調べる](video-indexer-output-json-v2.md)
