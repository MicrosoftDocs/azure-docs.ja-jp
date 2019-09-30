---
title: フィルターと Azure Media Services の動的マニフェスト | Microsoft Docs
description: このトピックでは、クライアントがストリームの特定のセクションをストリームする際に使用できるフィルターを作成する方法について説明します。 Media Services では、動的マニフェストを作成してこの選択型ストリーミングをアーカイブします。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: dc9f59894da071e956283591cf7206bc371650b7
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991429"
---
# <a name="pre-filtering-manifests-by-using-dynamic-packager"></a>Dynamic Packager を使用してマニフェストにフィルターを事前適用する

アダプティブ ビットレート ストリーミング コンテンツをデバイスに配信するときは、多くの場合、特定のデバイス機能または利用可能なネットワーク帯域幅をターゲットにするために、マニフェストの複数のバージョンを公開する必要があります。 [Dynamic Packager](dynamic-packaging-overview.md) を使用すると、特定のコーデック、解像度、ビットレート、およびオーディオ トラックの組み合わせをその場で除外できるフィルターを指定できるので、複数のコピーを作成する必要がなくなります。 必要なのは、ターゲット デバイス (iOS、Android、SmartTV、またはブラウザー) とネットワーク機能 (高帯域幅、モバイル、または低帯域幅の各シナリオ) に合わせて構成されたフィルターの特定のセットを含む新しい URL を発行することだけです。 この場合、クライアントはクエリ文字列によって (利用可能な[アセット フィルターまたはアカウント フィルター](filters-concept.md)を指定して) コンテンツのストリーミングを操作したり、フィルターを使用してストリームの特定のセクションをストリーミングしたりできます。

いくつかの配信シナリオでは、ユーザーが特定のトラックにアクセスできないようにする必要があります。 たとえば、HD トラックを含むマニフェストを、特定のレベルの利用者には発行したくない場合があります。 または、追加トラックのメリットを受けない特定のデバイスへの配信コストを削減するために、特定のアダプティブ ビットレート (ABR) トラックを削除したい場合があります。 この場合、あらかじめ作成しておいたフィルターのリストを、作成時に[ストリーミング ロケーター](streaming-locators-concept.md)に関連付けることができます。 この場合、コンテンツのストリーミング方法はクライアントによって操作できず、**ストリーミング ロケーター**によって定義されます。

