---
title: オフラインの Apple FairPlay で HLS コンテンツを保護する - Azure | Microsoft Docs
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
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: f2514fff2a3bb292a86c9f4c0e92c37ed2709097
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341041"
---
# <a name="offline-fairplay-streaming-for-ios"></a>オフラインの iOS 用 FairPlay Streaming 

 Azure Media Services では、次のものを対象にして、適切に設計された[コンテンツ保護サービス](https://azure.microsoft.com/services/media-services/content-protection/)のセットが提供されます。

- Microsoft PlayReady
- Google Widevine
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

オフライン モードを有効にするには、カスタム StreamingPolicy を作成し、[CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563) で StreamingLocator を作成するときに、その名前を使用します。
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
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

## <a name="faq"></a>FAQ

次のよく寄せられる質問は、トラブルシューティングに役立ちます。

- **オフライン モードの間は、オーディオのみが再生されて、ビデオは再生されないのはなぜですか。** この動作は、サンプル アプリの本来の設計のようです。 代替オーディオ トラックがある場合 (HLS の場合)、オフライン モードでは、iOS 10 と iOS 11 の両方が、既定で代替オーディオ トラックになります。FPS オフライン モードでこの動作を補正するには、ストリームから代替オーディオ トラックを削除します。 Media Services 側でこれを行うには、動的マニフェスト フィルター "audio-only=false" を追加します。 つまり、HLS URL の最後が .ism/manifest(format=m3u8-aapl,audio-only=false) になります。 
- **audio-only=false を追加した後もまだ、オフライン モードの間は、オーディオのみが再生されて、ビデオが再生されないのはなぜですか。** コンテンツ配信ネットワーク (CDN) キャッシュ キーの設計によっては、コンテンツがキャッシュされることがあります。 キャッシュを消去します。
- **FPS オフライン モードは、iOS 10 だけでなく iOS 11 でもサポートされますか。** はい。 FPS オフライン モードは iOS 10 と iOS 11 でサポートされています。
- **ドキュメント『Offline Playback with FairPlay Streaming and HTTP Live Streaming』(FairPlay Streaming と HTTP ライブ ストリーミングでのオフライン再生) が FPS Server SDK で見つからないのはなぜですか。** FPS Server SDK バージョン 4 以降、このドキュメントは『FairPlay Streaming Programming Guide』(FairPlay Streaming プログラミング ガイド) にまとめられています。
- **iOS デバイスでのダウンロード済み/オフライン ファイルの構造はどのようなものですか。** iOS デバイスにダウンロードされたファイルは、次のスクリーンショットのような構造になっています。 `_keys` フォルダーには、ダウンロードされた FPS ライセンスが格納されます (ライセンス サービス ホストごとに 1 つのストア ファイル)。 `.movpkg` フォルダーには、オーディオとビデオのコンテンツが格納されます。 名前の末尾にダッシュと数字が付いている最初のフォルダーには、ビデオ コンテンツが格納されます。 数字はビデオ再生の PeakBandwidth です。 名前の末尾にダッシュと 0 が付いている 2 つ目のフォルダーには、オーディオ コンテンツが格納されます。 "Data" という名前の 3 番目のフォルダーには、FPS コンテンツのマスター再生リストが含まれます。 最後に、boot.xml には、`.movpkg` フォルダーの内容が完全に説明されています。 

![オフライン FairPlay の iOS サンプル アプリ ファイルの構造](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

boot.xml ファイルのサンプル:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>次の手順

[AES-128 での保護](protect-with-aes128.md)の方法に関する記事を参照してください。
