---
title: フィルターと動的マニフェスト | Microsoft Docs
description: このトピックでは、クライアントがストリームの特定のセクションをストリームする際に使用できるフィルターを作成する方法について説明します。 Media Services では、動的マニフェストを作成してこの選択型ストリーミングをアーカイブします。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 002a7c61d6760decf65016870739ab62b15d5c72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013688"
---
# <a name="filters-and-dynamic-manifests"></a>フィルターと動的マニフェスト

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="使用している Media Services のバージョンを選択してください:"]
> * [Version 2](media-services-dynamic-manifest-overview.md)
> * [Version 3](../latest/filters-dynamic-manifest-overview.md)

Media Services のリリース 2.17 以降では、資産にフィルターを定義できます。 これらのフィルターは、ビデオの 1 つのセクションのみの再生や (ビデオ全体を再生するのではなく)、顧客のデバイスが処理できるオーディオ サブセットとビデオ演奏のみの再生 (資産に関連付けられているすべての演奏ではなく) などを顧客が選択できるようにする、サーバー側のルールです。 この資産のフィルター処理は **動的マニフェスト** によって実行されます。これは、指定したフィルターに基づいてビデオをストリームする必要がある顧客のリクエストに応じて作成されます。

このトピックでは、顧客にとってどのフィルターを使用することが効果的であるか一般的なシナリオを示し、プログラムによるフィルターの作成方法を説明するトピックへのリンクを示します。

## <a name="overview"></a>概要
コンテンツ (ストリーミング ライブ イベントまたはビデオ オン デマンド) を顧客に配信する場合、その目標は、異なるネットワーク条件におけるさまざまなデバイスに高品質のビデオを配信することにあります。 この目標を達成するために、次の操作を行います。

