---
title: Media Services および Apple FairPlay ライセンスのサポート - Azure | Microsoft Docs
description: このトピックでは、Apple FairPlay ライセンスの要件と構成の概要について説明します。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6d4b7ba842d08723b90a4f2491d9e79e68dd932e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994894"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Apple FairPlay ライセンスの要件と構成 

Azure Media Services では、**Apple FairPlay** (AES-128 CBC) を使用して HLS コンテンツを暗号化することができます。 Media Services も FairPlay ライセンスを提供するサービスを提供しています。 プレーヤーが FairPlay の保護されたコンテンツを再生しようとすると、ライセンスを取得する要求がライセンス配信サービスに送信されます。 ライセンス サービスはその要求を承認した後、ライセンスを発行します。このライセンスはクライアントに送信され、指定されたコンテンツの暗号化解除と再生に用いられます。

Media Services では、FairPlay ライセンス設定用の API も提供されます。 このトピックでは、FairPlay のライセンス要件について説明し、Media Services の API を使用して、**FairPlay** ライセンスを構成する方法について説明します。 

## <a name="requirements"></a>必要条件

Media Services を使用した **Apple FairPlay** での HLS コンテンツの暗号化、および FairPlay ライセンスの配信のための Media Services の使用には、次のものが必要です。

* [Apple 開発プログラム](https://developer.apple.com/)にサインアップします。
* コンテンツ所有者による[デプロイ パッケージ](https://developer.apple.com/contact/fps/)の取得が Apple によって求められます。 Media Services でキー セキュリティ モジュール (KSM) が既に実装されていること、および最終的な FPS パッケージを要求していることを明記してください。 最終的な FPS パッケージには、証明書を生成し、アプリケーション シークレット キー (ASK) を取得する指示が含まれています。 ASK を使用して、FairPlay を構成します。
* Media Services キー/ライセンスの配信側で次の設定が必要です。

    * **アプリ証明書 (AC)**: 秘密キーを含む .pfx ファイル。 このファイルを作成し、パスワードを使用して暗号化します。 .pfx ファイルの形式は Base64 である必要があります。

        次の手順では、FairPlay 用の .pfx 証明書ファイルを生成する方法について説明します。

        1. https://slproweb.com/products/Win32OpenSSL.html から OpenSSL をインストールします。

            Apple によって提供される FairPlay 証明書とその他のファイルが含まれるフォルダーに移動します。
        2. コマンド ラインから次のコマンドを実行します。 このコマンドにより、.cer ファイルが .pem ファイルに変換されます。

            "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. コマンド ラインから次のコマンドを実行します。 このコマンドにより、.pem ファイルが秘密キーを含む .pfx ファイルに変換されます。 OpenSSL によって .pfx ファイルのパスワードが求められます。

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **アプリ証明書パスワード**: .pfx ファイルを作成するためのパスワード。
    * **ASK**: このキーは、Apple 開発者ポータルを使用して証明書を生成したときに受け取ります。 開発チームごとに一意の ASK が割り当てられます。 ASK のコピーを保存して、安全な場所に保管してください。 ASK は FairPlayAsk として Media services に構成する必要があります。
    
* FPS のクライアント側で、次の設定が必要です。

  * **アプリ証明書 (AC)**: オペレーティング システムが一部のペイロードを暗号化する際に使用する公開キーを含む .cer/.der ファイル。 Media Services で把握しておく必要がある理由は、プレーヤーで必要になるからです。 復号化は、キー配信サービスが対応する秘密キーを使用して行います。

* FairPlay で暗号化されたストリームを再生するには、まず実際の ASK を取得してから、実際の証明書を生成する必要があります。 そのプロセスにより、3 つすべてのパートが作成されます。

  * .der ファイル
  * .pfx ファイル
  * .pfx のパスワード

## <a name="fairplay-and-player-apps"></a>FairPlay とプレーヤー アプリ

コンテンツが **Apple FairPlay** で暗号化されている場合、個々のビデオやオーディオのサンプルは、**AES-128 CBC** モードを使用して暗号化されます。 **FairPlay ストリーミング** (FPS) はデバイスのオペレーティング システムに統合されており、iOS および Apple TV でネイティブにサポートされます。 OS X 上の Safari では、Encrypted Media Extensions (EME) インターフェイスのサポートを使用することで FPS が可能になります。

Azure Media Player は FairPlay の再生もサポートしています。 詳しくは、[Azure Media Player のドキュメント](https://amp.azure.net/libs/amp/latest/docs/index.html)をご覧ください。

プレーヤー アプリは、iOS SDK を使用して開発できます。 FairPlay コンテンツを再生できるようにするには、ライセンス交換プロトコルを実装する必要があります。 このプロトコルは、Apple によって指定されていません。 キー配信の要求を送信する方法は、各アプリに従います。 Media Services FairPlay キー配信サービスでは、SPC が www-form-url でエンコードされた投稿メッセージとなることが想定されます。これは次のような形式になります。

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay 構成 .NET の例

FairPlay ライセンスを構成するには、Media Services API を使用します。 プレーヤーが FairPlay の保護されたコンテンツを再生しようとすると、ライセンスを取得する要求がライセンス配信サービスに送信されます。 ライセンス サービスが要求を承認すると、サービスがライセンスを発行します。 これはクライアントに送信され、指定されたコンテンツの復号化と再生に使用されます。

> [!NOTE]
> 通常、証明書と ASK は 1 組だけなので、FairPlay ポリシー オプションを構成する必要があるのは 1 回のみです。

次の例では、[Media Services .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) が使用されてライセンスが構成されています。

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>次の手順

[DRM での保護](protect-with-drm.md)に関するページを参照してください。
