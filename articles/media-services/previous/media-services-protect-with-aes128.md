---
title: AES-128 動的暗号化とキー配信サービスの使用 | Microsoft Docs
description: このトピックでは、AES-128 を使用して動的に暗号化する方法とキー配信サービスを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895825"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>AES-128 動的暗号化とキー配信サービスの使用
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

Media Services では、AES 128 ビット暗号化キーを使用して暗号化された HTTP ライブ ストリーミング (HLS) およびスムーズ ストリーミングを配信できます。 Media Services には、権限のあるユーザーに暗号化キーを配信する、キー配信サービスも用意されています。 Media Services で資産を暗号化する場合は、暗号化キーを資産に関連付ける必要があります。また、キーの承認ポリシーを構成する必要があります。 プレーヤーがストリームを要求すると、Media Services は指定されたキーを使用して、AES でコンテンツを動的に暗号化します。 ストリームの暗号化を解除するには、プレーヤーはキー配信サービスからキーを要求します。 ユーザーのキーの取得が承認されているかどうかを判断するために、サービスはキーに指定した承認ポリシーを評価します。

Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープンまたはトークン制限) を指定できます。 トークン制限ポリシーには、STS (セキュリティ トークン サービス) によって発行されたトークンを含める必要があります。 Media Services では、[単純 Web トークン](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) 形式と [JSON Web トークン](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 形式のトークンがサポートされます。 詳細については、「[コンテンツ キー承認ポリシーの構成](media-services-protect-with-aes128.md#configure_key_auth_policy)」を参照してください。

動的暗号化を使用するには、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産が必要です。 また、資産の配信ポリシーを構成する必要があります (この記事で後述します)。 次に、ストリーミング URL で指定された形式に基づいて、オンデマンド ストリーミング サーバーは、選択されたプロトコルでストリームを配信できるようにします。 そのため、単一のストレージ形式のファイルだけに対して保存と支払を行う必要があります。 Media Services は、クライアントからの要求に応じて適切な応答を作成して返します。

この記事には、保護されたメディアを配信するアプリケーションの開発に取り組む開発者にとって有用な情報が含まれています。 この記事では、承認ポリシーを使用するキー配信サービスの構成方法を説明します。これにより、許可されたクライアントのみが暗号化キーを受け取ることができるようになります。 また、動的暗号化の使用方法についても説明します。

macOS 上で Safari に配信するために Advanced Encryption Standard (AES) でコンテンツを暗号化する方法については、[こちらのブログ投稿](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)を参照してください。
AES 暗号化でメディア コンテンツを保護する方法の概要については、[こちらのビデオ](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption)をご覧ください。


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 動的暗号化とキー配信サービスのワークフロー

Media Services キー配信サービスと動的暗号化を使用して、AES で資産を保護する場合は、以下の一般的な手順を実行します。

1. [資産を作成し、その資産にファイルをアップロードします](media-services-protect-with-aes128.md#create_asset)。

2. [ファイルが含まれている資産をアダプティブ ビットレート MP4 セットにエンコードします](media-services-protect-with-aes128.md#encode_asset)。

3. [コンテンツ キーを作成し、それをエンコードした資産に関連付けます](media-services-protect-with-aes128.md#create_contentkey)。 Media Services では、コンテンツ キーに資産の暗号化キーが含まれています。

4. [コンテンツ キーの承認ポリシーを構成します](media-services-protect-with-aes128.md#configure_key_auth_policy)。 コンテンツ キー承認ポリシーを構成する必要があります。 コンテンツ キーがクライアントに配信される前に、クライアントはポリシーの要件を満たす必要があります。

5. [資産の配信ポリシーを構成します](media-services-protect-with-aes128.md#configure_asset_delivery_policy)。 配信ポリシー構成には、キー取得 URL と初期化ベクトル (IV) が含まれます (AES-128 では、暗号化と復号化に同じ IV が必要です)。構成には、配信プロトコル (MPEG-DASH、HLS、Smooth Streaming、またはすべて) と動的暗号化の種類 (エンベロープや動的暗号化なしなど) も含まれます。

    同じ資産の各プロトコルに異なるポリシーを適用できます。 たとえば、PlayReady 暗号化を Smooth/DASH に適用し、AES エンベロープを HLS に適用できます。 配信ポリシーで定義されていないプロトコルはストリーミングからブロックされます (たとえば、プロトコルとして HLS のみを指定する単一のポリシーを追加した場合などです)。ただし、資産配信ポリシーをまったく定義していない場合は例外です。 この場合、すべてのプロトコルが平文で許可されます。

6. ストリーミング URL を取得するために、[OnDemand ロケーターを作成します](media-services-protect-with-aes128.md#create_locator)。

この記事では、[クライアント アプリケーションがキー配信サービスにキーを要求する方法](media-services-protect-with-aes128.md#client_request)についても説明します。

この記事の最後に、詳細な [.NET の例](media-services-protect-with-aes128.md#example)があります。

前述したワークフローを図にすると、次のようになります。 ここでは、認証のためにトークンが使用されています。

![AES-128 での保護](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

この記事の残りの部分では、説明、コード例、および前述したタスクの実行方法を説明するトピックへのリンクを紹介します。

## <a name="current-limitations"></a>現時点での制限事項
資産の配信ポリシーを追加または更新する場合は、既存のすべてのロケーターを削除し、新しいロケーターを作成する必要があります。

## <a id="create_asset"></a>資産を作成し、その資産にファイルをアップロードする
ビデオの管理、エンコード、およびストリーミングを行うには、最初にコンテンツを Media Services にアップロードする必要があります。 コンテンツをアップロードすると、クラウドにコンテンツが安全に保存され、処理したりストリーミングしたりできるようになります。 

詳細については、[Media Services アカウントへのファイルのアップロード](media-services-dotnet-upload-files.md)に関するページを参照してください。

## <a id="encode_asset"></a>ファイルが含まれている資産をアダプティブ ビットレート MP4 セットにエンコードする
動的暗号化では、一連のマルチビットレート MP4 ファイルまたはマルチビットレート Smooth Streaming ソース ファイルを含む資産を作成します。 そうすれば、マニフェストまたはフラグメント要求で指定された形式に基づき、オンデマンド ストリーミング サーバーによって、ユーザーが選択したプロトコルでストリームを受信するようになります。 そのため、単一のストレージ形式のファイルだけに対して保存と支払を行う必要があります。 Media Services は、クライアントからの要求に応じて適切な応答を作成して返します。 詳細については、[動的パッケージの概要](media-services-dynamic-packaging-overview.md)に関するページを参照してください。

>[!NOTE]
>Media Services アカウントの作成時に、既定のストリーミング エンドポイントが "停止" 状態でアカウントに追加されます。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが "実行中" 状態である必要があります。 
>
>また、ダイナミック パッケージと動的暗号化を使用するには、資産に一連のアダプティブ ビットレート MP4、またはアダプティブ ビットレート Smooth Streaming ファイルが含まれている必要があります。

エンコード手順については、[Media Encoder Standard による資産のエンコード](media-services-dotnet-encode-with-media-encoder-standard.md)に関するページを参照してください。

## <a id="create_contentkey"></a>コンテンツ キーを作成し、それをエンコードした資産に関連付ける
Media Services では、コンテンツ キーに、資産を暗号化するときに使用するキーが含まれています。

詳細については、[コンテンツ キーの作成](media-services-dotnet-create-contentkey.md)に関するページを参照してください。

## <a id="configure_key_auth_policy"></a>コンテンツ キーの承認ポリシーを構成する
Media Services では、キーを要求するユーザーを承認する複数の方法がサポートされています。 コンテンツ キー承認ポリシーを構成する必要があります。 キーをクライアント (プレーヤー) に配信するには、クライアントがポリシーの要件を満たしている必要があります。 コンテンツ キー承認ポリシーには、1 つまたは複数の承認制限 (オープン、トークン制限、または IP 制限) を指定できます。

詳細については、[コンテンツ キー承認ポリシーの構成](media-services-dotnet-configure-content-key-auth-policy.md)に関するページを参照してください。

## <a id="configure_asset_delivery_policy"></a>資産の配信ポリシーを構成する
資産の配信ポリシーを構成します。 資産の配信ポリシーの構成には、次のような内容が含まれます。

* キー取得 URL。 
* エンベロープ暗号化に使用する初期化ベクトル (IV)。 AES-128 では、暗号化と復号化に同じ IV が必要です。 
* 資産配信プロトコル (たとえば、MPEG DASH、HLS、Smooth Streaming、またはすべて)。
* 動的暗号化の種類 (AES エンベロープなど) または動的暗号化なし。 

詳細については、[資産配信ポリシーの構成](media-services-dotnet-configure-asset-delivery-policy.md)に関するページを参照してください。

## <a id="create_locator"></a>ストリーミング URL を取得するために OnDemand ストリーミング ロケーターを作成する
Smooth Streaming、DASH、または HLS のストリーミング URL をユーザーに提供する必要があります。

> [!NOTE]
> 資産の配信ポリシーを追加または更新する場合は、既存のすべてのロケーターを削除し、新しいロケーターを作成する必要があります。
> 
> 

資産を発行し、ストリーミング URL を構築する手順については、「 [Build a streaming URL (ストリーミング URL の構築)](media-services-deliver-streaming-content.md)」をご覧ください。

## <a name="get-a-test-token"></a>テスト トークンを取得する
キー認証ポリシーに使用されたトークンによる制限に基づいて、テスト トークンを取得します。

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

[Azure Media Services Player](https://aka.ms/azuremediaplayer) を使用して、ストリームをテストできます。

## <a id="client_request"></a>クライアントが配信サービスにキーを要求する方法
以前の手順で、マニフェスト ファイルを参照する URL を構成しました。 クライアントは、キー配信サービスへの要求を実行するために、ストリーミング マニフェスト ファイルから必要な情報を抽出する必要があります。

### <a name="manifest-files"></a>マニフェスト ファイル
クライアントはマニフェスト ファイルから URL 値 (コンテンツ キー ID [kid] も含まれる) を抽出する必要があります。 その後、クライアントは、キー配信サービスからの暗号化キーの取得を試みます。 また、クライアントは、IV 値を抽出し、それを使ってストリームの暗号化を解除する必要もあります。 次のスニペットでは、Smooth Streaming マニフェストの `<Protection>` 要素を示します。

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

HLS の場合、ルート マニフェストはセグメント ファイルに分割されます。 

たとえば、ルート マニフェストは http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) です。 これにはセグメント ファイル名の一覧が含まれています。

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

セグメント ファイルのいずれかをテキスト エディターで開くと (たとえば、http:\///test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl)、それには #EXT-X-KEY キーが含まれ、ファイルが暗号化されていることを示します。

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Safari で AES 暗号化された HLS を再生することを計画している場合には、[こちらのブログ](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)を参照してください。

### <a name="request-the-key-from-the-key-delivery-service"></a>キー配信サービスにキーを要求する

次のコードは、キー配信 URI (マニフェストから抽出) とトークンを使用して、Media Services のキー配信サービスに要求を送信する方法を示しています (STS から SWT を取得する方法は、このトピックでは扱いません)。

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>.NET を使用して AES 128 でコンテンツを保護する

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio プロジェクトの作成と構成

1. 「[.NET を使用した Media Services 開発](media-services-dotnet-how-to-use.md)」の説明に従って、開発環境をセットアップし、app.config ファイルに接続情報を指定します。

2. app.config ファイルで定義されている appSettings に次の要素を追加します。

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>例

Program.cs ファイルのコードを、このセクションで示されているコードで上書きします。
 
>[!NOTE]
>さまざまな Media Services ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可を使用する場合は、同じポリシー ID を使用します。 たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー) に対してです。 詳細については、「[Media Services .NET SDK を使用するアセットと関連エンティティの管理](media-services-dotnet-manage-entities.md#limit-access-policies)」の「アクセス ポリシーを制限する」セクションを参照してください。

必ず変数を更新して、入力ファイルが置かれているフォルダーをポイントするようにしてください。

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
