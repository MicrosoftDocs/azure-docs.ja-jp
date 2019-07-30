---
title: Azure Media Services v3 によるライブ ストリーミング | Microsoft Docs
description: このチュートリアルでは、Media Services v3 を使用してライブ ストリーム配信を行う方法について順を追って説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 5028fd4179f19634b41bb46a5f6df40f36cc8e29
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275574"
---
# <a name="tutorial-stream-live-with-media-services"></a>チュートリアル:Media Services によるライブ ストリーム配信

> [!NOTE]
> このチュートリアルでは [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) の例を使用していますが、全体的な手順は [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)、またはその他のサポートされている [SDK](media-services-apis-overview.md#sdks) で同じです。

Azure Media Services では、[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)がライブ ストリーミング コンテンツの処理を受け持ちます。 ライブ イベントは入力エンドポイントであり、その取り込み URL をライブ エンコーダーに対して指定します。 ライブ イベントは、ライブ エンコーダーからライブ入力ストリームを受け取り、1 つまたは複数の[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints)を介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、ライブ イベントはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します。 このチュートリアルでは、.NET Core を使用してライブ イベントの**パススルー** タイプを作成、管理する方法について説明します。 

このチュートリアルでは、次の操作方法について説明します。    

> [!div class="checklist"]
> * このトピックで説明されているサンプル アプリをダウンロードする
> * ライブ ストリーミングを実行するコードを確認する
> * [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) (https://ampdemo.azureedge.net ) でイベントを視聴する
> * リソースのクリーンアップ

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

- Visual Studio Code または Visual Studio をインストールします。
- [Media Services アカウントを作成する](create-account-cli-how-to.md)<br/>Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](access-api-cli-how-to.md)」の手順に従い、資格情報を保存します。 API にアクセスするために必要となります。
- イベントのブロードキャストに使用するカメラまたはデバイス (ラップトップなど)。
- カメラからの信号を Media Services ライブ ストリーミング サービスに送信されるストリームに変換するオンプレミスのライブ エンコーダー。 ストリームは **RTMP** または **Smooth Streaming** 形式である必要があります。

> [!TIP]
> 先に進む前に、「[Live streaming with Media Services v3](live-streaming-overview.md)」(Media Services v3 によるライブ ストリーミング) を確認してください。 

## <a name="download-and-configure-the-sample"></a>サンプルをダウンロードして構成する

次のコマンドを使って、ストリーム配信の .NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

ライブ ストリーミングのサンプルは [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) フォルダーにあります。

ダウンロードしたプロジェクトに含まれる [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) を開きます。 [API へのアクセス](access-api-cli-how-to.md)に関するページで取得した資格情報の値に置き換えます。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数のライブ イベントが作成されます。 <br/>実行中のライブ イベントを必ず停止してください。 そうしないと、**課金**が発生します。

## <a name="examine-the-code-that-performs-live-streaming"></a>ライブ ストリーミングを実行するコードを確認する

このセクションでは、*MediaV3LiveApp* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) ファイルで定義されている関数を調べます。

サンプルをクリーンアップせずに複数回実行しても名前の競合が発生しないように、サンプルは各リソースに対して一意のサフィックスを作成します。

> [!IMPORTANT]
> このサンプルでは、各リソースに一意のサフィックスを使用します。 デバッグをキャンセルした場合、または完全に実行せずにアプリを終了した場合、アカウントに複数のライブ イベントが作成されます。 <br/>
> 実行中のライブ イベントを必ず停止してください。 そうしないと、**課金**が発生します。
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>ライブ イベントを作成する

このセクションは、**パススルー** タイプの (LiveEventEncodingType が None に設定されている) ライブ イベントを作成する方法を示します。 使用可能なライブ イベントの種類について詳しくは、「[ライブ イベントの種類](live-events-outputs-concept.md#live-event-types)」をご覧ください。 
 
ライブ イベントを作成するときは、次のことを指定します。

* Media Services の場所 
* ライブ イベントのストリーミング プロトコル (現時点では、RTMP プロトコルと Smooth Streaming プロトコルがサポートされます)<br/>ライブ イベントや、それに関連付けられたライブ出力の実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のライブ イベントを作成する必要があります。  
* 取り込みやプレビューの IP 制限。 このライブ イベントへのビデオの取り込みが許可される IP アドレスを定義できます。 許可される IP アドレスは、1 つの IP アドレス (例: '10.0.0.1')、IP アドレスと CIDR サブネット マスクを使用した IP 範囲 (例: '10.0.0.1/22')、IP アドレスとピリオドで区切られた 10 進数のサブネット マスクを使用した IP 範囲 (例: '10.0.0.1(255.255.252.0)') のいずれかの形で指定できます。<br/>IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。<br/>IP アドレスの形式は、4 つの数字を含む IpV4 アドレスまたは CIDR アドレス範囲のどちらかである必要があります。
* イベントの作成中に、そのイベントを自動開始するように設定できます。 <br/>自動開始が true に設定されている場合、ライブ イベントは作成された後に開始されます。 つまり、ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
* 取り込み URL を予測的にするには、"バニティ" モードを設定します。 詳細については、「[ライブ イベントの取り込み URL](live-events-outputs-concept.md#live-event-ingest-urls)」を参照してください。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>取り込み URL の取得

ライブ イベントが作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>プレビュー URL を取得する

previewEndpoint を使用して、エンコーダーからの入力が実際に受信されていることをプレビューおよび確認します。

> [!IMPORTANT]
> 続行する前に、ビデオがプレビュー URL に送信されていることを確認してください。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>ライブ イベントとライブ出力を作成して管理する

ストリームがライブ イベントに流れ始めると、資産、ライブ出力、ストリーミング ロケーターを作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、ストリーミング エンドポイントを介して視聴者がストリームを使用できるようになります。 

#### <a name="create-an-asset"></a>資産を作成する

ライブ出力が使用する資産を作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>ライブ出力を作成する

ライブ出力は作成すると開始され、削除されると停止します。 ライブ出力を削除しても、基になる資産と資産のコンテンツは削除されません。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

> [!NOTE]
> Media Services アカウントの作成時に、**既定**のストリーミング エンドポイントが**停止**状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、[ダイナミック パッケージ](dynamic-packaging-overview.md)と動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

ストリーミング ロケーターを使用してライブ出力資産を発行した場合、ライブ イベント (DVR ウィンドウの長さまで) は、ストリーミング ロケーターの有効期限まで、または削除するまで、どちらか早い方のタイミングまで引き続き表示できます。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

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
* ライブ イベントのアーカイブをオンデマンド ストリームとして提供し続けるのでない限り、ストリーミング エンドポイントを停止できます。 ライブ イベントが停止状態の場合は、いかなる課金も発生しません。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

次のコードは、すべてのライブ イベントのアカウントをクリーンアップする方法を示しています。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>イベントの視聴

イベントを視聴するには、「ストリーミング ロケーターを作成する」に記述されたコードを実行したときに取得したストリーミング URL をコピーし、適当なプレーヤーを使用します。 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) (https://ampdemo.azureedge.net ) を使用して、ストリームをテストできます。 

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。 イベントを停止して削除した後も、アセットを削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> ライブ イベントを実行したままにすると課金が発生します。 プロジェクトやプログラムがクラッシュする、またはなんらかの理由で閉じられると、ライブ イベントが課金状態で実行されたままになるため、ご注意ください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問する。フィードバックする。最新情報を入手する

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次の手順

[ファイルをストリーミングする](stream-files-tutorial-with-api.md)
 
