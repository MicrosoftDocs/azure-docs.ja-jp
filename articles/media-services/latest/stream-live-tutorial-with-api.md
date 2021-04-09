---
title:Media Services v3 によるライブ ストリーム配信:Azure Media Services の説明:Azure Media Services v3 を使ってライブ ストリーム配信する方法について説明します。
services: media-services documentationcenter: '' author:IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: media ms.tgt_pltfrm: na ms.devlang: na ms.topic: tutorial ms.custom: "mvc, devx-track-csharp" ms.date:06/13/2019 ms.author: inhenkel

---

# <a name="tutorial-stream-live-with-media-services"></a>チュートリアル:Media Services を使用したライブ ストリーム配信

> [!NOTE]
> このチュートリアルでは [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) の例を使用していますが、全体的な手順は [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event)、またはその他のサポートされている [SDK](media-services-apis-overview.md#sdks) で同じです。 

Azure Media Services では、[ライブ イベント](/rest/api/media/liveevents)がライブ ストリーミング コンテンツの処理を受け持ちます。 ライブ イベントは入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 ライブ イベントは、ライブ エンコーダーからライブ入力ストリームを受け取り、1 つまたは複数の[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、ライブ イベントはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します。 このチュートリアルでは、.NET Core を使用してライブ イベントの **パススルー** タイプを作成、管理する方法について説明します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * このトピックで説明されているサンプル アプリをダウンロードする。
> * ライブ ストリーミングを実行するコードを確認する。
> * [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) ([https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)) でイベントを視聴する。
> * リソースをクリーンアップする。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには以下が必要です。

- Visual Studio Code または Visual Studio をインストールします。
- [Media Services アカウントを作成する](./create-account-howto.md)<br/>API アクセスの詳細情報 (JSON 形式) をコピーするか、Media Services アカウントに接続するうえで必要な値をこのサンプルで使用する .env ファイルの書式に保存しておくようにしてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](./access-api-howto.md)」の手順に従い、資格情報を保存します。 これらは、このサンプルの API にアクセスする際や、 env ファイルの書式に入力する際に必要になります。 
- イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
- カメラ ストリームをエンコードし、RTMP プロトコルを使って Media Services のライブ ストリーミング サービスに送信するオンプレミスのソフトウェア エンコーダー ([お勧めのオンプレミスのライブ エンコーダー](recommended-on-premises-live-encoders.md)に関するページを参照してください)。 ストリームは **RTMP** または **Smooth Streaming** 形式である必要があります。  
- このサンプルには、シンプルに作業を開始するという観点から、最初は無料の [Open Broadcaster Software OBS Studio](https://obsproject.com/download) のようなソフトウェア エンコーダーを使用することをお勧めします。 

> [!TIP]
> 先に進む前に、「[Live streaming with Media Services v3](live-streaming-overview.md)」(Media Services v3 によるライブ ストリーミング) を確認してください。 

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使って、ライブ ストリーミングの .NET サンプルが含まれる次の GitHub リポジトリを、お使いのマシンに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

ライブ ストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live) フォルダーにあります。

ダウンロードしたプロジェクトに含まれる [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/appsettings.json) を開きます。 [API へのアクセス](./access-api-howto.md)に関するページで取得した資格情報の値に置き換えます。

このほか、プロジェクトのルートにある .env ファイルの書式を使用して、.NET サンプルのリポジトリにある全プロジェクトに一括で環境変数を設定することもできます。 sample.env ファイルをコピーし、Azure portal の Media Services API アクセス ページまたは Azure CLI から入手した情報を入力します。  sample.env は、あらゆるプロジェクトで利用できるようにするために、ファイル名を ".env" に変更します。
.gitignore ファイルについては、フォークしたリポジトリにファイルの内容を公開しないようにする構成が済んでいます。 

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数のライブ イベントが作成されます。 <br/>実行中のライブ イベントを必ず停止してください。 そうしないと、**料金が発生** します。

## <a name="examine-the-code-that-performs-live-streaming"></a>ライブ ストリーミングを実行するコードを確認する

このセクションでは、*LiveEventWithDVR* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) ファイルに定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。


### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の冒頭で複製したコードの **GetCredentialsAsync** 関数により、ローカルの構成ファイル (appsettings.json) またはリポジトリのルートにある .env 環境変数ファイルの中で指定されている資格情報に基づいて ServiceClientCredentials オブジェクトが作成されます。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションは、**パススルー** タイプの (LiveEventEncodingType が None に設定されている) ライブ イベントを作成する方法を示します。 他の使用可能なライブ イベントの種類の詳細については、「[ライブ イベントの種類](live-events-outputs-concept.md#live-event-types)」を参照してください。 パススルーのほかには、720p または 1080p のアダプティブ ビットレート クラウド エンコードのためのライブ コード変換ライブ イベントを使用できます。 
 
ライブ イベントを作成するときは、次のことを指定します。

