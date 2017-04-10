---
title: "Media Encoder Standard スキーマ | Microsoft Docs"
description: "このトピックでは、Media Encoder Standard スキーマの概要を示します。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: a526610f5b09ce73a9c192ec45ae8aafab001401
ms.lasthandoff: 01/13/2017


---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard スキーマ
このトピックでは、[Media Encoder Standard プリセット](media-services-mes-presets-overview.md)のベースとなっている XML スキーマの要素と型をいくつか取り上げます。 ここでは要素とその有効な値について説明します。 完全なスキーマは今後追加される予定です。  

## <a name="a-namepreseta-preset-root-element"></a><a name="Preset"></a> プリセット (ルート要素)
エンコード プリセットを定義します。  

### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Encoding** |[Encoding](media-services-mes-schema.md#Encoding) |ルート要素。入力ソースがエンコードされることを示します。 |
| **Outputs** |[Outputs](media-services-mes-schema.md#Output) |目的の出力ファイルのコレクション。 |

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **バージョン**<br/><br/> 必須 |**xs:decimal** |プリセット バージョン。 xs:fractionDigits value="1" と xs:minInclusive value="1" という制限が適用されます。たとえば、**version="1.0"** という制限です。 |

## <a name="a-nameencodinga-encoding"></a><a name="Encoding"></a> エンコード
次の要素のシーケンスが含まれます。  

### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |ビデオの H.264 エンコードの設定。 |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |オーディオの AAC エンコードの設定。 |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Bmp イメージの設定。 |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png イメージの設定。 |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg イメージの設定。 |

## <a name="a-nameh264videoa-h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |現在サポートされているのは&1; パス エンコードだけです。 |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |IDR フレーム間 (既定の) 間隔を決定します。 |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs:boolean** |true に設定すると、エンコーダーはビデオ内でシーン変更の検出を試行し、IDR フレームを挿入します。 |
| **Complexity**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |エンコード速度とビデオ品質の間のトレードオフを制御します。 **Speed**、**Balanced**、**Quality** のいずれかの値を指定できます<br/><br/> 既定値: **Balanced** |
| **SyncMode**<br/><br/> minOccurs="0" | |機能は今後のリリースで公開されます。 |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |出力ビデオ レイヤーのコレクション。 |

## <a name="a-nameh264layersa-h264layers"></a><a name="H264Layers"></a> H264Layers
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |H264 レイヤーのコレクション。 |

## <a name="a-nameh264layera-h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> ビデオの制限は、[H264 レベル](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels)の表で説明されている値に基づきます。  
> 
> 

### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **プロファイル**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs:string** |**Auto**、**Baseline**、**Main**、**High** のいずれかの **xs:string** 値を指定できます。 |
| **Level**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs:string** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |このビデオ レイヤーに使用されるビットレート。kbps 単位で指定します。 |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs:int** |このビデオ レイヤーに使用される最大ビットレート。kbps 単位で指定します。 |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs:time** |ビデオ バッファーの長さ。 |
| **Width**<br/><br/> minOccurs="0" |**xs:int** |出力ビデオ フレームの幅 (ピクセル単位)。<br/><br/> 現時点では、幅と高さの両方を指定する必要があります。 この幅と高さは、偶数にする必要があります。 |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |出力ビデオ フレームの高さ (ピクセル単位)。<br/><br/> 現時点では、幅と高さの両方を指定する必要があります。 この幅と高さは、偶数にする必要があります。|
| **BFrames**<br/><br/> minOccurs="0" |**xs:int** |参照フレーム間の B フレームの数。 |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |GOP の参照フレームの数。 |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs:string** |**Cabac** または **Cavlc** のいずれかの値を指定できます。 |
| **FrameRate**<br/><br/> minOccurs="0" |有理数 |出力ビデオのフレーム レートを決定します。 既定値 "0/1" を使用すると、エンコーダーが、入力ビデオと同じフレーム レートを使用できます。 以下に示すように、使用できる値は、共通のビデオ フレーム レートになります。 ただし、すべての有理数が許可されます。 たとえば、1/1 は 1 fps で、有効です。<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29.97 fps) |
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs:boolean** |Azure Media Encoder からコピー |
| **Slices**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |フレームがいくつのスライスに分割されるかを決定します。 既定値を使用することをお勧めします。 |

