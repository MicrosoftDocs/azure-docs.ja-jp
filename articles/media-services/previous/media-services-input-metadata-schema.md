---
title: Azure Media Services 入力メタデータのスキーマ | Microsoft Docs
description: この記事では、Azure Media Services 入力メタデータのスキーマの概要を説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017411"
---
# <a name="input-metadata"></a>入力メタデータ

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

エンコード ジョブは、エンコーディング タスクを実行する入力資産に関連付けられています。  タスクが完了すると、出力資産が生成されます。  出力資産には、ビデオ、オーディオ、サムネイル、マニフェストなどが含まれます。この出力資産には、入力資産に関するメタデータが格納されたファイルも含まれます。 メタデータ XML ファイルの名前は、&lt;asset_id&gt;_metadata.xml という形式になっています (たとえば、41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml)。ここで、&lt;asset_id&gt; は入力資産の AssetId の値です。  

Media Services では、メタデータを生成するために入力資産を事前にスキャンできません。 入力メタデータは、入力資産がジョブで処理されるときに、成果物としてのみ生成されます。 そのため、この成果物は、出力資産に書き込まれます。 入力資産と出力資産のメタデータの生成には、異なるツールが使用されます。 したがって、入力メタデータには、出力メタデータとは若干異なるスキーマがあります。

メタデータ ファイルを確認したい場合は、**SAS** ロケータ―を作成してファイルをローカル コンピューターにダウンロードできます。 SAS ロケータ―を作成してファイルをダウンロードする方法の例については、[Media Services .NET SDK Extensions の使用](media-services-dotnet-get-started.md)に関するページをご覧ください。  

この記事では、入力メタデータ (&lt;asset_id&gt;_metadata.xml) が基づく XML スキーマの要素と種類について説明します。  出力資産に関するメタデータを含むファイルについては、「[Output Metadata (出力メタデータ)](media-services-output-metadata-schema.md)」を参照してください。  

