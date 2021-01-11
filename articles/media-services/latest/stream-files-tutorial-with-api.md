---
title: Media Services v3 を使用してアップロード、エンコード、ストリーム配信する
titleSuffix: Azure Media Services
description: Azure Media Services v3 を使用してファイルをアップロードし、ビデオをエンコードし、コンテンツをストリーム配信する方法を示すチュートリアルです。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: eedbb63f4928c0397150b40a47fdc7c3e87d1991
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256771"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>チュートリアル:Media Services v3 を使用してビデオをアップロード、エンコード、ストリーム配信する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> このチュートリアルでは [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) の例を使用していますが、全体的な手順は [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event?view=azure-cli-latest)、またはその他のサポートされている [SDK](media-services-apis-overview.md#sdks) で同じです。

Azure Media Services を使うと、さまざまなブラウザーやデバイスで再生できる形式にメディア ファイルをエンコードできます。 たとえば、Apple の HLS または MPEG DASH 形式のコンテンツをストリーム配信することが必要な場合があります。 ストリーム配信する前に、高品質のデジタル メディア ファイルをエンコードする必要があります。 エンコードの詳細については、[エンコードの概念](encoding-concept.md)に関する記事を参照してください。 このチュートリアルでは、ローカルのビデオ ファイルをアップロードし、アップロードされたファイルをエンコードします。 HTTPS URL を使用してアクセスできるようにするコンテンツをエンコードすることもできます。 詳しくは、「[HTTP URL からジョブの入力を作成する」](job-input-from-http-how-to.md)をご覧ください。

![Azure Media Player でビデオを再生する](./media/stream-files-tutorial-with-api/final-video.png)

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * このトピックで説明されているサンプル アプリをダウンロードする。
> * アップロード、エンコード、およびストリーム配信するコードを調べる
> * アプリケーションを実行します。
> * ストリーミング URL をテストする
> * リソースをクリーンアップする。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- Visual Studio がインストールされていない場合は、[Visual Studio Community 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15) を入手できます。
- [Media Services アカウントを作成する](./create-account-howto.md)<br/>Media Services アカウント名、ストレージ名、およびリソース名として使用した値を覚えておいてください。
- 「[Azure CLI で Azure Media Services API にアクセスする](./access-api-howto.md)」の手順に従い、資格情報を保存します。 API にアクセスするために必要となります。

## <a name="download-and-set-up-the-sample"></a>サンプルをダウンロードして設定する

次のコマンドを使って、ストリーム配信の .NET サンプルが含まれる GitHub リポジトリを、お使いのコンピューターに複製します。  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

サンプルは、[UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles) フォルダーにあります。

ダウンロードしたプロジェクトに含まれる [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) を開きます。 [API へのアクセス](./access-api-howto.md)に関するページで取得した資格情報の値に置き換えます。

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>アップロード、エンコード、およびストリーム出力するコードを調べる

このセクションでは、*UploadEncodeAndStreamFiles* プロジェクトの [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) ファイルで定義されている関数を調べます。

サンプルは、次のアクションを実行します。

1. 新しい**変換**を作成します (最初に、指定された変換が存在するかどうかを確認します)。
2. エンコード **ジョブ**の出力として使用される出力**アセット**を作成します。
3. 入力**アセット**を作成し、指定されたローカル ビデオ ファイルをそこにアップロードします。 アセットは、ジョブの入力として使用されます。
4. 作成された入力と出力を使用してエンコード ジョブを送信します。
5. ジョブの状態を確認します。
6. **ストリーミング ロケーター**を作成します。
7. ストリーミング URL を作成します。

### <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>入力アセットを作成し、ローカル ファイルをそれにアップロードする

**CreateInputAsset** 関数は、新しい入力[アセット](/rest/api/media/assets)を作成し、指定されたローカル ビデオ ファイルをそこにアップロードします。 この**アセット**は、エンコード ジョブへの入力として使われます。 Media Services v3 では、**ジョブ**への入力としては、**アセット**を使うか、または HTTPS URL 経由で Media Services アカウントから使用できるようにされたコンテンツを使うことができます。 HTTPS URL からのエンコード方法については、[こちら](job-input-from-http-how-to.md)の記事を参照してください。