* ライブ イベントの取り込みプロトコル (現時点では、RTMP(S) プロトコルと Smooth Streaming プロトコルがサポートされます)。<br/>ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成する必要があります。  
* 取り込みやプレビューの IP 制限。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。<br/>IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。<br/>IP アドレスの形式は、4 つの数字を含む IpV4 アドレスか CIDR アドレス範囲にする必要があります。
* イベントの作成時に、そのイベントを自動開始するように設定できます。 <br/>自動開始が true に設定されている場合、ライブ イベントは作成された後に開始されます。 つまり、ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
このほか、各種のスタンバイ モードも利用できます。スタンバイ モードを使うと、"実行中" 状態にすばやく移行できる低コストの "割り当て済み" 状態でライブ イベントを開始できます。 これは、ホットプールのようにストリーマーにすばやくチャンネルを渡す必要がある局面で便利です。
* ハードウェアベースのライブ エンコーダーで取り込み URL の予測やメンテナンスをしやすくする場合には、"useStaticHostname" プロパティを true に設定します。 詳細については、「[ライブ イベントの取り込み URL](live-events-outputs-concept.md#live-event-ingest-urls)」を参照してください。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>取り込み URL の取得

ライブ イベントが作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

previewEndpoint を使用して、エンコーダーからの入力が実際に受信されていることをプレビューおよび確認します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>ライブ イベントとライブ出力を作成して管理する

ストリームがライブ イベントに流れ始めると、アセット、ライブ出力、ストリーミング ロケーターを作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、ストリーミング エンドポイントを介して視聴者がストリームを使用できるようになります。

ここに挙げた概念を学ぶにあたっては、"アセット" オブジェクトというものを、かつてビデオ テープ レコーダーに挿入していたテープにたとえて考えてみるのが良いでしょう。 "ライブ出力" は、テープ レコーダー マシンに相当します。 "ライブ イベント" は、マシンの背面に届くビデオ信号です。

まずは、信号となる "ライブ イベント" を作成します。  この信号は、ライブ イベントを開始し、エンコーダーを入力に接続するまで流れることはありません。

テープの作成は、いつでも問題ありません。 これは、ライブ出力オブジェクト (この比喩で言うならテープ レコーダー) に渡す空の "アセット" でしかありません。

テープ レコーダーを作成する時期も、いつでもかまいません。 つまり、ライブ出力の作成は、信号を流し始める前でも、後でも問題ありません。 物事を早く進める必要がある場合には、信号を流し始める前に作成しておくと良いでしょう。

テープ レコーダーを停止するときは、ライブ出力を対象とした delete を呼び出します。 これによりテープ、つまり "アセット" の中身が削除されることはありません。  アセットは、明示的にそのアセット自体を対象とした delete を呼び出さないかぎり、その中に保存されているビデオ コンテンツともども、そのまま保持されます。

次のセクションでは、アセット ("テープ") とライブ出力 ("テープ レコーダー") の作成手順を説明します。

#### <a name="create-an-asset"></a>アセットを作成する

ライブ出力が使用するアセットを作成します。 これは、上の比喩で言うと、ライブ ビデオの信号を記録するテープに相当します。 視聴者は、この仮想のテープからコンテンツをライブまたはオンデマンドで見ることができます。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>ライブ出力を作成する

ライブ出力は作成すると開始され、削除されると停止します。 これは、イベントの "テープ レコーダー" になります。 ライブ出力を削除しても、基になるアセットやアセット内のコンテンツが削除されることはありません。 テープを取り出しているようなものと考えてください。 アセットとそこに記録されている内容は、好きな期間だけ残しておくことができます。アセットを取り出すと (つまり、ライブ出力を削除すると)、そのアセットはすぐに、オンデマンドで視聴できる状態になります。 

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

> [!NOTE]
> Media Services アカウントの作成時に、**既定の** ストリーミング エンドポイントが **停止** 状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](dynamic-packaging-overview.md)と動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが **実行中** 状態である必要があります。

ストリーミング ロケーターを使用してアセットを公開した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限またはストリーミング ロケーターを削除するまでの、どちらか早い方のタイミングまで引き続き表示できます。 このようにして、視聴者が仮想の "テープ" レコーディングをライブまたはオンデマンドで視聴できるようにします。 ライブ イベント、DVR ウィンドウ、またはレコーディングが完了した (ライブ出力が削除された) 場合のオンデマンド アセットの視聴には、同じ URL を使用できます。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

ストリーミング イベントが完了し、以前にプロビジョニングされたリソースをクリーンアップする場合は、次の手順に従います。

* エンコーダーからのストリームのプッシュを停止します。
* ライブ イベントを停止します。 ライブ イベントが停止した後は、いかなる課金も発生しません。 もう一度開始する必要がある場合、取り込み URL は同一になるため、エンコーダーを再構成する必要はありません。
* ライブ イベントのアーカイブをオンデマンド ストリームとして提供し続けるのでない限り、ストリーミング エンドポイントを停止できます。 ライブ イベントが停止状態の場合は、料金は発生しません。

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>イベントの視聴

イベントを監視するには、「ストリーミング ロケーターを作成する」で説明されているコードを実行したときに表示されたストリーミング URL をコピーします。 任意のメディア プレーヤーを使用できます。 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) は、https://ampdemo.azureedge.net でのストリームをテストするために使用できます。

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後でも、アセットを削除しない限り、ユーザーはアーカイブされたコンテンツをビデオ オン デマンドとしてストリーム配信できます。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> ライブ イベントを実行したままにすると課金が発生します。 プロジェクトやプログラムがクラッシュする、またはなんらかの理由で閉じられると、ライブ イベントが課金状態で実行されたままになるため、ご注意ください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)
 
