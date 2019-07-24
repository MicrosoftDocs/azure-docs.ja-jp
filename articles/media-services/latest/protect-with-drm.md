---
title: Azure Media Services での DRM 動的暗号化ライセンス配信サービスの使用 | Microsoft Docs
description: Microsoft PlayReady、Google Widevine、または Apple FairPlay ライセンスを使用して暗号化したストリームを、Azure Media Services を使用して配信できます。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: f53ae122e9888f3e537a3557b6ac5bd76856c2eb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767551"
---
# <a name="use-drm-dynamic-encryption-and-license-delivery-service"></a>DRM 動的暗号化とライセンス配信サービスの使用

Azure Media Services を使用すると、MPEG-DASH、Smooth Streaming、および HTTP ライブ ストリーミング (HLS) のストリームを [PlayReady デジタル著作権管理 (DRM)](https://www.microsoft.com/playready/overview/) で保護して配信できます。 Media Services を使用すれば、**Google Widevine** DRM ライセンスで暗号化した DASH ストリームを配信することもできます。 PlayReady と Widevine は、いずれも Common Encryption (ISO/IEC 23001-7 CENC) 仕様に従って暗号化されます。 Media Services では、**Apple FairPlay** (AES-128 CBC) を使用して HLS コンテンツを暗号化することもできます。 

さらに、Media Services は、PlayReady、Widevine、FairPlay の DRM ライセンスを配信するためのサービスを提供しています。 ユーザーが DRM で保護されたコンテンツを要求すると、プレーヤー アプリケーションが Media Services ライセンス サービスにライセンスを要求します。 プレーヤー アプリケーションが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 ライセンスには、クライアント プレーヤーがコンテンツの解読とストリーム配信に使用できる解読キーが含まれています。

この記事は、[DRM での暗号化](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM)のサンプルに基づいています。 このサンプルでは、特に次の操作を行います。

* アダプティブ ビットレート エンコードのための組み込みプリセットを使用するエンコード Transform を作成し、[HTTPS ソース URL](job-input-from-http-how-to.md) からファイルを直接取り込みます。
* トークンの検証に使用する署名キーを設定します。
* 指定された構成でキーを配信するにあたって満たす必要がある、コンテンツ キー ポリシーの要件 (制限) を設定します。 

    * 構成 
    
        このサンプルでは、[PlayReady](playready-license-template-overview.md) と [Widevine](widevine-license-template-overview.md) のライセンスを構成して、Media Services のライセンス配信サービスで配信できるようにします。 このサンプル アプリでは [FairPlay](fairplay-license-overview.md) ライセンスは構成しませんが、サンプルには FairPlay の構成に利用できるメソッドが含まれています。 必要があれば、FairPlay の構成を別のオプションとして追加できます。

    * 制限

        このアプリにより、ポリシーに JWT トークンの種類の制限を設定します。

* 指定されたストリーミング ポリシー名を使用して、指定されたアセットの StreamingLocator を作成します。 この場合は、定義済みポリシーを使用します。 これは、StreamingLocator に 2 つのコンテンツ キーを設定します。AES-128 (エンベロープ) と CENC (PlayReady と Widevine) です。  
    
    StreamingLocator が作成されると、出力アセットが発行され、クライアントが再生に利用できるようになります。

    > [!NOTE]
    > コンテンツのストリーム配信の配信元とする StreamingEndpoint が実行中の状態であることを確認してください。

* DASH マニフェストと、PlayReady で暗号化されたコンテンツを再生するために必要な PlayReady トークンの両方を含む、Azure Media Player への URL を作成します。 このサンプルでは、トークンの有効期限を 1 時間に設定しています。 

    ブラウザーを開いて結果の URL を貼り付けると、URL とトークンが既に入力されている Azure Media Player のデモ ページが起動します。  

    ![DRM での保護](./media/protect-with-drm/playready_encrypted_url.png)

> [!NOTE]
> 複数の暗号化の種類 (AES-128、PlayReady、Widevine、FairPlay) を使用して各アセットを暗号化することができます。 合理的な組み合わせについては、「[ストリーミング プロトコルと暗号化の種類](content-protection-overview.md#streaming-protocols-and-encryption-types)」を参照してください。

この記事で紹介しているサンプルの結果は次のようになります。

![DRM で保護されたビデオを再生する AMS](./media/protect-with-drm/ams_player.png)

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

* 「[コンテンツ保護の概要](content-protection-overview.md)」の記事を確認します。
* [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)に関するページを参照します。
* Visual Studio Code または Visual Studio をインストールする
* [このクイックスタート](create-account-cli-quickstart.md)の説明に従って、新しい Azure Media Services アカウントを作成します。
* [API へのアクセス](access-api-cli-how-to.md)に関するページに従って、Media Services API を使用するために必要な資格情報を入手します。
* アプリケーション構成ファイル (appsettings.json) に適切な値を設定します。

## <a name="download-code"></a>コードをダウンロードする

次のコマンドを使用して、この記事で紹介した完全な .NET サンプルが含まれる GitHub リポジトリを、お使いのマシンに複製します。

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
"DRM を使用した暗号化" のサンプルは、[EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM) フォルダーにあります。

> [!NOTE]
> このサンプルでは、アプリケーションを実行するたびに一意のリソースが作成されます。 通常は、変換やポリシーなどの既存のリソースを再利用します (既存のリソースが必要な構成を備えている場合)。 

## <a name="start-using-media-services-apis-with-net-sdk"></a>.NET SDK で Media Services API の使用を開始する

.NET で Media Services API の使用を始めるには、**AzureMediaServicesClient** オブジェクトを作成する必要があります。 オブジェクトを作成するには、クライアントが Azure AD を使用して Azure に接続するために必要な資格情報を指定する必要があります。 この記事の最初に複製したコード内で、ローカル構成ファイルで指定された資格情報に基づいて、**GetCredentialsAsync** 関数が ServiceClientCredentials オブジェクトを作成します。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>出力アセットを作成する  

出力[アセット](https://docs.microsoft.com/rest/api/media/assets)には、対象のエンコード ジョブの結果が格納されます。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>エンコード Transform を取得または作成する

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) インスタンスを作成するときは、出力として生成するものを指定する必要があります。 必須のパラメーターは、下記のコードで示すように **TransformOutput** オブジェクトです。 各 **TransformOutput** には **Preset** が含まれます。 **Preset** では、目的の **TransformOutput** の生成に使用されるビデオやオーディオの処理操作の詳細な手順が記述されています。 この記事で説明されているサンプルでは、**AdaptiveStreaming** という名前の組み込みプリセットを使っています。 プリセットは、入力ビデオを入力の解像度とビットレートに基づいて自動生成されるビットレート ラダー (ビットレートと解像度のペア) にエンコードし、ビットレートと解像度の各ペアに対応する、H.264 ビデオと AAC オーディオを含む ISO MP4 ファイルを生成します。 

新しい [Transform](https://docs.microsoft.com/rest/api/media/transforms) を作成する前に、次のコードに示すように、最初に **Get** メソッドを使って変換が既に存在するかどうかを確認する必要があります。  Media Services v3 では、エンティティが存在しない場合 (大文字と小文字の区別がない名前のチェック)、エンティティに対する **Get** メソッドは **null** を返します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>ジョブの送信

上で説明したように、[Transform](https://docs.microsoft.com/rest/api/media/transforms) オブジェクトはレシピであり、[Job](https://docs.microsoft.com/rest/api/media/jobs) は **Transform** が特定の入力ビデオまたはオーディオ コンテンツに適用する Media Services への実際の要求です。 **Job** は、入力ビデオの場所や出力先などの情報を指定します。

このチュートリアルでは、[HTTPs ソース URL](job-input-from-http-how-to.md) から直接取り込まれたファイルに基づいてジョブの入力を作成します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>ジョブが完了するのを待つ

ジョブの完了には時間がかかり、完了したら通知を受け取る必要があります。 次のコード例では、[ジョブ](https://docs.microsoft.com/rest/api/media/jobs)の状態をサービスに対してポーリングする方法を示します。 潜在的な待機時間により、ポーリングは運用アプリケーション用の推奨されるベスト プラクティスではありません。 アカウントで過剰に使った場合、ポーリングはスロットルされる可能性があります。 開発者は、代わりに Event Grid を使う必要があります。 [カスタム Web エンドポイントへのイベントのルーティング](job-state-events-cli-how-to.md)に関するページをご覧ください。

**Job** には通常、**Scheduled**、**Queued**、**Processing**、**Finished** (最終状態) という状態があります。 ジョブでエラーが発生すると、**Error** 状態を取得します。 ジョブがキャンセル処理中の場合は **Canceling** を受け取り、完了すると **Canceled** を受け取ります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>ContentKeyPolicy を作成する

コンテンツ キーにより、アセットに安全にアクセスすることができます。 エンド クライアントにコンテンツ キーを配信する方法を構成するコンテンツ キー ポリシーを作成する必要があります。 コンテンツ キーは、StreamingLocator に関連付けられます。 Media Services には、承認されたユーザーに暗号化キーとライセンスを配信する、キー配信サービスも用意されています。 

指定された構成でキーを配信するにあたって満たす必要がある、コンテンツ キー ポリシーの要件 (制限) を設定する必要があります。 この例では、次の構成と要件を設定します。

* 構成 

    [PlayReady](playready-license-template-overview.md) と [Widevine](widevine-license-template-overview.md) のライセンスを構成して、Media Services ライセンス配信サービスで配信できるようにします。 このサンプル アプリでは [FairPlay](fairplay-license-overview.md) ライセンスは構成しませんが、サンプルには FairPlay の構成に利用できるメソッドが含まれています。 FairPlay の構成は別のオプションとして追加できます。

* 制限

    このアプリにより、ポリシーに JWT トークンの種類の制限を設定します。

プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、コンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーによるキーの取得が承認されているかどうかを判断するために、キーに指定されたコンテンツ キー ポリシーがサービスにより評価されます。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>StreamingLocator を作成する

エンコードが完了し、コンテンツ キー ポリシーを設定したら、次に、出力アセット内のビデオを、クライアントが再生に利用できるようにします。 これは 2 つの手順で実行します。 

1. [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成します。
2. クライアントが使用できるストリーミング URL を作成します。 

**StreamingLocator** を作成するプロセスは発行と呼ばれます。 既定では、**StreamingLocator** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。 

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成するときは、使用する **StreamingPolicyName** を指定する必要があります。 このチュートリアルでは、定義済みのストリーミング ポリシーの 1 つを使用しています。このポリシーは、ストリーミングのためにコンテンツを発行する方法を Azure Media Services に指示します。 この例では、"Predefined_MultiDrmCencStreaming" ポリシーに StreamingLocator.StreamingPolicyName を設定します。 このポリシーは、2 つのコンテンツ キー (エンベロープおよび CENC) を生成してロケーターに設定する必要があることを示しています。 このため、エンベロープ、PlayReady および Widevine の暗号化が適用されます (キーは構成済みの DRM ライセンスに基づいて再生クライアントに配信されます)。 また、CBCS (FairPlay) でもストリームを暗号化する場合は、"Predefined_MultiDrmStreaming" を使用します。 

> [!IMPORTANT]
> カスタム [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 Media Service アカウントには、StreamingPolicy エントリの数に対するクォータがあります。 StreamingLocator ごとに新しい StreamingPolicy を作成しないでください。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>テスト トークンを取得する
        
このチュートリアルでは、トークン制限があるコンテンツ キー ポリシーを指定します。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、サンプルで構成されている [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 形式のトークンがサポートされます。

ContentKeyIdentifierClaim は ContentKeyPolicy で使用されます。つまり、キー配信サービスに提示されるトークンに ContentKey の識別子が含まれている必要があります。 このサンプルでは、StreamingLocator の作成時にコンテンツ キーを指定していません。システムによってランダムなコンテンツ キーが自動的に作成されます。 テスト トークンを生成するには、ContentKeyIdentifierClaim 要求に含める ContentKeyId を取得する必要があります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>DASH ストリーミング URL を作成する

[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) が作成されたので、ストリーミング URL を取得できます。 URL を作成するには、[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) のホスト名と **StreamingLocator** のパスを連結する必要があります。 このサンプルでは、"*既定の*" **StreamingEndpoint** を使っています。 最初に Media Service アカウントを作成したとき、この "*既定の*" **StreamingEndpoint** は停止状態になっているので、**Start** を呼び出す必要があります。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services アカウント内のリソースをクリーンアップする

一般に、再利用を計画しているオブジェクトを除くすべてのものをクリーンアップする必要があります (通常、Transform は再利用し、StreamingLocator などは保持します)。 実験後にアカウントをクリーンアップする場合は、再利用する予定がないリソースを削除する必要があります。  たとえば、次のコードはジョブを削除します。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="next-steps"></a>次の手順

[AES-128 での保護](protect-with-aes128.md)の方法に関する記事を参照してください。
