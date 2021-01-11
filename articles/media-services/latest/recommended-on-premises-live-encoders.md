---
title: Media Services で推奨されるライブ ストリーミング エンコーダー - Azure | Microsoft Docs
description: Media Services で推奨されるライブ ストリーミング オンプレミス エンコーダーについて知る
services: media-services
keywords: エンコード;エンコーダー;メディア
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 08/31/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: 3532032f8fd3ac6e673d3913fd13f7f83ae7759e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295361"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>検証済みのオンプレミス ライブ ストリーミング エンコーダー

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services では、[ライブ イベント](/rest/api/media/liveevents) (チャネル) は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。 ライブ イベントは、次の 2 つの方法のいずれかでライブ入力ストリームを受信します。

* オンプレミスのライブ エンコーダーは、マルチビットレート RTMP またはスムーズ ストリーミング (フラグメント化 MP4) のストリームを、Media Services によるライブ エンコードの実行が無効な Live Event に送信します。 取り込んだストリームは、追加の処理なしで Live Event を通過します。 この方式は、 **パススルー**と呼ばれます。 ライブ エンコーダーの場合は、シングルビットレートのストリームではなく、マルチビットレートのストリームをパススルー ライブ イベントに送信して、クライアントへのアダプティブ ビットレート ストリーミングを可能にすることをお勧めします。 

    パススルー ライブ イベントにマルチビットレートのストリームを使用している場合は、再生側での予期しない動作を避けるために、異なるビットレートでのビデオの GOP サイズおよびビデオ フラグメントを同期する必要があります。

  > [!TIP]
  > パススルー方式を使用することが、ライブ ストリーミングを行う最も経済的な方法です。
 
* オンプレミスのライブ エンコーダーでは、次のいずれかの形式で、Media Services によるライブ エンコードが有効な Live Event にシングル ビットレート ストリームが送信されます。RTMP またはスムーズ ストリーミング (フラグメント化 MP4)。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードが Live Event で実行されます。

この記事では、検証済みのオンプレミス ライブ ストリーミング エンコーダーについて説明します。 検証は、ベンダーの自己検証または顧客の検証によって行われます。 Microsoft Azure Media Services では、各エンコーダーの完全または厳密なテストは実行されず、更新プログラムの再検証は継続的に行われません。 オンプレミス ライブ エンコーダーを確認する方法については、[オンプレミス エンコーダーの確認](become-on-premises-encoder-partner.md)に関するページを参照してください。

Media Services でのライブ エンコードの詳細については、[Media Services v3 でのライブ ストリーミング](live-streaming-overview.md)に関するページをご覧ください。

## <a name="encoder-requirements"></a>エンコーダーの要件

HTTPS または RTMPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。

## <a name="live-encoders-that-output-rtmp"></a>RTMP を出力するライブ エンコーダー

Media Services では、RTMP を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `rtmp://` または `rtmps://` です。

RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 1935 と 1936 が開いていることを確認します。<br/><br/>
RTMPS を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 2935 と 2936 が開いていることを確認します。

> [!NOTE]
> RTMPS プロトコルを使用する場合、エンコーダーによって TLS 1.2 がサポートされている必要があります。

- Adobe Flash Media Live Encoder 3.2
- [Blackmagic ATEM Mini および ATEM Mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (バージョン 2.14.15 以降)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 および Hero 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs OBS](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Telestream Wirecast (TLS 1.2 の要件によりバージョン 13.0.2 以降)
- Telestream Wirecast S (RTMP のみがサポートされています。 TLS 1.2 以降がないため、RTMPS はサポートされません)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> 上記のエンコーダーの一覧は、推奨リストにすぎません。 エンコーダーは、Microsoft によって継続的にテストまたは検証されるわけではなく、エンコーダー ベンダーやオープン ソース プロジェクトによって、互換性が損なわれ得る更新または重大な変更が導入される可能性があります。 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>フラグメント化 MP4 を出力するライブ エンコーダー (スムーズ ストリーミングの取り込み)

Media Services では、マルチビットレートのスムーズ ストリーミング (フラグメント化 MP4) を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `http://` または `https://` です。

> [!NOTE]
> HTTPS プロトコルを使用する場合、エンコーダーで TLS 1.2 がサポートされている必要があります。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (TLS 1.2 の要件により 2.14.15 以降)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Imagine Communications Selenio MCP3
- Media Excel Hero Live と Hero 4K (UHD/HEVC)

> [!TIP]
>  複数の言語でライブ イベント (たとえば、英語のオーディオ トラックやスペイン語のオーディオ トラックなど) をストリーミングする場合は、パススルー ライブ イベントにライブ フィードを送信するように構成された Media Excel ライブ エンコーダーを利用して、これを実現できます。

> [!WARNING]
> 上記のエンコーダーの一覧は、推奨リストにすぎません。 エンコーダーは、Microsoft によって継続的にテストまたは検証されるわけではなく、エンコーダー ベンダーやオープン ソース プロジェクトによって、互換性が損なわれ得るサポートやバグが導入される可能性が常にあります。 

## <a name="configuring-on-premises-live-encoder-settings"></a>オンプレミス ライブ エンコーダーの設定を構成する

ご利用のライブ イベントの種類に有効な設定に関する詳細については、「[ライブ イベントの種類の比較](live-event-types-comparison.md)」を参照してください。

### <a name="playback-requirements"></a>再生要件

コンテンツを再生するには、音声と映像の両方のストリームが存在する必要があります。 映像のみをストリーム再生することはできません。

### <a name="configuration-tips"></a>構成上のヒント

- 可能な限り、有線のインターネット接続を使用します。
- 帯域幅要件の目安は、ストリーミングのビットレートの 2 倍です。 必須ではありませんが、この単純なルールはネットワークの混雑による影響を軽減するのに役立ちます。
- ソフトウェア ベースのエンコーダーを使用する際は、不要なプログラムを終了します。
- プッシュの開始後にエンコーダーの構成を変更すると、イベントに悪影響を与えます。 構成を変更すると、イベントが不安定になる可能性があります。 
- Azure Media Services との互換性を維持するために、常に新しいバージョンのエンコーダー ソフトウェアをテストして検証します。 Microsoft では、この一覧にあるエンコーダーの再検証は行いません。また、ほとんどの検証は、"自己認定" としてソフトウェア ベンダーによって直接行われます。
- 十分な時間をかけてイベントを設定してください。 高スケールのイベントの場合、イベントの 1 時間前に設定を開始することをお勧めします。
- H.264 ビデオと AAC-LC オーディオ コーデックの出力を使用します。
- ブロードキャストしているライブ イベントの種類でサポートされている解像度およびフレーム レートを維持します (たとえば、60 fps は現在拒否されています)。
- ビデオの品質全般にキー フレームまたは GOP のテンポラル アラインメントが確実にあるようにします。
- ビデオの品質ごとに一意のストリーム名があることを確認してください。
- 最適なアダプティブ ビットレート パフォーマンスを得るには、高レベルの CBR エンコードを使用することをお勧めします。

> [!IMPORTANT]
> クラウドにフラグメントをアップロードするときに、マシン (CPU、メモリなど) の物理状態を監視するには、CPU と IO の操作が必要です。 エンコーダーの設定を変更する場合は、変更を有効にするためにチャネルまたはライブ イベントを確実にリセットします。

## <a name="see-also"></a>関連項目

[Media Services v3 を使用したライブ ストリーミング](live-streaming-overview.md)

## <a name="next-steps"></a>次のステップ

[エンコーダーを確認する方法](become-on-premises-encoder-partner.md)