[ストリーミング ロケーターでのフィルター](filters-concept.md#associating-filters-with-streaming-locator)の指定に、クライアントが URL で指定する追加のデバイス固有フィルターを加えた、複合的なフィルタリングを実行できます。 これは、メタデータやイベント ストリーム、オーディオ言語、説明的なオーディオ トラックなどの追加トラックを制限するために役立つことがあります。 

このように、ストリームにさまざまなフィルターを指定できることで、強力な**動的マニフェスト**操作ソリューションが提供され、ターゲット デバイスの複数のユースケース シナリオをターゲットにすることが可能になります。 このトピックでは、**動的マニフェスト**に関連する概念を説明し、この機能を使用するシナリオの例を示します。

> [!NOTE]
> アセットやその既定のマニフェストが動的マニフェストによって変更されることはありません。 
> 

##  <a name="overview-of-manifests"></a>マニフェストの概要

Azure Media Services では、HLS、MPEG DASH、Smooth Streaming プロトコルがサポートされます。 ストリーミング クライアント マニフェスト (HLS のマスター再生リスト、DASH の Media Presentation Description [MPD]、および Smooth Streaming) は、URL 内の形式セレクターに基づいて、[ダイナミック パッケージ](dynamic-packaging-overview.md)の一部として動的に生成されます。 [一般的なオンデマンド ワークフロー](dynamic-packaging-overview.md#delivery-protocols)内の配信プロトコルをご覧ください。 

### <a name="get-and-examine-manifest-files"></a>マニフェスト ファイルを取得して調査する

動的に作成されたマニフェストに含めるべきストリーム (ライブまたはビデオ オン デマンド [VOD]) のトラックに基づいて、一連のフィルター トラック プロパティ条件を指定します。 トラックのプロパティを取得して調査するためには、まず Smooth Streaming マニフェストを読み込む必要があります。

[.NET を使用したファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-api.md#get-streaming-urls)に関するチュートリアルでは、.NET を使用してストリーミング URL を構築する方法が紹介されています。 そのアプリを実行すると、いずれかの URL が次の Smooth Streaming マニフェストを指し示します。`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`<br/> この URL をコピーしてブラウザーのアドレス バーに貼り付けます。 該当するファイルがダウンロードされます。 それを任意のテキスト エディターで開いてください。

REST の例については、[REST を使用したファイルのアップロード、エンコード、ストリーム配信](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)に関するページを参照してください。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>ビデオ ストリームのビットレートを監視する

[Azure Media Player のデモ ページ](https://aka.ms/azuremediaplayer)を使用して、ビデオ ストリームのビットレートを監視します。 デモ ページの **[診断]** タブに診断情報が表示されます。

![Azure Media Player の診断][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>次に例を示します。クエリ文字列にフィルターを含む URL

ABR ストリーミング プロトコルにフィルターを適用できます。HLS、MPEG-DASH、および Smooth Streaming) に適用できます。 次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>演奏フィルター処理

アセットを複数のエンコード プロファイル (H.264 Baseline、H.264 高、AACL、AACH、Dolby Digital Plus) と複数の品質ビットレートにエンコードすることを選択できます。 ただし、クライアント デバイスによっては、一部のアセットのプロファイルやビットレートをサポートしていない場合があります。 たとえば、古いバージョンの Android デバイスでは H.264 Baseline+AACL のみがサポートされます。 メリットが得られないデバイスに高いビットレートを送信すると、帯域幅とデバイスの計算が無駄になります。 このようなデバイスでは、表示用にスケールダウンするためだけに、取得した情報をすべてデコードしなくてはなりません。

動的マニフェストの場合、デバイス プロファイル (携帯電話、コンソール、HD/SD など) を作成し、各プロファイルの一部としてトラックや品質を含めることができます。 これは、演奏フィルター処理と呼ばれます。 次の図はその例を示しています。

![演奏フィルター処理の例][renditions2]

次の例では、エンコーダーを使用して中間ファイル アセットを 7 つの ISO MP4 ビデオ演奏にエンコードしました (180p ～ 1080p)。 エンコードされた資産は、[動的にパッケージ化された](dynamic-packaging-overview.md)ストリーミング プロトコルのいずれかに。HLS、MPEG DASH、Smooth のいずれかのストリーミング プロトコルに動的にパッケージ化できます。 

次の図の上部には、フィルターのないアセットの HLS マニフェストが示されています。 (7 つの演奏すべてが含まれています。)左下の図には、"ott" という名前のフィルターが適用された HLS マニフェストが表示されています。 "ott" フィルターでは、1 Mbps より低いビットレートをすべて削除するように指定されるので、下位 2 つの品質レベルが応答で除去されています。 右下の図には、"mobile" という名前のフィルターが適用された HLS マニフェストが表示されています。 "mobile" フィルターでは、解像度が 720 p を超える演奏を削除するように指定されるので、2 つの 1080 p の演奏が除去されました。

![演奏フィルター処理][renditions1]

## <a name="removing-language-tracks"></a>言語トラックを削除する
アセットには、英語、スペイン語、フランス語などの複数のオーディオ言語が含まれる場合があります。通常、Player SDK マネージャーには、既定のオーディオ トラックと、ユーザーによって選択された使用可能なオーディオ トラックがあります。

このような Player SDK の開発は、デバイス固有のプレーヤー フレームワークによって異なる実装が必要になるため困難です。 また、一部のプラットフォームでは Player API に制限があり、オーディオ選択機能が含まれません。このため、ユーザーは既定のオーディオ トラックを選択、変更できません。アセット フィルターを使用すれば、目的のオーディオ言語のみを含むフィルターを作成することで、動作を制御できます。

![言語トラックのフィルター処理][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>アセットの開始をトリミングする

ほとんどのライブ ストリーミング イベントでは、実際のイベントの前にオペレータがいくつかのテストを行います。 たとえば、イベントの開始前に、"プログラムが間もなく開始します" などのスレートを追加する場合があります。 

プログラムをアーカイブすると、テストとスレートのデータもアーカイブされ、プレゼンテーションに追加されます。 しかし、この情報をクライアントに表示すべきではありません。 動的マニフェストを使用することで、開始時刻フィルターを作成し、マニフェストから不要なデータを削除できます。

![トリミングの開始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>ライブ アーカイブからサブクリップ (ビュー) を作成する

多くのライブ イベントは長時間実行され、ライブ アーカイブに複数のイベントが含まれる場合があります。 ライブ イベントの終了後は、ブロードキャスタが、ライブ アーカイブを論理プログラムの開始と終了シーケンスに分割できます。 

ライブ アーカイブの後処理や個別のアセットの作成を行わずに、これらの仮想プログラムを個別に発行できます (これでは CDN にキャッシュされた既存のフラグメントを利用できません)。 このような仮想プログラムの例として、フットボールやバスケットボールの試合のクオーター、野球のイニング、スポーツ プログラムの各イベントなどが挙げられます。

動的マニフェストでは、開始時刻と終了時刻を使用してフィルターを作成し、ライブ アーカイブ上に仮想ビューを作成できます。 

![サブクリップ フィルター][subclip_filter]

フィルター選択されたアセットを次に示します。

![スキー][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>プレゼンテーション ウィンドウ (DVR) を調整する

現在、Azure Media Services では、1 分から 25 時間の期間を構成できる循環アーカイブを提供しています。 マニフェストのフィルタ―処理を使用して、メディアを削除せずにアーカイブ上に DVR のローリングウィンドウを作成できます。 ブロードキャスタが、限定的な DVR ウィンドウを配信してライブ エッジとともに移動しつつ、規模の大きいアーカイブ ウインドウも確保しておく必要があるといったシナリオはよくあります。 ブロードキャスターは、クリップを強調表示するために DVR ウィンドウ外のデータを使用し、さまざまなデバイスに合わせて異なる DVR ウィンドウを用意する必要があります。 たとえば、ほとんどのモバイル デバイスでは大きな DVR ウィンドウに対応していません (DVR ウィンドウはモバイル デバイスでは 2 分、デスクトップ クライアントでは 1 時間使用できます)。

![DVR ウィンドウ][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>LiveBackoff (ライブ位置) を調整する

マニフェストのフィルター処理を使用して、ライブ プログラムのライブ エッジから数秒を削除できます。 フィルター処理により、ブロードキャスターはプレビュー公開ポイントでプレゼンテーションを視聴し、視聴者がストリームを受信する前の広告の挿入ポイントを作成できます (30 秒後退します)。 ブロードキャスターは、広告のチャンスの前にクライアントが情報を受信して処理するよう、これらの広告をクライアント フレームワークにプッシュできます。

広告のサポートに加えて、LiveBackoff 設定はビューアーの位置の調整に使用することもできます。これにより、クライアントの接続が安定せず、ライブ エッジに到達した場合でも、サーバーからフラグメントを取得できます。 このようにすると、クライアントで HTTP 404 または 412 エラーは発生しません。

![ライブ バックオフのフィルター][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>複数のルールを 1 つのフィルターに組み合わせる

複数のフィルター処理ルールを 1 つのフィルターに組み合わせることができます。 たとえば、"範囲ルール" を定義してスレートをライブ アーカイブから削除したり、使用可能なビット レートをフィルターで除外することもできます。 複数のフィルター処理ルールを適用した場合、最終的な結果は、すべての規則の共通部分になります。

![複数のフィルター処理ルール][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>複数のフィルターを結合する (フィルターの構成)

1 つの URL で複数のフィルターを結合することもできます。 次のシナリオは、フィルターを結合する理由を示しています。

1. Android や iPad などのモバイル デバイス用にビデオ品質をフィルター処理する必要があります (ビデオ品質を制限するため)。 不要な品質を取り除くために、デバイスのプロファイルに適したアカウント フィルターを作成します。 新たに関連付けを行わなくても、同じ Media Services アカウントに含まれるすべてのアセットにアカウント フィルターを使用できます。
1. また、資産の開始時刻と終了時刻を調整することもできます。 これを行うには、アセット フィルターを作成し、開始時刻と終了時刻を設定します。 
1. これらの両方のフィルターを結合する必要があります。 結合しない場合、品質フィルタリングをトリミング フィルターに追加する必要がありますが、このようにするとフィルターが使いづらくなります。


フィルターを結合するには、フィルター名をマニフェスト/再生リスト URL にセミコロン区切り形式で設定する必要があります。 品質をフィルター処理する *MyMobileDevice* という名前のフィルターと、特定の開始時刻を設定する *MyStartTime* という名前のフィルターがあると想定します。 最大 3 つのフィルターを結合できます。 

詳細については、 [このブログの投稿](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)を参照してください。

## <a name="considerations-and-limitations"></a>考慮事項と制限事項

- VOD フィルターの場合、**forceEndTimestamp**、**presentationWindowDuration**、**liveBackoffDuration** の値は設定しないでください。 これらは、ライブ フィルターのシナリオでのみ使用されます。 
-  動的マニフェストは GOP 境界 (キー フレーム) 内で動作するため、トリミングの正確度は GOP に応じます。
-  アカウント フィルターとアセット フィルターには同じフィルター名を使用できます。 アセット フィルターの方が優先順位が高く、アカウント フィルターをオーバーライドします。
- フィルターを更新した場合、ストリーミング エンドポイントでルールが更新されるまで最大 2 分かかります。 フィルターを使用してコンテンツを処理した (さらに、プロキシと CDN のキャッシュにコンテンツをキャッシュした) 場合、これらのフィルターを更新するとプレーヤーでエラーが発生します。 フィルターを更新した後にキャッシュをクリアすることをお勧めします。 このオプションが利用できない場合は、別のフィルターを使用することを検討してください。
- マニフェストは、顧客が手動でダウンロードして厳密な開始タイム スタンプとタイム スケールを解析する必要があります。
    
    - アセットのトラックのプロパティを調べるには、[マニフェスト ファイルを取得して調査](#get-and-examine-manifest-files)します。
    - アセット フィルターのタイムスタンプのプロパティを設定するための数式は次のとおりです。 <br/>startTimestamp = &lt;マニフェストにおける開始時間&gt; +  &lt;所定のフィルター開始時間 (秒)&gt; * タイムスケール

## <a name="next-steps"></a>次の手順

次の記事では、プログラムを使ってフィルターを作成する方法が紹介されています。  

- [REST API を使用したフィルターの作成](filters-dynamic-manifest-rest-howto.md)
- [.NET を使用したフィルターの作成](filters-dynamic-manifest-dotnet-howto.md)
- [CLI を使用したフィルターの作成](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
