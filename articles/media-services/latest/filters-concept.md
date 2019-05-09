---
title: Azure Media Services でのフィルターの定義
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 61b877c322fcd58472990c328beea2e309502bce
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652350"
---
# <a name="define-account-filters-and-asset-filters"></a>アカウント フィルターとアセット フィルターの定義  

コンテンツを顧客に配信する (イベントのライブ ストリーミングまたはビデオ オン デマンド) 際に、既定の資産のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツの資産フィルターを定義することができます。 

フィルターは、次のような操作を顧客に許可するサーバー側のルールです。 

- ビデオ全体を再生するのではなく、ビデオの 1 つのセクションのみを再生する。 例: 
  - マニフェストを減らして、ライブ イベントのサブクリップのみを表示する場合 (「サブクリップ フィルター処理」)、または
  - ビデオの開始をトリミングする場合 (「ビデオのトリミング」)
- コンテンツの再生に使用するデバイスでサポートされている演奏や言語のトラックのみを指定して配信する ("演奏フィルタ―処理")。 
- プレゼンテーション ウィンドウ (DVR) を調整し、プレーヤーの DVR ウィンドウの長さを限定する ("プレゼンテーション ウィンドウの調整")。

Media Services には、定義済みのフィルターに基づいた[動的マニフェスト](filters-dynamic-manifest-overview.md)が用意されています。 フィルターを定義すると、クライアントからストリーミング URL で使用できるようになります。 フィルターは、アダプティブ ビットレート ストリーミング プロトコル(Apple HTTP Live Streaming (HLS)、MPEG DASH、Smooth Streaming) に適用できます。

