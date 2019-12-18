---
title: AES-128 によるビデオの暗号化
titleSuffix: Azure Media Services
description: Azure Media Service で AES 128 ビット暗号化によりビデオを暗号化し、キー配信サービスを使用する方法を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974174"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>チュートリアル:AES-128 でビデオを暗号化し、キー配信サービスを使用する

> [!NOTE]
> このチュートリアルでは [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) の例を使用していますが、全体的な手順は [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)、またはその他のサポートされている [SDK](media-services-apis-overview.md#sdks) で同じです。

Media Services では、AES 128 ビット暗号化キーを使用して暗号化された HTTP ライブ ストリーミング (HLS)、MPEG-DASH、およびスムーズ ストリーミングを配信できます。 Media Services には、権限のあるユーザーに暗号化キーを配信する、キー配信サービスも用意されています。 Media Services でビデオを動的に暗号化したい場合、暗号化キーをストリーミング ロケーターに関連付け、コンテンツ キー ポリシーを構成します。 プレーヤーによってストリームが要求されると、Media Services では指定されたキーを使用して、AES-128 によってコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーによるキーの取得が承認されているかどうかを判断するために、キーに指定されたコンテンツ キー ポリシーがサービスにより評価されます。

複数の暗号化の種類 (AES-128、PlayReady、Widevine、FairPlay) を使用して各アセットを暗号化することができます。 合理的な組み合わせについては、「[ストリーミング プロトコルと暗号化の種類](content-protection-overview.md#streaming-protocols-and-encryption-types)」を参照してください。 また、[DRM による保護方法](protect-with-drm.md)に関するページも参照してください。

この記事のサンプルからの出力には、Azure Media Player への URL、マニフェスト URL、およびコンテンツを再生するのに必要な AES トークンが含まれます。 このサンプルでは、JSON Web Token (JWT) トークンの有効期限を 1 時間に設定しています。 ブラウザーを開いて結果の URL を貼り付けると、URL とトークンが既に入力されている Azure Media Player のデモ ページが起動します (形式は次のとおりです: ```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```)。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * この記事で説明する [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) サンプルをダウンロードする。
> * .NET SDK で Media Services API の使用を開始する。
> * 出力アセットを作成する。
> * エンコード Transform を作成する。
> * ジョブを送信する。
> * ジョブが完了するのを待つ。
> * コンテンツ キー ポリシーを作成する
> * ポリシーを構成して JWT トークンの制限を使用する。
> * ストリーミング ロケーターを作成する。
> * ストリーミング ロケーターを構成して AES (ClearKey) によってビデオを暗号化する。
> * テスト トークンを取得する。
> * ストリーミング URL を作成する。
> * リソースをクリーンアップする。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

* 「[コンテンツ保護の概要](content-protection-overview.md)」の記事を確認します。
* Visual Studio Code または Visual Studio をインストールします。
* [Media Services アカウントを作成する](create-account-cli-quickstart.md)
* [API へのアクセス](access-api-cli-how-to.md)に関するページに従って、Media Services API を使用するために必要な資格情報を入手します。

## <a name="download-code"></a>コードをダウンロードする

次のコマンドを使用して、この記事で紹介した完全な .NET サンプルが含まれる GitHub リポジトリを、お使いのマシンに複製します。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

"AES-128 を使用した暗号化" のサンプルは、[EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) フォルダーにあります。

> [!NOTE]
> このサンプルでは、アプリを実行するたびに一意のリソースが作成されます。 通常は、変換やポリシーなどの既存のリソースを再利用します (既存のリソースが必要な構成を備えている場合)。

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成します。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>出力アセットを作成する  

出力[アセット](https://docs.microsoft.com/rest/api/media/assets)には、対象のエンコード ジョブの結果が格納されます。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>エンコード Transform を取得または作成する

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように **TransformOutput** オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この記事で説明されているサンプルでは、**AdaptiveStreaming** という名前の組み込みプリセットを使っています。 この Preset は、入力ビデオを入力の解像度とビットレートに基づいて自動生成されるビットレート ラダー (ビットレートと解像度のペア) にエンコードし、その後ビットレートと解像度の各ペアに対応する、H.264 ビデオと AAC オーディオを含む ISO MP4 ファイルを生成します。

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) を作成する前に、次のコードに示すように、まず **Get** メソッドを使って変換が既に存在するかどうかを確認します。 Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>ジョブの送信

上で説明したように、[Transform](https://docs.microsoft.com/rest/api/media/transforms) オブジェクトはレシピであり、[Job](https://docs.microsoft.com/rest/api/media/jobs) は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。

このチュートリアルでは、[HTTPs ソース URL](job-input-from-http-how-to.md) から直接取り込んだファイルに基づいてジョブの入力を作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブが完了するまでに、一定の時間がかかります。 完了したら通知を受け取りたいでしょう。 次のコード例では、[ジョブ](https://docs.microsoft.com/rest/api/media/jobs)の状態をサービスに対してポーリングする方法を示します。 待機時間が発生する可能性があるため、ポーリングは運用アプリに推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。 詳細については、[カスタム Web エンドポイントにイベントをルーティングする方法](job-state-events-cli-how-to.md)に関するページを参照してください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態が返されます。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>コンテンツ キー ポリシーを作成する

コンテンツ キーにより、アセットに安全にアクセスすることができます。 エンド クライアントにコンテンツ キーを配信する方法を構成する**コンテンツ キー ポリシー**を作成する必要があります。 コンテンツ キーは、**ストリーミング ロケーター**に関連付けられます。 Media Services には、権限のあるユーザーに暗号化キーを配信する、キー配信サービスも用意されています。

プレーヤーがストリームを要求すると、Media Services は、指定されたキーを使用してコンテンツを動的に暗号化します (この場合は AES 暗号化を使用します)。ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーによるキーの取得が承認されているかどうかを判断するために、キーに指定されたコンテンツ キー ポリシーがサービスにより評価されます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

エンコードが完了し、コンテンツ キー ポリシーを設定したら、次に、出力アセット内のビデオを、クライアントが再生に利用できるようにします。 ビデオを公開するには、次の 2 つの手順を実行します。

1. [ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)を作成します。
2. クライアントが使用できるストリーミング URL を作成します。

**ストリーミング ロケーター**を作成するプロセスは、発行と呼ばれます。 既定では、API 呼び出しを行った直後に**ストリーミング ロケーター**が有効になります。 オプションの開始時間と終了時間を構成しない限り、その状態は削除されるまで継続されます。

[ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)を作成するときは、使用する **StreamingPolicyName** を指定する必要があります。 このチュートリアルでは、PredefinedStreamingPolicies の 1 つを使用します。これは、ストリーミングのためにコンテンツを発行する方法を Azure Media Services に指示します。 この例では、AES エンベロープ暗号化が適用されます (DRM ライセンスではなく HTTPS 経由でキーが再生クライアントに配信されるため、この暗号化は ClearKey 暗号化とも呼ばれます)。

> [!IMPORTANT]
> カスタム [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いのストリーミング ロケーターに対してそのセットを再利用する必要があります。 Media Service アカウントには、StreamingPolicy エントリの数に対するクォータがあります。 ストリーミング ロケーターごとに新しい StreamingPolicy を作成しないでください。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>テスト トークンを取得する

このチュートリアルでは、トークン制限があるコンテンツ キー ポリシーを指定します。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では [JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) 形式のトークンがサポートされており、サンプルではこの形式を構成しています。

ContentKeyIdentifierClaim は **ContentKeyPolicy** 内で使用されます。つまり、キー配信サービスに提示されるトークンに、コンテンツ キーの識別子が含まれている必要があります。 サンプルでは、ストリーミング ロケーターの作成時にコンテンツ キーを指定していません。システムによってランダムなコンテンツ キーが作成されます。 テスト トークンを生成するには、ContentKeyIdentifierClaim 要求に含める ContentKeyId を取得する必要があります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH ストリーミング URL を作成する

[ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)が作成されたので、ストリーミング URL を取得できます。 URL を作成するには、[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) のホスト名と**ストリーミング ロケーター**のパスを連結する必要があります。 このサンプルでは、"*既定の*" **ストリーミング エンドポイント**を使っています。 最初に Media Service アカウントを作成したとき、この "*既定の*" **ストリーミング エンドポイント**は停止状態になっているので、**Start** を呼び出す必要があります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform、ストリーミング ロケーターなどは再利用します)。 実験後にアカウントをクリーン アップする場合は、再利用する予定のないリソースを削除します。 たとえば、次のコードでジョブを削除します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>質問する。フィードバックする。最新情報を入手する

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. が提供するサービスで、Google Inc. のサービス利用規約とプライバシーポリシーが適用されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [DRM での保護](protect-with-drm.md)
