---
title: Azure Media Services v3 を使用した iOS 用のオフラインの FairPlay ストリーミング
description: このトピックでは、Azure Media Services を使って HTTP ライブ ストリーミング (HLS) コンテンツをオフライン モードの Apple FairPlay で動的に暗号化する方法の概要を説明します。
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), オフライン, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: c46fe5a4199e73cd22aeef4df62daeed4b58db06
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291315"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Media Services v3 を使用した iOS 用のオフラインの FairPlay ストリーミング

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

 Azure Media Services では、次のものを対象にして、適切に設計された[コンテンツ保護サービス](https://azure.microsoft.com/services/media-services/content-protection/)のセットが提供されます。

- Microsoft PlayReady
- Google Widevine
    
    Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。
- Apple FairPlay
- AES-128 暗号化

コンテンツのデジタル著作権管理 (DRM)/Advanced Encryption Standard (AES) 暗号化は、さまざまなストリーミング プロトコルの要求時に動的に実行されます。 DRM ライセンス/AES 暗号化解除キー配信サービスも、Media Services によって提供されます。

さまざまなストリーミング プロトコルでのオンライン ストリーミングのコンテンツの保護だけでなく、保護されたコンテンツのオフライン モードも、よく要求される機能です。 オフライン モードのサポートは、次のシナリオで必要です。

* 旅行中など、インターネット接続を利用できないときに再生します。
* コンテンツ プロバイダーによっては、国/地域の境を越えた DRM ライセンス配信を許可しないことがあります。 国/地域の外部を旅行中にコンテンツを視聴したい場合は、オフライン ダウンロードが必要です。
* 一部の国/地域では、インターネットの使用や帯域幅がまだ制限されています。 ユーザーは先にダウンロードしておくことで、満足できる十分に高い解像度でコンテンツを視聴できます。 通常、この場合の問題はネットワークの可用性ではなく、ネットワーク帯域幅の制限です。 Over-the-Top (OTT)/オンライン ビデオ プラットフォーム (OVP) プロバイダーは、オフライン モードのサポートを要求します。

この記事では、iOS 10 以降を搭載するデバイスを対象とする FairPlay Streaming (FPS) のオフライン モードのサポートについて説明します。 この機能は、watchOS、tvOS、Safari on macOS など、他の Apple プラットフォームではサポートされていません。

> [!NOTE]
> オフライン DRM は、コンテンツをダウンロードするときの 1 ライセンスのために、1 つの要求を発行することに対してのみ課金されます。 どのエラーにも課金は行われません。

## <a name="prerequisites"></a>前提条件

iOS 10 以降のデバイスの FairPlay 用にオフライン DRM を実装する前に、次の準備が必要です。

* FairPlay のオンライン コンテンツ保護について確認します。 

    - [Apple FairPlay ライセンスの要件と構成](fairplay-license-overview.md)
    - [DRM 動的暗号化とライセンス配信サービスの使用](protect-with-drm.md)
    - オンライン FPS ストリーミングの構成を含む .NET サンプル:[ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Apple Developer Network から FPS SDK を入手します。 FPS SDK には、2 つのコンポーネントが含まれています。

    - FPS Server SDK には、キー セキュリティ モジュール (KSM)、クライアントのサンプル、仕様、テスト ベクターのセットが含まれています。
    - FPS Deployment Pack には、D 関数、仕様および FPS 証明書生成方法の説明、顧客固有の秘密キー、アプリケーション秘密キーが含まれています。 Apple は、ライセンスのあるコンテンツ プロバイダーに対してのみ、FPS Deployment Pack を発行します。
* [https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git ) を複製します。 

    [.NET を使用した DRM の暗号化](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)に関する記事にあるコードを変更して FairPlay の構成を追加する必要があります。  

## <a name="configure-content-protection-in-azure-media-services"></a>Azure Media Services でコンテンツ保護を構成する

[GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) メソッドで、次の操作を行います。

FairPlay ポリシー オプションを設定するコードをコメント解除します。

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

また、CBCS ContentKeyPolicyOption を ContentKeyPolicyOptions の一覧に追加するコードをコメント解除します

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>オフライン モードを有効にする

オフライン モードを有効にするには、カスタム StreamingPolicy を作成し、[CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561) で StreamingLocator を作成するときに、その名前を使用します。
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

これで、Media Services アカウントはオフライン FairPlay ライセンスを配信するように構成されました。

## <a name="sample-ios-player"></a>サンプル iOS プレーヤー

FPS オフライン モードのサポートは iOS 10 以降でのみ利用可能です。 FPS Server SDK (バージョン 3.0 以降) には、FPS オフライン モードのドキュメントとサンプルが含まれています。 具体的には、FPS Server SDK (バージョン 3.0 以降) には、オフライン モードに関連する次の 2 つの項目が含まれます。

* ドキュメント:「Offline Playback with FairPlay Streaming and HTTP Live Streaming」(FairPlay Streaming と HTTP ライブ ストリーミングでのオフライン再生) (Apple、2016 年 9 月 14 日発行)。 FPS Server SDK バージョン 4.0 では、このドキュメントはメインの FPS ドキュメントにマージされています。
* サンプル コード:FPS オフライン モードの HLSCatalog サンプル (Apple の FPS Server SDK の一部)(\FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\)。 HLSCatalog サンプル アプリでは、次のコード ファイルがオフラインモード機能の実装に使用されます。

    - AssetPersistenceManager.swift コード ファイル:AssetPersistenceManager が、次の方法を示すこのサンプルのメイン クラスです。

        - ダウンロードの開始とキャンセルやデバイスからの既存資産の削除に使用される API など、HLS ストリームのダウンロードを管理します。
        - ダウンロードの進行状況を監視します。
    - AssetListTableViewController.swift および AssetListTableViewCell.swift コード ファイル:AssetListTableViewController は、このサンプルのメイン インターフェイスです。 サンプルが再生、ダウンロード、削除、またはダウンロードのキャンセルに使用できる資産の一覧を提供します。 

実行中の iOS プレーヤーを設定する手順を説明します。 FPS Server SDK バージョン 4.0.1 の HLSCatalog サンプルを基にして始める場合は、次のようにコードを変更します。

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift で、次のコードを使用してメソッド `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` を実装します。 "drmUr" を HLS URL に割り当てられる変数にします。

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

HLSCatalog\Shared\Managers\ContentKeyDelegate.swift で、`requestApplicationCertificate()` メソッドを実装します。 この実装は、デバイスに証明書 (公開キーのみ) を埋め込むか、Web 上で証明書をホストするかによって異なります。 次の実装では、テスト サンプルで使用されるホスト アプリケーション証明書を使用します。 変数 "certUrl" には、アプリケーション証明書の URL が格納されます。

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

最終的な統合テストでのビデオ URL とアプリケーション証明書 URL については、「統合テスト」セクションで提供します。

HLSCatalog\Shared\Resources\Streams.plist にテスト ビデオの URL を追加します。 コンテンツ キー ID には、skd プロトコルを使用した FairPlay ライセンス取得 URL を一意の値として使用します。

![オフライン FairPlay iOS アプリ ストリーム](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

独自のテスト ビデオ URL、FairPlay ライセンス取得 URL、およびアプリケーション証明書 URL を設定している場合は、それらを使用します。 または、次のセクションに進みます。このセクションには、テスト サンプルが含まれています。

## <a name="integrated-test"></a>統合テスト

Media Services の 3 つのテスト サンプルには、次の 3 つのシナリオが含まれます。

* FPS 保護、ビデオ、オーディオ、代替オーディオ トラックあり。
* FPS 保護、ビデオ、オーディオあり、代替オーディオ トラックなし。
* FPS 保護、ビデオのみ、オーディオなし。

これらのサンプルはこちらの[デモ サイト](https://aka.ms/poc#22)にあり、対応するアプリケーション証明書は Azure Web アプリでホストされています。
FPS Server SDK のバージョン 3 またはバージョン 4 サンプルでは、マスター再生リストに代替オーディオが含まれる場合、オフライン モードのときに、オーディオのみが再生されます。 そのため、代替オーディオを削除する必要があります。 つまり、前述の 2 番目と 3 番目のサンプルは、オンライン モードとオフライン モードで動作します。 最初のサンプルは、オフライン モードでのみオーディオが再生され、オンライン ストリーミングは正常に動作します。

## <a name="faq"></a>よく寄せられる質問

[トラブルシューティングに役立つよく寄せられる質問](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode)を参照してください。

## <a name="next-steps"></a>次のステップ

[AES-128 での保護](protect-with-aes128.md)の方法に関する記事を参照してください。