## <a name="a-nameaacaudioa-aacaudio"></a><a name="AACAudio"></a> AACAudio
 次の要素とグループのシーケンスが含まれます。  

 AAC の詳細については、[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) に関するページをご覧ください。  

### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **プロファイル**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs:string** |**AACLC**、**HEAACV1**、**HEAACV2** のいずれかの値を指定できます。 |

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** |入力に音声が入っていないとき、無音オーディオ トラックが含まれる資産を生成するようにエンコーダーに強制するには、"InsertSilenceIfNoAudio" 値を指定します。<br/><br/> 既定では、映像のみで音声の入っていないエンコーダーに入力を送信すると、映像データのみが含まれるファイルが出力資産に含まれます。 プレーヤーによっては、このような出力ストリームを処理できないことがあります。 そのような場合、この設定を利用すれば、無音のオーディオ トラックを出力に追加するようにエンコーダーに強制できます。 |

### <a name="groups"></a>グループ
| リファレンス | 説明 |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |各プロファイルに設定できる適切なチャネル数、サンプリング レート、およびビットレートについては、「[AudioGroup](media-services-mes-schema.md#AudioGroup)」の説明を参照してください。 |

## <a name="a-nameaudiogroupa-audiogroup"></a><a name="AudioGroup"></a> AudioGroup
各プロファイルの有効な値の詳細については、以下の「オーディオ コーデックの詳細」の表を参照してください。  

### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Channels**<br/><br/> minOccurs="0" |**xs:int** |エンコードされたオーディオ チャネルの数。 有効なオプションは 1、2、5、6、8 です。<br/><br/> 既定値: 2。 |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs:int** |オーディオ サンプリング レート。Hz で指定します。 |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |オーディオをエンコードするときに使用されるビットレート。kbps で指定します。 |

### <a name="audio-codec-details"></a>オーディオ コーデックの詳細
オーディオ コーデック|詳細  
-----------------|---  
**AACLC**|1:<br/><br/> - 11025 : 8 &lt;= ビットレート &lt; 16<br/><br/> - 12000 : 8 &lt;= ビットレート &lt; 16<br/><br/> - 16000 : 8 &lt;= ビットレート &lt;32<br/><br/>- 22050 : 24 &lt;= ビットレート &lt; 32<br/><br/> - 24000 : 24 &lt;= ビットレート &lt; 32<br/><br/> - 32000 : 32 &lt;= ビットレート &lt;= 192<br/><br/> - 44100 : 56 &lt;= ビットレート &lt;= 288<br/><br/> - 48000 : 56 &lt;= ビットレート &lt;= 288<br/><br/> - 88200 : 128 &lt;= ビットレート &lt;= 288<br/><br/> - 96000 : 128 &lt;= ビットレート &lt;= 288<br/><br/> 2.<br/><br/> - 11025 : 16 &lt;= ビットレート &lt; 24<br/><br/> - 12000 : 16 &lt;= ビットレート &lt; 24<br/><br/> - 16000 : 16 &lt;= ビットレート &lt; 40<br/><br/> - 22050 : 32 &lt;= ビットレート &lt; 40<br/><br/> - 24000 : 32 &lt;= ビットレート &lt; 40<br/><br/> - 32000 :  40 &lt;= ビットレート &lt;= 384<br/><br/> - 44100 : 96 &lt;= ビットレート &lt;= 576<br/><br/> - 48000 : 96 &lt;= ビットレート &lt;= 576<br/><br/> - 88200 : 256 &lt;= ビットレート &lt;= 576<br/><br/> - 96000 : 256 &lt;= ビットレート &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000 : 160 &lt;= ビットレート &lt;= 896<br/><br/> - 44100 : 240 &lt;= ビットレート &lt;= 1024<br/><br/> - 48000 : 240 &lt;= ビットレート &lt;= 1024<br/><br/> - 88200 : 640 &lt;= ビットレート &lt;= 1024<br/><br/> - 96000 : 640 &lt;= ビットレート &lt;= 1024<br/><br/> 8:<br/><br/> - 32000 : 224 &lt;= ビットレート &lt;= 1024<br/><br/> - 44100 : 384 &lt;= ビットレート &lt;= 1024<br/><br/> - 48000 : 384 &lt;= ビットレート &lt;= 1024<br/><br/> - 88200 : 896 &lt;= ビットレート &lt;= 1024<br/><br/> - 96000 : 896 &lt;= ビットレート &lt;= 1024  
**HEAACV1**|1:<br/><br/> - 22050 : ビットレート = 8<br/><br/> - 24000 : 8 &lt;= ビットレート &lt;= 10<br/><br/> - 32000 : 12 &lt;= ビットレート &lt;= 64<br/><br/> - 44100 : 20 &lt;= ビットレート &lt;= 64<br/><br/> - 48000 : 20 &lt;= ビットレート &lt;= 64<br/><br/> - 88200 : ビットレート = 64<br/><br/> 2.<br/><br/> - 32000 : 16 &lt;= ビットレート &lt;= 128<br/><br/> - 44100 : 16 &lt;= ビットレート &lt;= 128<br/><br/> - 48000 : 16 &lt;= ビットレート &lt;= 128<br/><br/> - 88200 : 96 &lt;= ビットレート &lt;= 128<br/><br/> - 96000 : 96 &lt;= ビットレート &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000 : 64 &lt;= ビットレート &lt;= 320<br/><br/> - 44100 : 64 &lt;= ビットレート &lt;= 320<br/><br/> - 48000 : 64 &lt;= ビットレート &lt;= 320<br/><br/> - 88200 : 256 &lt;= ビットレート &lt;= 320<br/><br/> - 96000 : 256 &lt;= ビットレート &lt;= 320<br/><br/> 8:<br/><br/> - 32000 : 96 &lt;= ビットレート &lt;= 448<br/><br/> - 44100 : 96 &lt;= ビットレート &lt;= 448<br/><br/> - 48000 : 96 &lt;= ビットレート &lt;= 448<br/><br/> - 88200 : 384 &lt;= ビットレート &lt;= 448<br/><br/> - 96000 : 384 &lt;= ビットレート &lt;= 448  
**HEAACV2**|2.<br/><br/> - 22050 : 8 &lt;= ビットレート &lt;= 10<br/><br/> - 24000 : 8 &lt;= ビットレート &lt;= 10<br/><br/> - 32000 : 12 &lt;= ビットレート &lt;= 64<br/><br/> - 44100 : 20 &lt;= ビットレート &lt;= 64<br/><br/> - 48000 : 20 &lt;= ビットレート &lt;= 64<br/><br/> - 88200 : 64 &lt;= ビットレート &lt;= 64  
  

## <a name="a-nameclipa-clip"></a><a name="Clip"></a> クリップ
### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **StartTime** |**xs:duration** |プレゼンテーションの開始時刻を指定します。 StartTime の値は、入力ビデオの絶対タイムスタンプと一致している必要があります。 たとえば、入力ビデオの最初のフレームのタイムスタンプが 12:00:10.000 の場合、StartTime は 12:00:10.000 以降でなければなりません。 |
| **Duration** |**xs:duration** |プレゼンテーションの期間 (ビデオのオーバーレイの外観など) を指定します。 |

## <a name="a-nameoutputa-output"></a><a name="Output"></a> 出力
### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **FileName** |**xs:string** |出力ファイルの名前。<br/><br/> 次の表で説明するマクロを使用すると、出力ファイルの名前を作成できます。 次に例を示します。<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>マクロ
| マクロ | 説明 |
| --- | --- |
| **{Basename}** |VoD エンコードを実行する場合、{Basename} は、入力資産におけるプライマリ ファイルの AssetFile.Name プロパティに含まれる最初の 32 文字です。<br/><br/> 入力資産がライブ アーカイブの場合、{Basename} は、サーバー マニフェストの trackName 属性から派生します。 TopBitrate を使用してサブクリップ ジョブを送信する場合 (例: "< VideoStream\>TopBitrate </VideoStream\>")、出力ファイルにビデオが含まれていると、{Basename} は、最高ビットレートを持つビデオ レイヤーの trackName に含まれる最初の 32 文字です。<br/><br/> すべての入力ビットレートを使用してサブクリップ ジョブを送信する場合 (例: "<VideoStream\>*</VideoStream\>")、出力ファイルにビデオが含まれていると、{Basename} は、対応するビデオ レイヤーの trackName に含まれる最初の 32 文字です。 |
| **{Codec}** |ビデオの場合は "H264" に、オーディオの場合は "AAC" にマップします。 |
| **{Bitrate}** |出力ファイルにビデオとオーディオが含まれる場合は、ターゲット ビデオ ビットレート。出力ファイルにオーディオのみが含まれる場合は、ターゲット オーディオ ビットレート。 使用される値はビットレートです (kbps 単位)。 |
| **{Channel}** |ファイルにオーディオが含まれる場合は、オーディオ チャネルの数。 |
| **{Width}** |ファイルにビデオが含まれる場合は、出力ファイルのビデオの幅 (ピクセル単位)。 |
| **{Height}** |ファイルにビデオが含まれる場合は、出力ファイルのビデオの高さ (ピクセル単位)。 |
| **{Extension}** |出力ファイルの "Type" プロパティから継承されます。 出力ファイル名の拡張子は、"mp4"、"ts"、"jpg"、"png"、"bmp" のいずれかになります。 |
| **{Index}** |サムネイルについては必須。 一度だけ提供されます。 |

## <a name="a-namevideoa-video-complex-type-inherits-from-codec"></a><a name="Video"></a> ビデオ (複合型はコーデックから継承)
### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Start** |**xs:string** | |
| **Step** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |詳細については、「[PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation)」セクションを参照してください |

### <a name="a-namepreserveresolutionafterrotationa-preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
PreserveResolutionAfterRotation フラグは、パーセントで表される解像度の値 (Width = "100%" , Height = "100%") と組み合わせて使用することをお勧めします。  

既定では、Media Encoder Standard (MES) プリセットのエンコード解像度設定 (幅、高さ) の対象は、回転が 0 度のビデオです。 たとえば、入力ビデオの解像度が 1280 x 720 で、回転が 0 度の場合、既定のプリセットでは出力の解像度が必ず同じになります。 次の図を参照してください。  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

ただし、入力ビデオが、0 度以外の回転でキャプチャされている場合 (たとえば、 スマートフォンやタブレットが垂直になっている場合)、既定では、エンコード解像度設定 (幅、高さ) は入力ビデオに適用され、回転が補正されます。 たとえば、次の図を参照してください。 プリセットでは Width = "100%", Height = "100%" が使用されるため、MES は、出力の幅が 1280 ピクセル、高さが 720 ピクセルになるように解釈します。 ビデオを回転すると、画像はそのウィンドウに合わせて縮小され、画面の左右にピラー ボックス領域が表示されます。  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

上記のように動作させたくない場合は、PreserveResolutionAfterRotation フラグを "true" に設定します (既定値は"false")。 プリセットが Width = "100%", Height = "100%" で、PreserveResolutionAfterRotation が "true" に設定されている場合、幅が 1280 ピクセル、高さが 720 ピクセルの入力ビデオを 90 度回転させると、出力の回転は 0 度ですが、幅が 720 ピクセル、高さが 1280 ピクセルになります。 次の図を参照してください。  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="a-nameformatgroupa-formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (グループ)
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="a-namebmplayera-bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namepnglayera-pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namejpglayera-jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Quality**<br/><br/> minOccurs="0" |**xs:int** |有効な値: 1(worst)-100(best) |

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="a-namepnglayersa-pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="a-namebmplayersa-bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="a-namejpglayersa-jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="a-namebmpimagea-bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (複合型はビデオから継承)
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png レイヤー |

## <a name="a-namejpgimagea-jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (複合型はビデオから継承)
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png レイヤー |

## <a name="a-namepngimagea-pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (複合型はビデオから継承)
### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png レイヤー |

## <a name="examples"></a>例
このスキーマに基づいて作成された XML プリセットの例については、「[Task Presets for MES (Media Encoder Standard) (MES (Media Encoder Standard) のタスク プリセット)](media-services-mes-presets-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