次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`<br/>HLS v3 の場合は、`format=m3u8-aapl-v3` を使用します。|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>フィルターを定義する

次の 2 種類のアセット フィルターがあります。 

* [アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters) (グローバル) - Azure Media Services アカウントのすべてのアセットに適用可能。アカウントの有効期間があります。
* [アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters) (ローカル) - 作成時にフィルターに関連付けられたアセットにのみ適用可能。アセットの有効期間があります。 

[アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters) タイプと[アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters) タイプは、フィルターの定義/記述に関して、まったく同じプロパティを持ちます。 ただし**アセット フィルター**を作成するときは、フィルターを関連付けるアセットの名前を指定する必要があります。

実際のシナリオに応じて、アセット フィルターとアカウント フィルターのどちらのタイプのフィルターが適しているかを判断することになります。 アカウント フィルターは、デバイス プロファイル (演奏フィルター処理) に適しています。一方、アセット フィルターは、特定のアセットをトリミングする目的で使用できます。

フィルターは、次のプロパティを使用して記述します。 

|名前|説明|
|---|---|
|firstQuality|フィルターの最高品質ビットレートです。|
|presentationTimeRange|プレゼンテーション時間の範囲です。 このプロパティは、マニフェストの開始/終了ポイント、プレゼンテーション ウィンドウの長さ、ライブ開始位置をフィルタリングする目的で使用します。 <br/>詳細については、「[PresentationTimeRange](#presentationtimerange)」を参照してください。|
|tracks|トラックの選択条件です。 詳細については、「[tracks](#tracks)」を参照してください。|

### <a name="presentationtimerange"></a>presentationTimeRange

このプロパティは、**アセット フィルター**で使用します。 このプロパティを**アカウント フィルター**で設定することはお勧めできません。

|名前|説明|
|---|---|
|**endTimestamp**|ビデオ オン デマンド (VOD) が対象となります。<br/>ライブ ストリーミング プレゼンテーションでは、ダイアログを表示せずに無視され、プレゼンテーションが終了してストリームが VoD になったときに適用されます。<br/>これは long 値であり、プレゼンテーションの絶対的な終了点を表します。最も近い次の GOP 開始に丸められます。 単位はタイムスケールであるため、3 分の場合、endTimestamp は 1800000000 となります。<br/>プレイリスト (マニフェスト) に含まれるフラグメントをトリミングするには、startTimestamp と endTimestamp を使用します。<br/>たとえば、既定のタイムスケールを使用する startTimestamp=40000000 と endTimestamp=100000000 では、VoD プレゼンテーションの 4 秒から 10 秒までのフラグメントを含むプレイリストが生成されます。 その境界をフラグメントがまたいだ場合、フラグメント全体がマニフェストに含められます。|
|**forceEndTimestamp**|ライブ ストリーミングのみに適用されます。<br/>endTimestamp プロパティが存在する必要があるかどうかを示します。 true の場合、endTimestamp を指定する必要があります。そうしないと、不適切な要求コードが返されます。<br/>使用できる値: false、true。|
|**liveBackoffDuration**|ライブ ストリーミングのみに適用されます。<br/> この値では、クライアントがシークできる最新のライブ位置が定義されます。<br/>このプロパティを使用すると、ライブ再生位置を遅らせ、プレーヤーのためにサーバー側のバッファーを作成することができます。<br/>このプロパティの単位はタイムスケールです (下記参照)。<br/>最大ライブ バックオフ期間は 300 秒 (3000000000) です。<br/>たとえば、2000000000 の値は、使用可能な最新のコンテンツが、リアル ライブ エッジから 20 秒遅れていることを意味します。|
|**presentationWindowDuration**|ライブ ストリーミングのみに適用されます。<br/>プレイリストに含めるフラグメントのスライディング ウィンドウを適用するには、presentationWindowDuration を使用します。<br/>このプロパティの単位はタイムスケールです (下記参照)。<br/>たとえば、2 分のスライディング ウィンドウを適用するには、presentationWindowDuration を 1200000000 に設定します。 ライブ エッジの 2 分以内のメディアがプレイリストに含められます。 その境界をフラグメントがまたいだ場合、フラグメント全体がプレイリストに含められます。 最小プレゼンテーション ウィンドウ期間は 60 秒です。|
|**startTimestamp**|ビデオ オン デマンド (VoD) またはライブ ストリーミングに適用されます。<br/>これは long 値であり、ストリームの絶対的な開始点を表します。 この値は最も近い次の GOP 開始に丸められます。 単位はタイムスケールであるため、15 秒の場合、startTimestamp は 150000000 となります。<br/>プレイリスト (マニフェスト) に含まれるフラグメントをトリミングするには、startTimestamp と endTimestamp を使用します。<br/>たとえば、既定のタイムスケールを使用する startTimestamp=40000000 と endTimestamp=100000000 では、VoD プレゼンテーションの 4 秒から 10 秒までのフラグメントを含むプレイリストが生成されます。 その境界をフラグメントがまたいだ場合、フラグメント全体がマニフェストに含められます。|
|**timescale**|プレゼンテーション時間の範囲のすべてのタイムスタンプと期間が対象となります。1 秒間のインクリメント数として指定されます。<br/>既定値は 10000000 (1 秒間に 1,000 万インクリメント) です。各インクリメントの長さは 100 ナノ秒となります。<br/>たとえば、startTimestamp を 30 秒に設定する場合、既定のタイムスケールを使うときに 300000000 の値を使用します。|

### <a name="tracks"></a>tracks

動的に作成されたマニフェストに含める必要があるストリーム (ライブ ストリーミングまたはビデオ オン デマンド) のトラックに基づいて、フィルター トラック プロパティ条件 (FilterTrackPropertyConditions) のリストを指定します。 フィルターは、**AND** と **OR** の論理演算を使用して組み合わせます。

フィルター トラック プロパティ条件は、トラックの種類、値 (以下の表で説明します)、演算 (Equal、NotEqual) を記述したものです。 

|名前|説明|
|---|---|
|**Bitrate**|フィルタリングにトラックのビットレートを使用します。<br/><br/>推奨される値は、bps (1 秒あたりのビット数) で表したビットレートの範囲です。 たとえば、「0-2427000」と指定します。<br/><br/>注: 特定のビットレート値、たとえば 250000 (bps) を使用することもできますが、厳密なビットレートはアセットごとに変動するので、この方法はお勧めしません。|
|**FourCC**|フィルタリングにトラックの FourCC 値を使用します。<br/><br/>この値は、[RFC 6381](https://tools.ietf.org/html/rfc6381) で規定されたコーデック形式の最初の要素です。 現在は、次のコーデックがサポートされています。 <br/>ビデオ: "avc1"、"hev1"、"hvc1"<br/>オーディオ: "mp4a"、"ec-3"<br/><br/>アセットのトラックの FourCC 値を調べるには、マニフェスト ファイルを取得して調査します。|
|**Language**|フィルタリングにトラックの言語を使用します。<br/><br/>この値は、含めたい言語のタグです (RFC 5646 で規定)。 例: "en"。|
|**Name**|フィルタリングにトラックの名前を使用します。|
|**Type**|フィルタリングにトラックの種類を使用します。<br/><br/>"video"、"audio"、"text" のいずれかの値を指定できます。|

## <a name="associate-filters-with-streaming-locator"></a>フィルターをストリーミング ロケーターに関連付ける

資産またはアカウント フィルターの一覧を指定できます。これはストリーミング ロケーターに適用されます。 [ダイナミック パッケージャー](dynamic-packaging-overview.md)は、このフィルターの一覧を、クライアントが URL で指定するフィルターとともに適用します。 この組み合わせは[動的マニフェスト](filters-dynamic-manifest-overview.md)を生成します。これは、URL のフィルター + ストリーミング ロケーターに指定するフィルターに基づいています。 フィルターを適用したいものの URL でフィルター名を公開したくない場合は、この機能を使用することをお勧めします。

## <a name="definition-example"></a>定義の例

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>次の手順

次の記事では、プログラムを使ってフィルターを作成する方法が紹介されています。  

- [REST API を使用したフィルターの作成](filters-dynamic-manifest-rest-howto.md)
- [.NET を使用したフィルターの作成](filters-dynamic-manifest-dotnet-howto.md)
- [CLI を使用したフィルターの作成](filters-dynamic-manifest-cli-howto.md)

