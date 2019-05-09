---
title: ビデオ プレーヤー アプリケーションの開発
description: このトピックでは、Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できるプレーヤー フレームワークとプラグインへのリンクを提供します。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880689"
---
# <a name="develop-video-player-applications"></a>ビデオ プレーヤー アプリケーションの開発
## <a name="overview"></a>概要
Azure Media Services には、ほとんどのプラットフォーム (iOS デバイス、Android デバイス、Windows、Windows Phone、Xbox、セットトップ ボックスなど) 向けのリッチで動的なクライアント再生アプリケーションの作成に必要なツールが用意されています。 このトピックでは、Azure Media Services からのストリーミング メディアを使用する独自のクライアント アプリケーションの開発に使用できる SDK とプレーヤー フレームワークへのリンクもご紹介します。

>[!NOTE]
>AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo) は、さまざまなブラウザーやデバイス上で Microsoft Azure Media Services からメディア コンテンツを再生できる組み込みの Web ビデオ プレーヤーです。 Azure Media Player では、HTML5、Media Source Extensions (MSE)、Encrypted Media Extensions (EME) といった業界標準を使用して、強化されたアダプティブ ストリーミングを提供します。 デバイスやブラウザーでこれらの標準を使用できない場合、Azure Media Player は Flash や Silverlight をフォールバック テクノロジとして使用します。 使用する再生テクノロジにかかわらず、開発者は統一された JavaScript インターフェイスを使用して API にアクセスできます。 これにより、Azure Media Services で提供されるコンテンツを、さまざまなデバイスやブラウザーで追加作業なしで再生できるようになります。

Microsoft Azure Media Services では、DASH、スムーズ ストリーミング、HLS ストリーミングなどの形式でコンテンツを提供して再生できます。 Azure Media Player はこれらの多用な形式を考慮し、プラットフォームやブラウザーの性能に基づいて最適なリンクを自動的に再生します。 また、Microsoft Azure Media Services では PlayReady 暗号化や AES-128 ビットエンベロープ暗号化を使用した資産の動的暗号化も可能です。 Azure Media Player では、コンテンツが適切に構成されていれば、PlayReady 暗号化や AES-128 ビットエンベロープ暗号化されたコンテンツの暗号化を解除できます。 

詳細:

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player のドキュメント](https://aka.ms/ampdocs) 
* [Azure Media Player の概要に関するブログ](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [Azure Media Player を最新の状態に維持するためのサインアップのページ](https://aka.ms/ampsignup)
* [新しい機能に関する要望、アイデア、フィードバックを追加するページ](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>プレーヤー アプリケーションを作成するためのその他のツール
次のような SDK を使用することもできます。

* [Smooth Streaming Client SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [スムーズ ストリーミング用 Windows ストア アプリ](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: プレーヤー フレームワーク](https://playerframework.codeplex.com/) 
* [HTML5 プレーヤー フレームワークに関するドキュメント](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [OSMF 用 Microsoft Smooth Streaming Plugin](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Licensing Microsoft® Smooth Streaming Client Porting Kit](https://aka.ms/sspk) 
* [Xbox ビデオ アプリケーションの開発](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>広告
Azure Media Services では、Windows メディア プラットフォームのプレーヤー フレームワークを通じて広告の挿入がサポートされています。 広告がサポートされるプレーヤー フレームワークは、Windows 8、Silverlight、Windows Phone 8、iOS デバイスで使用できます。 各プレーヤー フレームワークには、プレーヤー アプリケーションの実装方法を示すサンプル コードが含まれています。 メディアには、3 種類の広告を挿入できます。

線形 – メイン ビデオを一時停止するフル フレーム広告

非線形 – メイン ビデオの再生中に表示されるオーバーレイ広告 (通常は、プレーヤー内に配置されるロゴや他の静的イメージ)

コンパニオン – プレーヤーの外部に表示される広告

広告は、メイン ビデオの時系列のどのポイントにも配置できます。 広告をいつ再生し、どの広告を再生するかをプレーヤーに通知する必要があります。 これは、一連の標準 XML ベース ファイルである Video Ad Service Template (VAST)、Digital Video Multiple Ad Playlist (VMAP)、Media Abstract Sequencing Template (MAST)、Digital Video Player Ad Interface Definition (VPAID) を使用して行います。 VAST ファイルは、表示する広告を指定します。 VMAP ファイルは、各広告をいつ再生するかを指定します。このファイルには VAST XML が含まれています。 MAST ファイルを使用して広告の順序を指定することもできます。このファイルにも VAST XML が含まれています。 VPAID ファイルは、ビデオ プレーヤーと広告や広告サーバーの間のインターフェイスを定義します。 詳細については、「[広告の挿入](https://msdn.microsoft.com/library/dn387398.aspx)」をご覧ください。

ライブ ストリーミング ビデオでのクローズド キャプションと広告のサポートの詳細については、「 [サポートされる字幕と広告挿入の標準](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad)」をご覧ください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[DASH.js を使用した HTML5 アプリケーションへの MPEG-DASH アダプティブ ストリーミング ビデオの埋め込み](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js リポジトリ](https://github.com/Dash-Industry-Forum/dash.js)