[スキーマ コード](media-services-input-metadata-schema.md#code)と [XML の例](media-services-input-metadata-schema.md#xml)は、この記事の最後で紹介します。  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles 要素 (ルート要素)
エンコード ジョブの [AssetFile 要素](media-services-input-metadata-schema.md#AssetFile)のコレクションが含まれます。  

XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

| 名前 | 説明 |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs="1" maxOccurs="unbounded" |1 つの子要素。 詳細については、「[AssetFile 要素](media-services-input-metadata-schema.md#AssetFile)」を参照してください。 |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile 要素
 資産ファイルについて記述する属性と要素が含まれます。  

 XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **名前**<br /><br /> 必須 |**xs:string** |資産ファイルの名前。 |
| **[サイズ]**<br /><br /> 必須 |**xs:long** |資産ファイルのサイズ (バイト単位)。 |
| **Duration**<br /><br /> 必須 |**xs:duration** |コンテンツの再生時間。 例:Duration="PT25M37.757S"。 |
| **NumberOfStreams**<br /><br /> 必須 |**xs:int** |資産ファイル内のストリーム数。 |
| **FormatNames**<br /><br /> 必須 |**xs: string** |形式の名前。 |
| **FormatVerboseNames**<br /><br /> 必須 |**xs: string** |形式の詳細な名前。 |
| **StartTime** |**xs:duration** |コンテンツの開始時刻。 例:StartTime="PT2.669S"。 |
| **OverallBitRate** |**xs: int** |資産ファイルの平均ビットレート (kbps 単位)。 |

> [!NOTE]
> 次の 4 つの子要素は順番に表示されます。  
> 
> 

### <a name="child-elements"></a>子要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Programs**<br /><br /> minOccurs="0" | |資産ファイルが MPEG-TS 形式の場合のすべての [Programs 要素](media-services-input-metadata-schema.md#Programs)のコレクション。 |
| **VideoTracks**<br /><br /> minOccurs="0" | |各物理資産ファイルには、適切なコンテナー形式にインターリーブされる 0 個以上のビデオ トラックを含めることができます。 この要素には、資産ファイルの一部であるすべての [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) のコレクションが含まれます。 |
| **AudioTracks**<br /><br /> minOccurs="0" | |各物理資産ファイルには、適切なコンテナー形式にインターリーブされる 0 個以上のオーディオ トラックを含めることができます。 この要素には、資産ファイルの一部であるすべての [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) のコレクションが含まれます。 |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |キー\値文字列として表される資産ファイルのメタデータ。 次に例を示します。<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Id**<br /><br /> 必須 |**xs:int** |このオーディオまたはビデオ トラックの 0 から始まるインデックス。<br /><br /> 必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。 |
| **Codec** |**xs:string** |ビデオ トラック コーデック文字列。 |
| **CodecLongName** |**xs: string** |オーディオまたはビデオ トラック コーデックの長い名前。 |
| **TimeBase**<br /><br /> 必須 |**xs:string** |時間ベース。 例:TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |フレームの数。 |
| **StartTime** |**xs: duration** |トラックの開始時刻。 例:StartTime="PT2.669S" |
| **Duration** |**xs:duration** |トラックの時間。 例:Duration="PTSampleFormat M37.757S"。 |

> [!NOTE]
> 次の 2 つの子要素は順番に表示されます。  
> 
> 

### <a name="child-elements"></a>子要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |プレゼンテーション情報が含まれます (特定のオーディオ トラックが視覚障碍者向けかどうか、など)。 |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |さまざまな情報を保持する際に使用できる汎用的なキー/値文字列。 例: key=”language”、value=”eng”。 |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (TrackType から継承)
 **AudioTrackType** は、[TrackType](media-services-input-metadata-schema.md#TrackType) から継承されるグローバルな複合型です。  

 型は、資産ファイル内の特定のオーディオ トラックを表します。  

 XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |サンプル形式。 |
| **ChannelLayout** |**xs: string** |チャネル レイアウト。 |
| **Channels**<br /><br /> 必須 |**xs:int** |オーディオ チャネルの数 (0 以上)。 |
| **SamplingRate**<br /><br /> 必須 |**xs:int** |オーディオ サンプリング レート (サンプル数/秒または Hz)。 |
| **Bitrate** |**xs:int** |資産ファイルから計算された、平均オーディオ ビット レート (ビット/秒)。 基本ストリーム ペイロードのみがカウントされます。この値には、パッケージのオーバーヘッドは含まれません。 |
| **BitsPerSample** |**xs:int** |wFormatTag 形式タイプのサンプルあたりのビット数。 |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (TrackType から継承)
**VideoTrackType** は、[TrackType](media-services-input-metadata-schema.md#TrackType) から継承されるグローバルな複合型です。  

型は、資産ファイル内の特定のビデオ トラックを表します。  

XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **FourCC**<br /><br /> 必須 |**xs:string** |ビデオ コーデックの FourCC コード。 |
| **プロファイル** |**xs: string** |ビデオ トラックのプロファイル。 |
| **Level** |**xs: string** |ビデオ トラックのレベル。 |
| **PixelFormat** |**xs: string** |ビデオ トラックのピクセル形式。 |
| **Width**<br /><br /> 必須 |**xs:int** |エンコードされたビデオの幅 (ピクセル単位)。 |
| **Height**<br /><br /> 必須 |**xs:int** |エンコードされたビデオの高さ (ピクセル単位)。 |
| **DisplayAspectRatioNumerator**<br /><br /> 必須 |**xs: double** |ビデオ ディスプレイの縦横比の分子。 |
| **DisplayAspectRatioDenominator**<br /><br /> 必須 |**xs:double** |ビデオ ディスプレイの縦横比の分母。 |
| **DisplayAspectRatioDenominator**<br /><br /> 必須 |**xs: double** |ビデオ サンプルの縦横比の分子。 |
| **SampleAspectRatioNumerator** |**xs: double** |ビデオ サンプルの縦横比の分子。 |
| **SampleAspectRatioNumerator** |**xs:double** |ビデオ サンプルの縦横比の分母。 |
| **FrameRate**<br /><br /> 必須 |**xs:decimal** |.3f 形式の測定されたビデオ フレーム レート。 |
| **Bitrate** |**xs:int** |資産ファイルから計算された、平均ビデオ ビット レート (キロビット/秒)。 基本ストリーム ペイロードのみがカウントされます。パッケージのオーバーヘッドは含まれません。 |
| **MaxGOPBitrate** |**xs: int** |このビデオ トラックの最大 GOP 平均ビットレート (キロビット数/秒)。 |
| **HasBFrames** |**xs:int** |B フレームのビデオ トラック数。 |

## <a name="metadatatype"></a><a name="MetadataType"></a> MetadataType
**MetadataType** は、資産ファイルのメタデータをキー/値文字列として記述する、グローバルな複合型です。 例: key=”language”、value=”eng”。  

XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **key**<br /><br /> 必須 |**xs:string** |キー/値ペアのキー。 |
| **value**<br /><br /> 必須 |**xs:string** |キー/値ペアの値。 |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
**ProgramType** は、プログラムを記述するグローバルな複合型です。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **ProgramId**<br /><br /> 必須 |**xs:int** |プログラム ID |
| **NumberOfPrograms**<br /><br /> 必須 |**xs:int** |プログラム数。 |
| **PmtPid**<br /><br /> 必須 |**xs:int** |プログラム マップ テーブル (PMT) には、プログラム情報が含まれています。  詳細については、「[PMT](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT)」を参照してください。 |
| **PcrPid**<br /><br /> 必須 |**xs: int** |デコーダーが使用。 詳細については、「[PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR)」を参照してください |
| **StartPTS** |**xs: long** |プレゼンテーション開始のタイム スタンプ。 |
| **EndPTS** |**xs: long** |プレゼンテーション終了のタイム スタンプ。 |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** は、ストリームを記述するグローバルな複合型です。  

XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **[Default]**<br /><br /> 必須 |**xs: int** |この属性を 1 に設定して、これが既定のプレゼンテーションであることを示します。 |
| **Dub**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、これが吹き替えのプレゼンテーションであることを示します。 |
| **Original**<br /><br /> 必須 |**xs: int** |この属性を 1 に設定して、これがオリジナルのプレゼンテーションであることを示します。 |
| **解説**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、このトラックにコメントが含まれることを示します。 |
| **Lyrics**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、このトラックに歌詞が含まれることを示します。 |
| **Karaoke**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、これがカラオケ トラック (伴奏のみ、ボーカルなし) であることを示します。 |
| **Forced**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、これが強制的に実行されるプレゼンテーションであることを示します。 |
| **HearingImpaired**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、このトラックが聴覚障碍者を対象にしていることを示します。 |
| **VisualImpaired**<br /><br /> 必須 |**xs:int** |この属性を 1 に設定して、このトラックが視覚障碍者を対象にしていることを示します。 |
| **CleanEffects**<br /><br /> 必須 |**xs: int** |この属性を 1 に設定して、このトラックにクリーン効果があることを示します。 |
| **AttachedPic**<br /><br /> 必須 |**xs: int** |この属性を 1 に設定して、このトラックに画像が含まれることを示します。 |

## <a name="programs-element"></a><a name="Programs"></a> Programs 要素
複数の **Program** 要素が保持されているラッパー要素。  

### <a name="child-elements"></a>子要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |MPEG-TS 形式の資産ファイルについては、資産ファイルのプログラムに関する情報が含まれます。 |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks 要素
 複数の **VideoTrack** 要素が保持されているラッパー要素。  

 XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="child-elements"></a>子要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (TrackType から継承)](media-services-input-metadata-schema.md#VideoTrackType) |資産ファイルのビデオ トラックに関する情報が含まれます。 |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks 要素
 複数の **AudioTrack** 要素が保持されているラッパー要素。  

 XML の例については、この記事の最後の「[XML の例](media-services-input-metadata-schema.md#xml)」を参照してください。  

### <a name="elements"></a>要素
| 名前 | 型 | 説明 |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (TrackType から継承)](media-services-input-metadata-schema.md#AudioTrackType) |資産ファイルのオーディオ トラックに関する情報が含まれます。 |

## <a name="schema-code"></a><a name="code"></a> スキーマ コード
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> XML の例
入力メタデータ ファイルの例を次に示します。  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

