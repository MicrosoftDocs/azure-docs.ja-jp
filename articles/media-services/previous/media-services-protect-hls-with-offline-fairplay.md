---
title: オフラインの Apple FairPlay で HLS コンテンツを保護する - Azure | Microsoft Docs
description: このトピックでは、Azure Media Services を使って HTTP ライブ ストリーミング (HLS) コンテンツをオフライン モードの Apple FairPlay で動的に暗号化する方法の概要を説明します。
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), オフライン, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 228b00a19bac9c773fce8e502d302314821fbf39
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871635"
---
# <a name="offline-fairplay-streaming-for-ios"></a>オフラインの iOS 用 FairPlay Streaming 

> [!div class="op_single_selector" title1="使用している Media Services のバージョンを選択してください。"]
> * [Version 3](../latest/offline-fairplay-for-ios.md)
> * [Version 2](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

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

## <a name="preliminary-steps"></a>準備作業
iOS 10 以降のデバイスの FairPlay 用にオフライン DRM を実装する前に、次の準備が必要です。

* FairPlay のオンライン コンテンツ保護について理解します。 詳細については、次の記事とサンプルを参照してください。

    - [Apple FairPlay Streaming for Azure Media Services の一般公開](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Apple FairPlay または Microsoft PlayReady による HLS コンテンツの保護](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [オンライン FPS ストリーミングのサンプル](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Apple Developer Network から FPS SDK を入手します。 FPS SDK には、2 つのコンポーネントが含まれています。

    - FPS Server SDK には、キー セキュリティ モジュール (KSM)、クライアントのサンプル、仕様、テスト ベクターのセットが含まれています。
    - FPS Deployment Pack には、D 関数、仕様および FPS 証明書生成方法の説明、顧客固有の秘密キー、アプリケーション秘密キーが含まれています。 Apple は、ライセンスのあるコンテンツ プロバイダーに対してのみ、FPS Deployment Pack を発行します。

## <a name="configuration-in-media-services"></a>Media Services での構成
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices) を使用した FPS オフライン モード構成では、FPS オフライン モードの構成に必要な API が用意されている Media Services .NET SDK バージョン 4.0.0.4 以降を使用します。
オンライン モードの FPS コンテンツ保護を構成するには、作業コードも必要です。 FPS のオンライン モードのコンテンツ保護を構成するコードを入手した後に必要な変更は、次の 2 点のみです。

## <a name="code-change-in-the-fairplay-configuration"></a>FairPlay 構成でのコードの変更
1 つ目の変更は、"オフライン モードを有効にする" ブール値 objDRMSettings.EnableOfflineMode の定義です。オフライン DRM シナリオを有効にするときは、これを true にします。 このインジケーターに応じて、FairPlay の構成を次のように変更します。

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>資産の配信ポリシーの構成でコードを変更する
2 つ目の変更は、Dictionary<AssetDeliveryPolicyConfigurationKey, string> への第 3 のキーの追加です。
次のように AssetDeliveryPolicyConfigurationKey を追加します。
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

このステップの後、FPS の資産配信ポリシーの <Dictionary_AssetDeliveryPolicyConfigurationKey> には、次の 3 つのエントリが含まれるようになります。

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl または AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl (使用される FPS KSM/キー サーバーや、複数の資産に同じ資産配信ポリシーを再利用するかどうかなどの要素に応じて変わります)
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

これで、Media Services アカウントはオフライン FairPlay ライセンスを配信するように構成されました。

## <a name="sample-ios-player"></a>サンプル iOS プレーヤー
FPS オフライン モードのサポートは iOS 10 以降でのみ利用可能です。 FPS Server SDK (バージョン 3.0 以降) には、FPS オフライン モードのドキュメントとサンプルが含まれています。 具体的には、FPS Server SDK (バージョン 3.0 以降) には、オフライン モードに関連する次の 2 つの項目が含まれます。

* ドキュメント:「Offline Playback with FairPlay Streaming and HTTP Live Streaming」(FairPlay Streaming と HTTP ライブ ストリーミングでのオフライン再生) (Apple、2016 年 9 月 14 日発行)。 FPS Server SDK バージョン 4.0 では、このドキュメントはメインの FPS ドキュメントにマージされています。
* サンプル コード:\FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ 内の FPS オフライン モードの HLSCatalog サンプル。 HLSCatalog サンプル アプリでは、次のコード ファイルがオフラインモード機能の実装に使用されます。

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

![オフライン FairPlay iOS アプリ ストリーム](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

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
- **FPS オフライン モードの次の API で、最後のパラメーターは何を表していますか。** 
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    この API のドキュメントについては、「[FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration Method](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)」(FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration メソッド) を参照してください。 このパラメーターはオフライン レンタルの期間を表します (単位は時間)。
- **iOS デバイスでのダウンロード済み/オフライン ファイルの構造はどのようなものですか。** iOS デバイスにダウンロードされたファイルは、次のスクリーンショットのような構造になっています。 `_keys` フォルダーには、ダウンロードされた FPS ライセンスが格納されます (ライセンス サービス ホストごとに 1 つのストア ファイル)。 `.movpkg` フォルダーには、オーディオとビデオのコンテンツが格納されます。 名前の末尾にダッシュと数字が付いている最初のフォルダーには、ビデオ コンテンツが格納されます。 数字はビデオ再生の PeakBandwidth です。 名前の末尾にダッシュと 0 が付いている 2 つ目のフォルダーには、オーディオ コンテンツが格納されます。 "Data" という名前の 3 番目のフォルダーには、FPS コンテンツのマスター再生リストが含まれます。 最後に、boot.xml には、`.movpkg` フォルダーの内容が完全に説明されています。 

![オフライン FairPlay の iOS サンプル アプリ ファイルの構造](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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

## <a name="summary"></a>まとめ
このドキュメントには、FPS オフライン モードを実装するために使用できる以下の手順と情報が含まれています。

* Media Services .NET API を使用した Media Services コンテンツ保護の構成で、Media Services に動的 FairPlay 暗号化と FairPlay ライセンス配信が構成されます。
* FPS Server SDK のサンプルに基づく iOS プレーヤーで、オンライン ストリーミング モードまたはオフライン モードのいずれかで FPS コンテンツを再生できる iOS プレーヤーを設定します。
* サンプル FPS ビデオは、オフライン モードとオンライン ストリーミングのテストに使用されます。
* FAQ では、FPS オフライン モードに関する質問の回答を確認できます。
