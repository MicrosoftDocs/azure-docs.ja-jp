---
title: Media Encoder Standard スキーマ | Microsoft Docs
description: この記事では、Media Encoder Standard プリセットのベースとなっている XML スキーマの要素と型をいくつか取り上げます。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901451"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard スキーマ
この記事では、[Media Encoder Standard プリセット](media-services-mes-presets-overview.md)のベースとなっている XML スキーマの要素と型をいくつか取り上げます。 ここでは要素とその有効な値について説明します。  

## <a name="Preset"></a> プリセット (ルート要素)
エンコード プリセットを定義します。  

### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Encoding** |[Encoding](media-services-mes-schema.md#Encoding) |ルート要素。入力ソースがエンコードされることを示します。 |
| **Outputs** |[Outputs](media-services-mes-schema.md#Output) |目的の出力ファイルのコレクション。 |
| **StretchMode**<br/>minOccurs="0"<br/>default="AutoSize|xs:string|出力ビデオ フレームのサイズ、パディング、ピクセル、ディスプレイの縦横比を制御します。 **StretchMode** には、次のいずれかの値を指定できます:**None**、**AutoSize** (既定)、**AutoFit**。<br/><br/>**なし**: 出力解像度 (プリセットの **Width** と **Height** など) に厳密に従います。入力ビデオのピクセル縦横比やディスプレイの縦横比は考慮されません。 [トリミング](media-services-crop-video.md)など、出力ビデオの縦横比が入力と異なるシナリオで推奨されます。 <br/><br/>**AutoSize**:出力解像度は、プリセットで指定されたウィンドウ (Width * Height) 内に収まります。 ただし、エンコーダーから生成される出力ビデオのピクセル縦横比は正方形 (1:1) となります。 そのため Width または Height の出力が、入力のディスプレイの縦横比と合わせるために、パディングなしでオーバーライドされることがあります。 たとえば入力が 1920 x 1080 であるとき、エンコーディング プリセットによって 1280 x 1280 が要求された場合、プリセットの Height 値がオーバーライドされ、出力は 1280 x 720 となり、入力の縦横比 (16:9) が維持されます。 <br/><br/>**AutoFit**:出力におけるアクティブなビデオ領域に入力と同じ縦横比を確保しつつ、目的の出力解像度を遵守するために、必要であれば出力ビデオを (レターボックスまたはピラーボックスで) パディングします。 たとえば入力が 1920 x 1080 であるとき、エンコーディング プリセットによって 1280 x 1280 が要求されたとします。 この場合、出力ビデオは 1280 x 1280 になりますが、実際には、縦横比が 16:9 である "アクティブ ビデオ" の 1280 x 720 の四角形を挟むように、その上下に高さ 280 ピクセルのレターボックス領域が追加されます。 もう 1 つの例として、入力が 1440 x 1080 であるとき、エンコーディング プリセットによって 1280 x 720 が要求された場合、出力は 1280 x 720 となりますが、縦横比が 4:3 である 960 x 720 の四角形を挟むように、幅 160 ピクセルのピラー ボックス領域が左右に追加されます。 

### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **バージョン**<br/><br/> 必須 |**xs: decimal** |プリセット バージョン。 xs:fractionDigits value="1" と xs:minInclusive value="1" という制限が適用されます。たとえば、**version="1.0"** という制限です。 |

## <a name="Encoding"></a> エンコード
次の要素のシーケンスが含まれます。  

### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |ビデオの H.264 エンコードの設定。 |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |オーディオの AAC エンコードの設定。 |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Bmp イメージの設定。 |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png イメージの設定。 |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg イメージの設定。 |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |現在サポートされているのは 1 パス エンコードだけです。 |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |IDR フレーム間の固定の間隔を秒単位で決定します。 これは GOP 期間とも呼ばれます。 エンコーダーがこの値を逸脱できるかどうかの制御については、**SceneChangeDetection** をご覧ください。 |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs: boolean** |true に設定すると、エンコーダーはビデオ内でシーン変更の検出を試行し、IDR フレームを挿入します。 |
| **Complexity**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |エンコード速度とビデオ品質の間のトレードオフを制御します。 次のいずれかの値を指定できます:**Speed**、**Balanced**、**Quality**<br/><br/> 既定値は**Balanced** |
| **SyncMode**<br/><br/> minOccurs="0" | |機能は今後のリリースで公開されます。 |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |出力ビデオ レイヤーのコレクション。 |

### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | 入力に映像が含まれていない場合、モノクロのビデオ トラックを挿入するようエンコーダーに強制できます。これを実行するには、Condition="InsertBlackIfNoVideoBottomLayerOnly" (最も低いビットレートでのみビデオを挿入) または Condition="InsertBlackIfNoVideo" (すべてのビットレートでビデオを挿入) を使用します。 詳細については、[こちらの記事](media-services-advanced-encoding-with-mes.md#no_video)を参照してください。|

## <a name="H264Layers"></a> H264Layers

既定では、音声のみで映像の入っていない入力をエンコーダーに送信すると、音声データのみが含まれたファイルが出力資産に含まれます。 プレーヤーによっては、このような出力ストリームを処理できないことがあります。 その場合、H264Video の **InsertBlackIfNoVideo** 属性設定を使用することで、ビデオ トラックを出力に追加するようエンコーダーに強制できます。 詳細については、[こちらの記事](media-services-advanced-encoding-with-mes.md#no_video)を参照してください。
              
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |H264 レイヤーのコレクション。 |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> ビデオの制限は、[H264 レベル](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)の表で説明されている値に基づきます。  
> 
> 

### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **プロファイル**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** |次のいずれかの **xs:string** 値を指定できます:**Auto**、**Baseline**、**Main**、**High**。 |
| **Level**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |このビデオ レイヤーに使用されるビットレート。kbps 単位で指定します。 |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |このビデオ レイヤーに使用される最大ビットレート。kbps 単位で指定します。 |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: time** |ビデオ バッファーの長さ。 |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |出力ビデオ フレームの幅 (ピクセル単位)。<br/><br/> 現時点では、幅と高さの両方を指定する必要があります。 この幅と高さは、偶数にする必要があります。 |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |出力ビデオ フレームの高さ (ピクセル単位)。<br/><br/> 現時点では、幅と高さの両方を指定する必要があります。 この幅と高さは、偶数にする必要があります。|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |参照フレーム間の B フレームの数。 |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |GOP の参照フレームの数。 |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: string** |次のいずれかの値を指定できます:**Cabac**、**Cavlc**。 |
| **FrameRate**<br/><br/> minOccurs="0" |有理数 |出力ビデオのフレーム レートを決定します。 既定値 "0/1" を使用すると、エンコーダーが、入力ビデオと同じフレーム レートを使用できます。 使用できる値は、共通のビデオ フレーム レートになります。 ただし、すべての有理数が許可されます。 たとえば、1/1 は 1 fps で、有効です。<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29.97 fps) <br/> <br/>**注** マルチビットレート エンコードのカスタム プリセットを作成する場合、プリセットのすべてのレイヤーで同じ FrameRate 値を使用する**必要があります**。|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Azure Media Encoder からコピー |
| **Slices**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |フレームがいくつのスライスに分割されるかを決定します。 既定値を使用することをお勧めします。 |

## <a name="AACAudio"></a> AACAudio
 次の要素とグループのシーケンスが含まれます。  

 AAC の詳細については、[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) に関するページをご覧ください。  

### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **プロファイル**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: string** |次のいずれかの値を指定できます:**AACLC**、**HEAACV1**、**HEAACV2**。 |

### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Condition** |**xs: string** |入力に音声が入っていないとき、無音オーディオ トラックが含まれる資産を生成するようにエンコーダーに強制するには、"InsertSilenceIfNoAudio" 値を指定します。<br/><br/> 既定では、映像のみで音声の入っていない入力をエンコーダーに送信すると、映像データのみが含まれたファイルが出力資産に含まれます。 プレーヤーによっては、このような出力ストリームを処理できないことがあります。 そのような場合、この設定を利用すれば、無音のオーディオ トラックを出力に追加するようにエンコーダーに強制できます。 |

### <a name="groups"></a>グループ

| リファレンス | 説明 |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |各プロファイルに設定できる適切なチャネル数、サンプリング レート、およびビットレートについては、「[AudioGroup](media-services-mes-schema.md#AudioGroup)」の説明を参照してください。 |

## <a name="AudioGroup"></a> AudioGroup
各プロファイルの有効な値の詳細については、以下の「オーディオ コーデックの詳細」の表を参照してください。  

### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Channels**<br/><br/> minOccurs="0" |**xs: int** |エンコードされたオーディオ チャネルの数。 有効なオプションは次の値です:1、2、5、6、8。<br/><br/> 既定値は2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |オーディオ サンプリング レート。Hz で指定します。 |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |オーディオをエンコードするときに使用されるビットレート。kbps で指定します。 |

### <a name="audio-codec-details"></a>オーディオ コーデックの詳細

オーディオ コーデック|詳細  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025:8 &lt;= ビットレート &lt; 16<br/><br/> - 12000:8 &lt;= ビットレート &lt; 16<br/><br/> - 16000:8 &lt;= ビットレート &lt;32<br/><br/>- 22050:24 &lt;= ビットレート &lt; 32<br/><br/> - 24000:24 &lt;= ビットレート &lt; 32<br/><br/> - 32000:32 &lt;= ビットレート &lt;= 192<br/><br/> - 44100:56 &lt;= ビットレート &lt;= 288<br/><br/> - 48000:56 &lt;= ビットレート &lt;= 288<br/><br/> - 88200 :128 &lt;= ビットレート &lt;= 288<br/><br/> - 96000 :128 &lt;= ビットレート &lt;= 288<br/><br/> 2\.<br/><br/> - 11025:16 &lt;= ビットレート &lt; 24<br/><br/> - 12000:16 &lt;= ビットレート &lt; 24<br/><br/> - 16000:16 &lt;= ビットレート &lt; 40<br/><br/> - 22050:32 &lt;= ビットレート &lt; 40<br/><br/> - 24000 :32 &lt;= ビットレート &lt; 40<br/><br/> - 32000:40 &lt;= ビットレート &lt;= 384<br/><br/> - 44100:96 &lt;= ビットレート &lt;= 576<br/><br/> - 48000 :96 &lt;= ビットレート &lt;= 576<br/><br/> - 88200:256 &lt;= ビットレート &lt;= 576<br/><br/> - 96000:256 &lt;= ビットレート &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000:160 &lt;= ビットレート &lt;= 896<br/><br/> - 44100:240 &lt;= ビットレート &lt;= 1024<br/><br/> - 48000:240 &lt;= ビットレート &lt;= 1024<br/><br/> - 88200:640 &lt;= ビットレート &lt;= 1024<br/><br/> - 96000:640 &lt;= ビットレート &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 :224 &lt;= ビットレート &lt;= 1024<br/><br/> - 44100 :384 &lt;= ビットレート &lt;= 1024<br/><br/> - 48000:384 &lt;= ビットレート &lt;= 1024<br/><br/> - 88200:896 &lt;= ビットレート &lt;= 1024<br/><br/> - 96000:896 &lt;= ビットレート &lt;= 1024  
**HEAACV1** |1:<br/><br/> - 22050: ビットレート = 8<br/><br/> - 24000:8 &lt;= ビットレート &lt;= 10<br/><br/> - 32000:12 &lt;= ビットレート &lt;= 64<br/><br/> - 44100:20 &lt;= ビットレート &lt;= 64<br/><br/> - 48000:20 &lt;= ビットレート &lt;= 64<br/><br/> - 88200: ビットレート = 64<br/><br/> 2\.<br/><br/> - 32000:16 &lt;= ビットレート &lt;= 128<br/><br/> - 44100:16 &lt;= ビットレート &lt;= 128<br/><br/> - 48000:16 &lt;= ビットレート &lt;= 128<br/><br/> - 88200 :96 &lt;= ビットレート &lt;= 128<br/><br/> - 96000:96 &lt;= ビットレート &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 :64 &lt;= ビットレート &lt;= 320<br/><br/> - 44100:64 &lt;= ビットレート &lt;= 320<br/><br/> - 48000:64 &lt;= ビットレート &lt;= 320<br/><br/> - 88200 :256 &lt;= ビットレート &lt;= 320<br/><br/> - 96000:256 &lt;= ビットレート &lt;= 320<br/><br/> 8:<br/><br/> - 32000:96 &lt;= ビットレート &lt;= 448<br/><br/> - 44100:96 &lt;= ビットレート &lt;= 448<br/><br/> - 48000:96 &lt;= ビットレート &lt;= 448<br/><br/> - 88200:384 &lt;= ビットレート &lt;= 448<br/><br/> - 96000:384 &lt;= ビットレート &lt;= 448  
**HEAACV2** |2\.<br/><br/> - 22050:8 &lt;= ビットレート &lt;= 10<br/><br/> - 24000:8 &lt;= ビットレート &lt;= 10<br/><br/> - 32000:12 &lt;= ビットレート &lt;= 64<br/><br/> - 44100:20 &lt;= ビットレート &lt;= 64<br/><br/> - 48000:20 &lt;= ビットレート &lt;= 64<br/><br/> - 88200:64 &lt;= ビットレート &lt;= 64  
  
## <a name="Clip"></a> クリップ
### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **StartTime** |**xs:duration** |プレゼンテーションの開始時刻を指定します。 StartTime の値は、入力ビデオの絶対タイムスタンプと一致している必要があります。 たとえば、入力ビデオの最初のフレームのタイムスタンプが 12:00:10.000 の場合、StartTime は 12:00:10.000 以降でなければなりません。 |
| **Duration** |**xs:duration** |プレゼンテーションの期間 (ビデオのオーバーレイの外観など) を指定します。 |

## <a name="Output"></a> 出力
### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **FileName** |**xs:string** |出力ファイルの名前。<br/><br/> 次の表で説明するマクロを使用すると、出力ファイルの名前を作成できます。 例:<br/><br/> **"Outputs": [      {       "FileName": "{Basename} *{Resolution}* {Bitrate}.mp4",       "Format": {         "Type":"MP4Format"       }     }   ]** |

### <a name="macros"></a>マクロ

| マクロ | 説明 |
| --- | --- |
| **{Basename}** |VoD エンコードを実行する場合、{Basename} は、入力資産におけるプライマリ ファイルの AssetFile.Name プロパティに含まれる最初の 32 文字です。<br/><br/> 入力資産がライブ アーカイブの場合、{Basename} は、サーバー マニフェストの trackName 属性から派生します。 TopBitrate を使用してサブクリップ ジョブを送信する場合 (例: "<VideoStream\>TopBitrate</VideoStream\>")、出力ファイルにビデオが含まれていると、{Basename} は、最高ビットレートを持つビデオ レイヤーの trackName に含まれる最初の 32 文字です。<br/><br/> すべての入力ビットレートを使用してサブクリップ ジョブを送信する場合 (例: "<VideoStream\>*</VideoStream\>")、出力ファイルにビデオが含まれていると、{Basename} は、対応するビデオ レイヤーの trackName に含まれる最初の 32 文字です。 |
| **{Codec}** |ビデオの場合は "H264" に、オーディオの場合は "AAC" にマップします。 |
| **{Bitrate}** |出力ファイルにビデオとオーディオが含まれる場合は、ターゲット ビデオ ビットレート。出力ファイルにオーディオのみが含まれる場合は、ターゲット オーディオ ビットレート。 使用される値はビットレートです (kbps 単位)。 |
| **{Channel}** |ファイルにオーディオが含まれる場合は、オーディオ チャネルの数。 |
| **{Width}** |ファイルにビデオが含まれる場合は、出力ファイルのビデオの幅 (ピクセル単位)。 |
| **{Height}** |ファイルにビデオが含まれる場合は、出力ファイルのビデオの高さ (ピクセル単位)。 |
| **{Extension}** |出力ファイルの "Type" プロパティから継承されます。 出力ファイル名の拡張子は、"mp4"、"ts"、"jpg"、"png"、"bmp" のいずれかになります。 |
| **{Index}** |サムネイルについては必須。 一度だけ提供されます。 |

## <a name="Video"></a> ビデオ (複合型はコーデックから継承)
### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Start** |**xs:string** | |
| **Step** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |詳しくは、次のセクションをご覧ください。[PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
**PreserveResolutionAfterRotation** フラグは、パーセントで表される解像度の値 (Width="100%" , Height="100%") と組み合わせて使用することをお勧めします。  

既定では、Media Encoder Standard (MES) プリセットのエンコード解像度設定 (幅、高さ) の対象は、回転が 0 度のビデオです。 たとえば、入力ビデオの解像度が 1280 x 720 で、回転が 0 度の場合、既定のプリセットでは出力の解像度が必ず同じになります。  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

入力ビデオが、0 度以外の回転でキャプチャされている場合 (たとえば、スマートフォンやタブレットが垂直になっている場合)、既定では、エンコード解像度設定 (幅、高さ) は入力ビデオに適用され、回転が補正されます。 たとえば、次の図をご覧ください。 プリセットでは Width = "100%", Height = "100%" が使用されるため、MES は、出力の幅が 1280 ピクセル、高さが 720 ピクセルになるように解釈します。 ビデオを回転すると、画像はそのウィンドウに合わせて縮小され、画面の左右にピラー ボックス領域が表示されます。  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

または、**PreserveResolutionAfterRotation** フラグを "true" に設定できます (既定値は "false")。 プリセットが Width = "100%", Height = "100%" で、PreserveResolutionAfterRotation が "true" に設定されている場合、幅が 1280 ピクセル、高さが 720 ピクセルの入力ビデオを 90 度回転させると、出力の回転は 0 度ですが、幅が 720 ピクセル、高さが 1280 ピクセルになります。 次の図を参照してください。  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (グループ)
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Quality**<br/><br/> minOccurs="0" |**xs:int** |有効な値: 1 (最悪) ～ 100 (最良) |

### <a name="attributes"></a>属性

| 名前 | Type | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (複合型はビデオから継承)
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png レイヤー |

## <a name="JpgImage"></a> JpgImage (複合型はビデオから継承)
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png レイヤー |

## <a name="PngImage"></a> PngImage (複合型はビデオから継承)
### <a name="elements"></a>要素

| 名前 | Type | 説明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png レイヤー |

## <a name="examples"></a>例
このスキーマに基づいて作成された XML プリセットの例については、「[Task Presets for MES (Media Encoder Standard) (MES (Media Encoder Standard) のタスク プリセット)](media-services-mes-presets-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