Media Services v3 では、Azure Storage API を使ってファイルをアップロードします。 次の .NET スニペットはその方法を示したものです。

後で示す関数は、次の処理を実行します。

* **アセット**を作成する。
* 書き込み可能な [SAS URL](../../storage/common/storage-sas-overview.md) を資産の[ストレージ内のコンテナー](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)に取得する。

    資産の [ListContainerSas](/rest/api/media/assets/listcontainersas) 関数を使用して SAS URL を取得する場合、複数の SAS URL が返されることに注意してください。これは、ストレージ アカウント キーがストレージ アカウントごとに 2 つ存在するためです。 ストレージ アカウントにキーが 2 つあるのは、ストレージ アカウント キーのシームレスなローテーションを可能にするためです (一方のキーを使用しながらもう一方のキーを変更した後、新しいキーの使用を開始し、その後もう一方のキーをローテーションするなど)。 1 つ目の SAS URL はストレージ キー 1 を、2 つ目の SAS URL はストレージ キー 2 を表します。
* SAS URL を使用してストレージ内のコンテナーにファイルをアップロードする。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>ジョブの結果を格納する出力アセットを作成する

出力[アセット](/rest/api/media/assets)には、対象のエンコード ジョブの結果が格納されます。 プロジェクトで定義されている **DownloadResults** 関数は、この出力アセットから "output" フォルダーに結果をダウンロードするので、取得したものを確認できます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>アップロードされたファイルをエンコードする変換とジョブを作成する

Media Services でコンテンツをエンコードまたは処理するときは、レシピとしてエンコード設定をセットアップするのが一般的なパターンです。 その後、**ジョブ**を送信してビデオにレシピを適用します。 新しいビデオごとに新しいジョブを送信することで、ライブラリ内のすべてのビデオにレシピを適用します。 Media Services でのレシピは**変換**と呼ばれます。 詳しくは、「[Transform と Job](./transforms-jobs-concept.md)」をご覧ください。 このチュートリアルで説明するサンプルでは、さまざまな iOS および Android デバイスにストリーム配信するために、ビデオをエンコードするレシピが定義されています。

#### <a name="transform"></a>変換

新しい [Transform](/rest/api/media/transforms) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように **TransformOutput** オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この記事で説明されているサンプルでは、**AdaptiveStreaming** という名前の組み込みプリセットを使っています。 プリセットは、入力ビデオを入力の解像度とビットレートに基づいて自動生成されるビットレート ラダー (ビットレートと解像度のペア) にエンコードし、ビットレートと解像度の各ペアに対応する、H.264 ビデオと AAC オーディオを含む ISO MP4 ファイルを生成します。 このプリセットについては、[ビットレート ラダーの自動生成](autogen-bitrate-ladder.md)に関するページをご覧ください。

組み込み EncoderNamedPreset またはカスタム プリセットを使用できます。 詳しくは、[エンコーダー プリセットをカスタマイズする方法](customize-encoder-presets-how-to.md)に関する記事をご覧ください。

[Transform](/rest/api/media/transforms) を作成するときは、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。 Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>ジョブ

上で説明したように、[Transform](/rest/api/media/transforms) オブジェクトはレシピであり、[Job](/rest/api/media/jobs) は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。

この例では、入力ビデオはローカル コンピューターからアップロードされています。 HTTPS URL からのエンコード方法については、[こちら](job-input-from-http-how-to.md)の記事を参照してください。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブの完了には時間がかかり、完了したら通知を受け取る必要があります。 次のコード例では、[ジョブ](/rest/api/media/jobs)の状態をサービスに対してポーリングする方法を示します。 待機時間が発生する可能性があるため、ポーリングは運用アプリに推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。