* ストリームをマルチビットレート ([アダプティブ ビットレート](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) ビデオ ストリームにエンコードします (これにより品質とネットワーク条件に対応)。 
* Media Services の [動的パッケージ](media-services-dynamic-packaging-overview.md) を使用して、ストリームをさまざまなプロトコルに動的に再パッケージ化します (これにより異なるデバイスでのストリーミングに対応)。 Media Services は、次のアダプティブ ビットレート ストリーミング配信テクノロジをサポートしています。HTTP ライブ ストリーミング (HLS)、Smooth Streaming、および MPEG DASH。 

### <a name="manifest-files"></a>マニフェスト ファイル
アダプティブ ビットレート ストリーミングのアセットをエンコードすると、 **マニフェスト** (再生リスト) ファイルが作成されます (ファイルはテキスト ベースか XML ベース)。 **マニフェスト** ファイルには、トラックの種類 (オーディオ、ビデオ、テキスト)、トラック名、開始時刻と終了時刻、ビットレート (品質)、トラック言語、プレゼンテーション ウィンドウ (固定時間のスライディング ウィンドウ)、ビデオ コーデック (FourCC) などの、ストリーミング メタデータが含まれます。 また、次に再生可能なビデオ フラグメントとその場所の情報を通知して、次のフラグメントを取得するようにプレイヤーに指示します。 フラグメント (またはセグメント) とは、ビデオ コンテンツの実際の「チャンク」です。

以下に、マニフェスト ファイルの例を示します。 

```xml
<?xml version="1.0" encoding="UTF-8"?>    
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

<StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
<QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
<QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
<QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
<QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
<QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
<QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

<c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
</StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

</SmoothStreamingMedia>
```

### <a name="dynamic-manifests"></a>動的マニフェスト
アセットの既定のマニフェスト ファイルに記述されている情報よりも、さらに高い柔軟性をクライアントが必要とする [シナリオ](media-services-dynamic-manifest-overview.md#scenarios) があります。 次に例を示します。

* デバイスに固有: コンテンツの再生に使用するデバイスでサポートしている演奏や言語のトラックのみを指定して配信する場合 (「演奏フィルタ―処理」)。 
* マニフェストを減らして、ライブ イベントのサブクリップのみを表示する場合 (「サブクリップ フィルター処理」)
* ビデオの開始をトリミングする場合 (「ビデオのトリミング」)
* プレゼンテーション ウィンドウ (DVR) を調整し、プレーヤーの DVR ウィンドウの長さを限定する ("プレゼンテーション ウィンドウの調整")。

このような柔軟性を実現するために、Media Services では定義済みの **フィルター** に基づいた [動的マニフェスト](media-services-dynamic-manifest-overview.md#filters)を用意しています。  フィルターを定義しておくと、クライアントはそのフィルターを使用して、ビデオの特定の演奏やサブクリップをストリーミングできるようになります。 ストリーミング URL にフィルターを指定することもできます。 フィルターは、[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md) によってサポートされるアダプティブ ビットレート ストリーミング プロトコル (HLS、MPEG-DASH、および Smooth Streaming) に適用できます。 次に例を示します。

フィルターを使用した MPEG DASH URL

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)`

フィルターを使用した Smooth Streaming URL

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)`


コンテンツを配信してストリーミング URL をビルドする方法の詳細については、「 [コンテンツの配信の概要](media-services-deliver-content-overview.md)」をご覧ください。

> [!NOTE]
> 動的マニフェストではアセットやそのアセットの既定のマニフェストは変更されないことにご注意ください。 クライアントはストリームにフィルターを使用するかしないかを選択できます。 
> 
> 

### <a name="filters"></a><a id="filters"></a>フィルター
次の 2 種類のアセット フィルターがあります。 

* グローバル フィルター (Azure Media Services アカウントのすべてのアセットに適用可能。有効期間付きアカウント) 
* ローカル フィルター (作成時にフィルターに関連付けられたアセットにのみ適用可能。有効期間付きアセット) 

グローバル フィルターとローカル フィルターのプロパティはまったく同じです。 この 2 つのフィルターの主な違いは、適しているシナリオが異なることです。 グローバル フィルターは通常、デバイス プロファイル (演奏フィルター処理) に適していますが、ローカル フィルターは特定のアセットのトリミングに使用できます。

## <a name="common-scenarios"></a><a id="scenarios"></a>一般的なシナリオ
コンテンツ (ストリーミング ライブ イベントまたはビデオ オンデマンド) を顧客に配信する場合、その目標は、異なるネットワーク条件におけるさまざまなデバイスに高品質のビデオを配信することにあると既に説明しました。 それに加えて、アセットのフィルタ―処理や、 **動的マニフェスト** の使用に関するその他の要件も考慮する必要があります。 次のセクションでは、さまざまなフィルター処理のシナリオについて、簡単に説明します。

* 特定のデバイスで処理できるオーディオやビデオ演奏のサブセットのみを指定する (アセットに関連付けられているすべての演奏ではなく)。 
* ビデオの 1 つのセクションのみを再生する (ビデオ全体を再生するのではなく)。
* DVR プレゼンテーション ウィンドウを調整する。

## <a name="rendition-filtering"></a>演奏フィルター処理
アセットは、複数のエンコード プロファイル (H.264 Baseline、H.264 高、AACL、AACH、Dolby Digital Plus) と複数の品質ビットレートにエンコードできます。 ただし、クライアント デバイスによっては、一部のアセットのプロファイルやビットレートをサポートしていない場合があります。 たとえば、古いバージョンの Android デバイスでは H.264 Baseline+AACL のみをサポートしています。 品質のメリットを利用できないデバイスに高いビットレートを送信すると、帯域幅とデバイスの計算が無駄になります。 このようなデバイスでは、表示用にスケールダウンするためだけに、取得した情報をすべてデコードしなくてはなりません。

動的マニフェストの場合、携帯電話、コンソール、HD/SD などのデバイス プロファイルを作成し、各プロファイルの一部としてトラックや品質を含めることができます。

![演奏フィルター処理例][renditions2]

次の例では、エンコーダーを使用して中間ファイル アセットを 7 つの ISO MP4 ビデオ演奏にエンコードしました (180p ～ 1080p)。 エンコード済みのアセットは、HLS、Smooth、MPEG DASH のいずれかのストリーミング プロトコルに動的にパッケージ化できます。  図の上部には、フィルターを使用しないアセット用の HLS マニフェストが表示されています (これには全 7 演奏が含まれています)。  左下には、"ott" という名前のフィルターが適用された HLS マニフェストが表示されています。 "ott" フィルターでは、下の 2 つの品質レベルが応答で除去されてしまうため、1 Mbps より低いビットレートをすべて削除するように指定します。 右下には、"mobile" という名前のフィルターが適用された HLS マニフェストが表示されています。 "mobile" フィルターでは、2 つの 1080 p の演奏が除去されてしまうため、解像度が 720 p を超える演奏を削除するように指定します。

![演奏フィルター処理][renditions1]

## <a name="removing-language-tracks"></a>言語トラックを削除する
アセットには、英語、スペイン語、フランス語などの複数のオーディオ言語が含まれる場合があります。通常、Player SDK マネージャーには、既定のオーディオ トラックと、ユーザーによって選択された使用可能なオーディオ トラックがあります。 このような Player SDK の開発は、デバイス固有のプレーヤー フレームワークによってさまざまな実装が必要になるため困難です。 また、一部のプラットフォームでは Player API に制限があり、オーディオ選択機能が含まれません。このため、ユーザーは既定のオーディオ トラックを選択、変更できません。アセット フィルターを使用すれば、目的のオーディオ言語のみを含むフィルターを作成することで、動作を制御できます。

![言語トラックのフィルター処理][language_filter]

## <a name="trimming-start-of-an-asset"></a>アセットのトリミングを開始する
ほとんどのライブ ストリーミング イベントでは、実際のイベントの前にオペレータがいくつかのテストを行います。 たとえば、イベントの開始前に、"プログラムが間もなく開始します" などのスレートを追加する場合があります。 プログラムをアーカイブすると、テストとスレートのデータもアーカイブされ、プレゼンテーションに追加されます。 しかし、この情報をクライアントに表示すべきではありません。 動的マニフェストを使用することで、開始時刻フィルターを作成し、マニフェストから不要なデータを削除できます。

![トリミングの開始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>ライブ アーカイブからサブクリップ (ビュー) を作成する
多くのライブ イベントは長時間実行され、ライブ アーカイブに複数のイベントが含まれる場合があります。 ライブ イベントの終了後は、ブロードキャスタが、ライブ アーカイブを論理プログラムの開始と終了シーケンスに分割できます。 次に、ライブ アーカイブの後処理や個別のアセットの作成を行わずに、これらの仮想プログラムを個別に発行します (これでは CDN にキャッシュされた既存のフラグメントを利用できません)。 このような仮想プログラムの例として、フットボールやバスケットボールの試合のクオーター、野球のイニング、スポーツ プログラムの各イベントなどが挙げられます。

動的マニフェストでは、開始時刻と終了時刻を使用したフィルターを作成し、ライブ アーカイブ上に仮想ビューを作成できます。 

![サブクリップ フィルター][subclip_filter]

フィルター処理されたアセット:

![スキー][skiing]

## <a name="adjusting-presentation-window-dvr"></a>プレゼンテーション ウィンドウ (DVR) を調整する
現在、Azure Media Services では 5 分～ 25 時間の期間を構成できる循環アーカイブを提供しています。 マニフェストのフィルタ―処理を使用して、メディアを削除せずにアーカイブ上に DVR のローリングウィンドウを作成できます。 ブロードキャスタが、限定的な DVR ウィンドウを配信してライブ エッジとともに移動しつつ、規模の大きいアーカイブ ウインドウも確保しておく必要があるといったシナリオはよくあります。 ブロードキャスターは、クリップを強調表示するために DVR ウィンドウ外のデータを使用し、さまざまなデバイスに合わせて異なる DVR ウィンドウを用意する必要があります。 たとえば、ほとんどのモバイル デバイスでは大きな DVR ウィンドウに対応していません (DVR ウィンドウはモバイル デバイスでは 2 分、デスクトップ クライアントでは 1 時間使用できます)。

![DVR ウィンドウ][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (ライブ位置) を調整する
マニフェストのフィルター処理を使用して、ライブ プログラムのライブ エッジから数秒を削除できます。 フィルター処理により、ブロードキャスタはプレビュー公開ポイントでプレゼンテーションを視聴し、視聴者がストリームを受信する前の広告の挿入ポイントを作成できます (30 秒後退します)。 ブロードキャスタは、広告のチャンスの前にクライアントが情報を受信して処理するよう、これらの広告をクライアント フレームワークにプッシュできます。

広告のサポートに加えて、LiveBackoff 設定はビューアーの位置の調整に使用することもできます。これにより、クライアントの接続が安定せず、ライブ エッジに到達した場合でも、HTTP 404 または 412 エラーを発生させずにサーバーからフラグメントを取得できます。

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>複数のルールを 1 つのフィルターに組み合わせる
複数のフィルター処理ルールを 1 つのフィルターに組み合わせることができます。 たとえば、"範囲ルール" を定義してスレートをライブ アーカイブから削除したり、使用可能なビット レートをフィルターで除外することもできます。 複数のフィルター規則を適用した場合、最終的な結果は、すべての規則の共通部分になります。

![multiple-rules][multiple-rules]

## <a name="create-filters-programmatically"></a>プログラムを使用してフィルターを作成する
次の記事では、フィルターに関連した Media Services のエンティティについて説明します。 この記事では、プログラムを使用してフィルターを作成する方法も示します。  

[REST API を使用してフィルターを作成する](media-services-rest-dynamic-manifest.md)

## <a name="combining-multiple-filters-filter-composition"></a>複数のフィルターを結合する (フィルターの構成)
1 つの URL で複数のフィルターを結合することもできます。 

次のシナリオは、フィルターを結合する理由を示しています。

1. Android や iPAD などのモバイル デバイス用にビデオ品質をフィルター処理する必要があります (ビデオ品質を制限するため)。 不要な品質を取り除くために、デバイスのプロファイルに適したグローバル フィルターを作成します。 この記事で前述したように、新たに関連付けを行わなくても、同じメディア サービス アカウントに含まれるすべての資産にグローバル フィルターを使用できます。 
2. また、資産の開始時刻と終了時刻を調整することもできます。 これを行うには、ローカル フィルターを作成し、開始時刻と終了時刻を設定します。 
3. これらの両方のフィルターを結合する必要があります (結合しない場合、品質フィルタリングをトリミング フィルターに追加する必要がありますが、このようにするとフィルターがさらに使いづらくなります)。

フィルターを結合するには、フィルター名をマニフェスト/再生リスト URL にセミコロンで区切って設定する必要があります。 品質をフィルター処理する *MyMobileDevice* という名前のフィルターと、特定の開始時刻を設定する *MyStartTime* という名前のフィルターがあると想定します。 次のようにして、これらを結合できます。

`http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)`

最大 3 つのフィルターを結合できます。 

詳細については、 [このブログ](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) をご覧ください。

## <a name="know-issues-and-limitations"></a>既知の問題と制限
* 動的マニフェストは GOP 境界 (キー フレーム) で動作するため、トリミングの正確度は GOP に応じます。 
* ローカル フィルターとグローバル フィルターに同じフィルター名を使用できます。 ローカル フィルターの方が優先順位が高く、グローバル フィルターをオーバーライドします。
* フィルターを更新する場合、ストリーミング エンドポイントでルールを更新するのに最大 2 分かかります。 いくつかのフィルターを使用してコンテンツが処理された場合 (プロキシと CDN にキャッシュされている)、プレイヤーでこれらのフィルターを更新すると失敗します。 フィルターを更新した後にキャッシュをクリアすることをお勧めします。 このオプションが利用できない場合は、別のフィルターを使用することを検討してください。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>参照
[顧客へのコンテンツの配信に関する概要](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
