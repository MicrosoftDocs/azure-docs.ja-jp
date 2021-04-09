---
title: Video Indexer API の使用
titleSuffix: Azure Media Services
description: この記事では、Azure Media Services Video Indexer API の使用を開始する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: fcd194e2503610db314f6a975a4afb1d27962f8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028222"
---
# <a name="tutorial-use-the-video-indexer-api"></a>チュートリアル:Video Indexer API の使用

Video Indexer は、Microsoft が提供するさまざまなオーディオおよびビデオの人工知能 (AI) テクノロジが 1 つに統合されたサービスです。このサービスを利用すると、開発がより簡単になります。 API は、開発者がクラウド プラットフォームの規模、世界的な展開、可用性、信頼性を気にすることなく、Media AI テクノロジの使用に集中できるように設計されています。 この API を使用して、ファイルをアップロードしたり、ビデオの詳細な分析情報を取得したり、埋め込み可能な分析情報ウィジェットやプレーヤー ウィジェットの URL を取得したりできます。

Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer を使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される](connect-to-azure.md) Video Indexer アカウントを作成します。 インデックス作成にかかった時間 (分) に対して支払います。詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。

この記事では、開発者が [Video Indexer API](https://api-portal.videoindexer.ai/) を利用する方法について説明します。

## <a name="subscribe-to-the-api"></a>API にサブスクライブする

1. [Video Indexer 開発者ポータル](https://api-portal.videoindexer.ai/)にサインインします。

    [ログイン情報](release-notes.md#october-2020)に関するリリース ノートを確認します。
    
     ![Video Indexer 開発者ポータルにサインインする](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * Video Indexer へのサインアップ時と同じプロバイダーを使用する必要があります。
   > * 個人用の Google アカウントと Microsoft (Outlook/Live) アカウントは試用アカウントにのみ使用できます。 Azure に接続するアカウントには、Azure AD が必要です。
   > * 1 つのメール アドレスで有効にすることができるアカウントは 1 つのみです。 ユーザーが user@gmail.com を使用して LinkedIn にサインインし、後から user@gmail.com を使用して Google にサインインしようとすると、ユーザーは既に存在しているというエラー ページが表示されます。
2. サブスクライブします。

    [[製品]](https://api-portal.videoindexer.ai/products) タブを選択します。次に、[承認] を選択し、サブスクライブします。
    
    ![Video Indexer 開発者ポータルの [製品] タブ](./media/video-indexer-use-apis/authorization.png)

    > [!NOTE]
    > 新しいユーザーは自動的に Authorization にサブスクライブされます。
    
    サブスクライブした後は、 **[製品]**  ->  **[承認]** の下にサブスクリプションが表示されます。 サブスクリプションのページには、プライマリとセカンダリ キーが表示されます。 キーは保護する必要があります。 キーはサーバー コードでのみ使用してください。 クライアント側 (.js、.html など) では使用できないようにします。

    ![Video Indexer 開発者ポータルでのサブスクリプションとキー](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> Video Indexer ユーザーは、単一のサブスクリプション キーを使用して複数の Video Indexer アカウントに接続することができます。 さらに、それらの Video Indexer アカウントを異なる Media Services アカウントにリンクさせることができます。

## <a name="obtain-access-token-using-the-authorization-api"></a>Authorization API を使用してアクセス トークンを取得する

Authorization API にサブスクライブしたら、アクセス トークンを取得できます。 これらのアクセス トークンは、Operations API の認証を受けるために使用されます。

Operations API の各呼び出しは、呼び出しの承認スコープと一致するアクセス トークンに関連付けられている必要があります。

- ユーザー レベル:ユーザー レベルのアクセス トークンを使用すると、**ユーザー** レベルに対して操作を実行できます。 たとえば、関連付けられたアカウントの取得などです。
- アカウント レベル:アカウント レベルのアクセス トークンを使用すると、**アカウント** レベルまたは **ビデオ** レベルに対して操作を実行できます。 たとえば、ビデオのアップロード、すべてのビデオの一覧表示、ビデオの分析情報の取得などです。
- ビデオ レベル:ビデオ レベルのアクセス トークンを使用すると、特定の **ビデオ** に対して操作を実行できます。 たとえば、ビデオの分析情報、キャプションのダウンロード、ウィジェットの取得などです。

**allowEdit=true/false** を指定することで、これらのトークンを読み取り専用にするか編集を許可するかを制御できます。

**アカウント** 操作と **ビデオ** 操作の両方がカバーされるので、ほとんどのサーバー間シナリオにおそらく同じ **アカウント** トークンを使用します。 ただし、クライアント側 (JavaScript など) から Video Indexer を呼び出す予定がある場合は、クライアントがアカウント全体へのアクセス権を取得できないように、**ビデオ** アクセス トークンを使用することもあります。 また、(たとえば、**Get Insights ウィジェット** または **Get Player ウィジェット** を使用して) VideoIndexer クライアント コードをクライアントに埋め込む場合にも、**ビデオ** アクセス トークンを指定する必要があります。

わかりやすくするために、ユーザー トークンを最初に取得せずに、**Authorization** API の **GetAccounts** を使用してアカウントを取得することができます。 有効なトークンを持つアカウントの取得を要求することもできます。これで、アカウント トークンを取得する追加の呼び出しをスキップすることができます。

アクセス トークンの有効期間は 1 時間です。 Operations API を使用する前に、アクセス トークンが有効であることを確認してください。 期限切れの場合は、もう一度 Authorization API を呼び出して新しいアクセス トークンを取得します。

これで API との統合を開始する準備が整いました。 [各 Video Indexer REST API の詳細な説明](https://api-portal.videoindexer.ai/)を参照してください。

## <a name="account-id"></a>Account ID

アカウント ID パラメーターは、すべての操作 API 呼び出しに必要です。 アカウント ID は、次のいずれかの方法で取得できる GUID です。

* **Video Indexer** Web サイトを使用して、アカウント ID を取得します。

    1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
    2. **[設定]** ページを表示します。
    3. アカウント ID をコピーします。

        ![Video Indexer の設定とアカウント ID](./media/video-indexer-use-apis/account-id.png)

* **Video Indexer 開発者ポータル** を使用して、プログラムからアカウント ID を取得します。

    [Get account](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account?) API を使用します。

    > [!TIP]
    > `generateAccessTokens=true` を定義することで、アカウントのアクセス トークンを生成できます。

* アカウントのプレーヤー ページの URL からアカウント ID を取得します。

    ビデオを見ると、`accounts` セクションの後と `videos` セクションの前に ID が表示されます。

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recommendations

このセクションでは、Video Indexer API を使用する際の推奨事項をいくつか示します。

- ビデオをアップロードする予定がある場合は、ファイルをパブリック ネットワークの場所 (Azure Blob Storage アカウントなど) に配置することをお勧めします。 ビデオのリンクを取得し、アップロード ファイルのパラメーターとしてその URL を指定します。

    Video Indexer に指定する URL は、メディア (オーディオまたはビデオ) ファイルを指している必要があります。 URL (または SAS URL) をブラウザーに貼り付けると、簡単に確認できます。ファイルの再生またはダウンロードが開始された場合は、おそらく適切な URL です。 ブラウザーに何かが描画される場合は、おそらく、ファイルではなく HTML ページへのリンクです。

- 指定されたビデオのビデオ分析情報を取得する API を呼び出すと、応答コンテンツとして詳細な JSON 出力を取得できます。 [返される JSON の詳細については、こちらのトピックを参照してください](video-indexer-output-json-v2.md)。

## <a name="code-sample"></a>コード サンプル

次の C# コード スニペットは、すべての Video Indexer API の使用方法を示しています。

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了したら、使用する予定がないリソースを削除します。

## <a name="see-also"></a>関連項目

- [Video Indexer の概要](video-indexer-overview.md)
- [リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>次のステップ

- [出力 JSON の詳細を調べる](video-indexer-output-json-v2.md)
- こちらの[サンプル コード](https://github.com/Azure-Samples/media-services-video-indexer)をご覧ください。動画のアップロードとインデックス作成という重要な作業が説明されます。 このコードからは、API の基本機能の使い方がよくわかります。 必ずインライン コメントを読み、ベスト プラクティスに関する助言を確認してください。

