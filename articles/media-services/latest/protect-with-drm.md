---
title: Azure Media Services DRM の暗号化とライセンス配信サービス
titleSuffix: Azure Media Services
description: Microsoft PlayReady、Google Widevine、または Apple FairPlay のライセンスで暗号化されたストリームを、DRM 動的暗号化とライセンス販売サービスを使用して配信する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: abaa82d6f5f33a3dc29db50ae6d029dacd3f7c13
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89289360"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>チュートリアル:DRM 動的暗号化とライセンス配信サービスの使用

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

> [!NOTE]
> このチュートリアルでは [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) の例を使用していますが、全体的な手順は [REST API](/rest/api/media/liveevents)、[CLI](/cli/azure/ams/live-event?view=azure-cli-latest)、またはその他のサポートされている [SDK](media-services-apis-overview.md#sdks) と同じです。

Microsoft PlayReady、Google Widevine、または Apple FairPlay ライセンスを使用して暗号化したストリームを、Azure Media Services を使用して配信できます。 詳細については、「[動的暗号化によるコンテンツ保護](content-protection-overview.md)」を参照してください。

Media Services にも、PlayReady、Widevine、FairPlay の DRM ライセンスを配信するサービスがあります。 ユーザーが DRM で保護されたコンテンツを要求すると、プレーヤー アプリが Media Services ライセンス サービスにライセンスを要求します。 そのプレーヤー アプリが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 ライセンスには、クライアント プレーヤーがコンテンツの解読とストリーム配信に使用できる解読キーが含まれています。

この記事は、[DRM での暗号化](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM)のサンプルに基づいています。

この記事で紹介しているサンプルの結果は次のようになります。

![Azure Media Player での AMS と DRM で保護されたビデオ](./media/protect-with-drm/ams_player.png)

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * エンコード Transform を作成します。
> * トークンの検証に使用する署名キーを設定します。
> * コンテンツ キー ポリシーで要件を設定します。
> * 指定したストリーミング ポリシーで StreamingLocator を作成します。
> * お使いのファイルの再生に使用する URL を作成します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには以下が必要です。

* 「[コンテンツ保護の概要](content-protection-overview.md)」の記事を確認します。
* 「[アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)」を確認してください。
* Visual Studio Code または Visual Studio をインストールします。
* [このクイックスタート](./create-account-howto.md)の説明に従って、新しい Azure Media Services アカウントを作成します。
* [API へのアクセス](./access-api-howto.md)に関するページに従って、Media Services API を使用するために必要な資格情報を入手します。
* アプリの構成ファイル (appsettings.json) に適切な値を設定します。

## <a name="download-code"></a>コードをダウンロードする

次のコマンドを使用して、この記事で紹介した完全な .NET サンプルが含まれる GitHub リポジトリを、お使いのマシンに複製します。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
"DRM を使用した暗号化" のサンプルは、[EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) フォルダーにあります。

> [!NOTE]
> このサンプルでは、アプリを実行するたびに一意のリソースが作成されます。 通常は、変換やポリシーなどの既存のリソースを再利用します (既存のリソースが必要な構成を備えている場合)。

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成します。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>出力アセットを作成する  

出力[アセット](assets-concept.md)には、対象のエンコード ジョブの結果が格納されます。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>エンコード Transform を取得または作成する

新しい [Transform](transforms-jobs-concept.md) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように `transformOutput` オブジェクトです。 各 TransformOutput には **Preset** が含まれます。 Preset では、目的の TransformOutput の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この記事で説明されているサンプルでは、**AdaptiveStreaming** という名前の組み込みプリセットを使っています。 プリセットは、入力ビデオを入力の解像度とビットレートに基づいて自動生成されるビットレート ラダー (ビットレートと解像度のペア) にエンコードし、ビットレートと解像度の各ペアに対応する、H.264 ビデオと AAC オーディオを含む ISO MP4 ファイルを生成します。 

新しい **Transform** を作成する前に、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。  Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>ジョブの送信

上で説明したように、**Transform** オブジェクトはレシピであり、[Job](transforms-jobs-concept.md) は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。

このチュートリアルでは、[HTTPs ソース URL](job-input-from-http-how-to.md) から直接取り込んだファイルに基づいてジョブの入力を作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブが完了するまでに、一定の時間がかかります。 完了したら通知を受け取りたいでしょう。 次のコード例では、**ジョブ**の状態をサービスに対してポーリングする方法を示します。 待機時間が発生する可能性があるため、ポーリングは運用アプリに推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。 [カスタム Web エンドポイントへのイベントのルーティング](job-state-events-cli-how-to.md)に関するページをご覧ください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態が返されます。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>コンテンツ キー ポリシーを作成する

コンテンツ キーにより、アセットに安全にアクセスすることができます。 DRM を使用してコンテンツを暗号化するときは、[コンテンツ キーポリシー](content-key-policy-concept.md)を作成する必要があります。 このポリシーでは、コンテンツ キーをエンド クライアントに配信する方法を構成します。 コンテンツ キーは、ストリーミング ロケーターに関連付けられます。 Media Services には、承認されたユーザーに暗号化キーとライセンスを配信する、キー配信サービスも用意されています。

指定された構成でキーを配信するにあたって満たす必要がある、**コンテンツ キー ポリシー**の要件 (制限) を設定する必要があります。 この例では、次の構成と要件を設定します。

* 構成

    [PlayReady](playready-license-template-overview.md) と [Widevine](widevine-license-template-overview.md) のライセンスを構成して、Media Services ライセンス配信サービスで配信できるようにします。 このサンプル アプリでは [FairPlay](fairplay-license-overview.md) ライセンスは構成しませんが、FairPlay の構成に利用できるメソッドが含まれています。 FairPlay のオプションは、別に構成して追加できます。

* Restriction

    このアプリにより、ポリシーに JSON Web トークン (JWT) のトークンの種類の制限が設定されます。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、コンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーによるキーの取得が承認されているかどうかを判断するために、キーに指定されたコンテンツ キー ポリシーがサービスにより評価されます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

エンコードが完了し、コンテンツ キー ポリシーを設定したら、次に、出力アセット内のビデオを、クライアントが再生に利用できるようにします。 ビデオを公開するには、次の 2 つの手順を実行します。

1. [ストリーミング ロケーター](streaming-locators-concept.md)を作成します。
2. クライアントが使用できるストリーミング URL を作成します。

**ストリーミング ロケーター**を作成するプロセスは、発行と呼ばれます。 既定では、API 呼び出しを行った直後に**ストリーミング ロケーター**が有効になります。 オプションの開始時間と終了時間を構成しない限り、その状態は削除されるまで継続されます。

**ストリーミング ロケーター**を作成するときは、使用する `StreamingPolicyName` を指定する必要があります。 このチュートリアルでは、定義済みのストリーミング ポリシーの 1 つを使用しています。このポリシーは、ストリーミングのためにコンテンツを発行する方法を Azure Media Services に指示します。 この例では、"Predefined_MultiDrmCencStreaming" ポリシーに StreamingLocator.StreamingPolicyName を設定します。 PlayReady と Widevine の暗号化が適用され、キーは構成済みの DRM ライセンスに基づいて再生クライアントに配信されます。 また、CBCS (FairPlay) でもストリームを暗号化する場合は、"Predefined_MultiDrmStreaming" を使用します。

> [!IMPORTANT]
> カスタム [ストリーミング ポリシー](streaming-policy-concept.md)を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 Media Service アカウントには、StreamingPolicy エントリの数に対するクォータがあります。 StreamingLocator ごとに新しい StreamingPolicy を作成しないでください。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>テスト トークンを取得する

このチュートリアルでは、トークン制限があるコンテンツ キー ポリシーを指定します。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、このサンプルで構成する [JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) 形式のトークンがサポートされています。

ContentKeyIdentifierClaim は ContentKeyPolicy で使用されます。つまり、キー配信サービスに提示されるトークンに ContentKey の識別子が含まれている必要があります。 このサンプルでは、ストリーミング ロケーターの作成時にコンテンツ キーを指定していません。システムによってランダムなコンテンツ キーが自動的に作成されます。 テスト トークンを生成するには、ContentKeyIdentifierClaim 要求に含める ContentKeyId を取得する必要があります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>ストリーミング URL を作成する

[StreamingLocator](/rest/api/media/streaminglocators) が作成されたので、ストリーミング URL を取得できます。 URL を作成するには、[StreamingEndpoint](/rest/api/media/streamingendpoints) のホスト名と**ストリーミング ロケーター**のパスを連結する必要があります。 このサンプルでは、*既定の* **ストリーミング エンドポイント**を使っています。 最初に Media Service アカウントを作成したとき、この*既定の* **ストリーミング エンドポイント**は停止状態になっているので、**Start** を呼び出す必要があります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

アプリを実行すると、次の画面が表示されます。

![DRM での保護](./media/protect-with-drm/playready_encrypted_url.png)

ブラウザーを開いて結果の URL を貼り付けると、URL とトークンが既に入力されている Azure Media Player のデモ ページが起動します。

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般には、再利用を計画しているオブジェクト以外をクリーンアップします (通常、Transform、StreamingLocator などを再利用します)。 実験後にお使いのアカウントをクリーン アップする場合は、再利用する予定のないリソースを削除します。 たとえば、ジョブ、作成された資産、コンテンツ キー ポリシーは、次のコードで削除します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、前に作成したリソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

次のページを確認してください

> [!div class="nextstepaction"]
> [AES-128 での保護](protect-with-aes128.md)