Event Grid は、高可用性、一貫したパフォーマンス、および動的スケーリングを目的に設計されています。 Event Grid では、アプリはほぼすべての Azure サービスやカスタム ソースのイベントをリッスンし、対応できます。 単純な HTTP ベースのリアクティブ イベント ハンドリングでは、インテリジェントなイベント フィルタリングやイベント ルーティングを使用して、効率的なソリューションを構築できます。  [カスタム Web エンドポイントへのイベントのルーティング](job-state-events-cli-how-to.md)に関するページをご覧ください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態を取得します。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

### <a name="get-a-streaming-locator"></a>ストリーミング ロケーターを取得する

エンコードが完了したら次に、出力アセット内のビデオを、クライアントが再生に使用できるようにします。 これを実現するには 2 つのステップがあります。最初に[ストリーミング ロケーター](/rest/api/media/streaminglocators)を作成し、次にクライアントが使用できるストリーミング URL を作成します。

**ストリーミング ロケーター** を作成するプロセスは発行と呼ばれます。 既定では、**ストリーミング ロケーター** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。

[StreamingLocator](/rest/api/media/streaminglocators) を作成するときは、使用する **StreamingPolicyName** を指定する必要があります。 この例では、クリアなコンテンツ (暗号化されていないコンテンツ) をストリーム配信するので、定義済みのクリア ストリーミング ポリシー **PredefinedStreamingPolicy.ClearStreamingOnly** が使用されます。

> [!IMPORTANT]
> カスタム [ストリーミング ポリシー](/rest/api/media/streamingpolicies)を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 Media Service アカウントには、ストリーミング ポリシー エントリの数に対するクォータがあります。 ストリーミング ロケーターごとに新しいストリーミング ポリシーを作成しないでください。

次のコードでは、一意の locatorName で関数を呼び出しているものとします。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

このトピックのサンプルではストリーム配信について説明していますが、同じ呼び出しを使って、プログレッシブ ダウンロードによるビデオ配信用のストリーミング ロケーターを作成できます。

### <a name="get-streaming-urls"></a>ストリーミング URL を取得する

[ストリーミング ロケーター](/rest/api/media/streaminglocators)が作成されたので、**GetStreamingURLs** で示されているように、ストリーミング URL を取得できます。 URL を作成するには、[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)のホスト名と**ストリーミング ロケーター** パスを連結する必要があります。 このサンプルでは、*既定の* **ストリーミング エンドポイント**を使っています。 最初に Media Service アカウントを作成したとき、この*既定の* **ストリーミング エンドポイント**は停止状態になっているので、**Start** を呼び出す必要があります。

> [!NOTE]
> このメソッドでは、出力アセットの**ストリーミング ロケーター**を作成するときに使った locatorName が必要です。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform は再利用し、StreamingLocator などは保持します)。 実験後にアカウントをクリーン アップする場合は、再利用する予定のないリソースを削除します。 たとえば、ジョブ、作成された資産、コンテンツ キー ポリシーは、次のコードで削除します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

1. Ctrl + F5 キーを押して、*EncodeAndStreamFiles* アプリを実行します。
2. ストリーミング URL の 1 つをコンソールからコピーします。

この例では、別のプロトコルでビデオを再生するために使用できる URL が表示されます。

![Media Services ストリーミング ビデオの URL を表示する出力例](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>ストリーミング URL をテストする

ストリーム配信をテストするため、この記事では Azure Media Player を使います。

> [!NOTE]
> プレーヤーが HTTPS サイトでホストされている場合は、忘れずに URL を "https" に更新してください。

1. Web ブラウザーを開いて、[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) にアクセスします。
2. **[URL:]** ボックスに、アプリを実行したときに取得したストリーム配信 URL 値のいずれかを貼り付けます。
3. **[Update Player]\(プレーヤーの更新\)** を選択します。

Azure Media Player はテストには使用できますが、運用環境では使わないでください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>マルチスレッド

Azure Media Services v3 SDK は、スレッドセーフではありません。 マルチスレッド アプリを開発するときは、スレッドごとに新しい AzureMediaServicesClient オブジェクトを生成して使用する必要があります。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

ビデオをアップロード、エンコード、ストリーム配信する方法がわかったので、次の記事を参照してください。 

> [!div class="nextstepaction"]
> [ビデオを分析する](analyze-videos-tutorial-with-api.md)
