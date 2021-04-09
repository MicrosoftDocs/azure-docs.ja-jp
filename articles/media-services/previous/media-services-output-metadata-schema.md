---
title: Azure Media Services 出力メタデータのスキーマ | Microsoft Docs
description: この記事では、Azure Media Services 出力メタデータのスキーマの概要を説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 519aa158d60eae97eb4c1b792bcecfc8a6c066e0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008290"
---
# <a name="output-metadata"></a>出力メタデータ

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>概要
エンコード ジョブは、エンコーディング タスクを実行する入力資産に関連付けられています。 たとえば、MP4 ファイルを H.264 MP4 アダプティブ ビットレート セットにエンコードし、サムネイルを作成し、オーバーレイを作成します。 タスクが完了すると、出力資産が生成されます。  出力資産には、ビデオ、オーディオ、サムネイルなどが含まれます。出力資産には、出力資産に関するメタデータが格納されたファイルも含まれます。 メタデータ XML ファイルの名前は、&lt;source_file_name&gt;_manifest.xml という形式になっています (たとえば、BigBuckBunny_manifest.xml)。  

Media Services では、メタデータを生成するために入力資産を事前にスキャンできません。 入力メタデータは、入力資産がジョブで処理されるときに、成果物としてのみ生成されます。 そのため、この成果物は、出力資産に書き込まれます。 入力資産と出力資産のメタデータの生成には、異なるツールが使用されます。 したがって、入力メタデータには、出力メタデータとは若干異なるスキーマがあります。

メタデータ ファイルを確認したい場合は、**SAS** ロケータ―を作成してファイルをローカル コンピューターにダウンロードできます。  

この記事では、出力メタデータ (&lt;source_file_name&gt;_manifest.xml) が基づく XML スキーマの要素と種類について説明します。 入力資産に関するメタデータを含むファイルについては、入力メタデータに関するページを参照してください。  

完全なスキーマ コードと XML の例は、この記事の最後で紹介します。  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a> AssetFiles ルート要素
エンコード ジョブの AssetFile エントリのコレクション。  

### <a name="child-elements"></a>子要素
| 名前 | 説明 |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs="0" maxOccurs="1" |AssetFiles コレクションの一部である AssetFile 要素。 |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile 要素
XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 種類 | 説明 |
| --- | --- | --- |
| **名前**<br/><br/> 必須 |**xs:string** |メディア資産ファイルの名前。 |
| **[サイズ]**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:long** |資産ファイルのサイズ (バイト単位)。 |
| **Duration**<br/><br/> 必須 |**xs:duration** |コンテンツの再生時間。 |

### <a name="child-elements"></a>子要素
| 名前 | 説明 |
| --- | --- |
| **Sources** |この AssetFile を生成するために処理された入力/ソース メディア ファイルのコレクション。 詳細については、「Source 要素」を参照してください。 |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のビデオ トラックを含めることができます。 詳細については、「VideoTracks 要素」を参照してください。 |
| **AudioTracks**<br/><br/> minOccurs="0" maxOccurs="1" |各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のオーディオ トラックを含めることができます。 これは、そのオーディオ トラックすべてのコレクションです。 詳細については、「AudioTracks 要素」を参照してください。 |

## <a name="sources-element"></a><a name="Sources"></a> Sources 要素
この AssetFile を生成するために処理された入力/ソース メディア ファイルのコレクション。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="child-elements"></a>子要素
| 名前 | 説明 |
| --- | --- |
| **ソース**<br/><br/> minOccurs="1" maxOccurs="unbounded" |この資産を生成するときに使用される入力/ソース ファイル。 詳細については、「Source 要素」を参照してください。 |

## <a name="source-element"></a><a name="Source"></a> Source 要素
この資産を生成するときに使用される入力/ソース ファイル。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 種類 | 説明 |
| --- | --- | --- |
| **名前**<br/><br/> 必須 |**xs:string** |入力ソース ファイルの名前。 |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks 要素
各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のビデオ トラックを含めることができます。 **VideoTracks** 要素は、すべてのビデオ トラックのコレクションを表します。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="child-elements"></a>子要素
| 名前 | 説明 |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |親 AssetFile 内の特定のビデオ トラック。 詳細については、「VideoTrack 要素」を参照してください。 |

## <a name="videotrack-element"></a><a name="VideoTrack"></a> VideoTrack 要素
親 AssetFile 内の特定のビデオ トラック。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 種類 | 説明 |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |このビデオ トラックの 0 から始まるインデックス。**注:** この **Id** は、必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。 |
| **FourCC**<br/><br/> 必須 |**xs:string** |ビデオ コーデックの FourCC コード。 |
| **プロファイル** |**xs:string** |H264 プロファイル (H264 コーデックのみに適用されます)。 |
| **Level** |**xs:string** |H264 レベル (H264 コーデックのみに適用されます)。 |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |エンコードされたビデオの幅 (ピクセル単位)。 |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |エンコードされたビデオの高さ (ピクセル単位)。 |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:double** |ビデオ ディスプレイの縦横比の分子。 |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:double** |ビデオ ディスプレイの縦横比の分母。 |
| **FrameRate**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:decimal** |.3f 形式の測定されたビデオ フレーム レート。 |
| **TargetFramerate**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:decimal** |.3f 形式のプリセットされたターゲット ビデオ フレーム レート。 |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |AssetFile から計算された、平均ビデオ ビット レート (キロビット/秒)。 基本ストリーム ペイロードのみがカウントされます。パッケージのオーバーヘッドは含まれません。 |
| **TargetBitrate**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |エンコード プリセットを使用して要求された、このビデオ トラックのターゲット平均ビットレート (キロビット/秒)。 |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |このビデオ トラックの最大 GOP 平均ビットレート (キロビット数/秒)。 |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks 要素
各物理 AssetFile には、適切なコンテナー形式にインターリーブされる 0 個以上のオーディオ トラックを含めることができます。 **AudioTracks** 要素は、すべてのオーディオ トラックのコレクションを表します。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="child-elements"></a>子要素
| 名前 | 説明 |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |親 AssetFile 内の特定のオーディオ トラック。 詳細については、「AudioTrack 要素」を参照してください。 |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a> AudioTrack 要素
親 AssetFile 内の特定のオーディオ トラック。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 種類 | 説明 |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |このオーディオ トラックの 0 から始まるインデックス。**注:** これは、必ずしも MP4 ファイルで使用されている TrackID であるとは限りません。 |
| **Codec** |**xs:string** |オーディオ トラック コーデック文字列。 |
| **EncoderVersion** |**xs:string** |省略可能なエンコーダー バージョン文字列。EAC3 の場合は必須。 |
| **Channels**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |オーディオ チャネルの数。 |
| **SamplingRate**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |オーディオ サンプリング レート (サンプル数/秒または Hz)。 |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |AssetFile から計算された、平均オーディオ ビット レート (ビット/秒)。 基本ストリーム ペイロードのみがカウントされます。パッケージのオーバーヘッドは含まれません。 |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> 必須 |**xs:int** |wFormatTag 形式タイプのサンプルあたりのビット数。 |

### <a name="child-elements"></a>子要素
| 名前 | 説明 |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |ラウドネス測定結果パラメーター。 詳細については、「LoudnessMeteringResultParameters 要素」を参照してください。 |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a> LoudnessMeteringResultParameters 要素
ラウドネス測定結果パラメーター。  

XML の例については、「[XML の例](#xml)」を参照してください。  

### <a name="attributes"></a>属性
| 名前 | 種類 | 説明 |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |**Dolby** Professional Loudness Metering 開発キット バージョン。 |
| **DialogNormalization**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> 必須 |**xs:int** |DPLM によって生成された DialogNormalization。LoudnessMetering が設定されているときに必要 |
| **IntegratedLoudness**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> 必須 |**xs:float** |Integrated Loudness |
| **IntegratedLoudnessUnit**<br/><br/> 必須 |**xs:string** |Integrated Loudness ユニット。 |
| **IntegratedLoudnessGatingMethod**<br/><br/> 必須 |**xs:string** |ゲ―ティング ID |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |プログラムにおける音声コンテンツの割合。 |
| **SamplePeak**<br/><br/> 必須 |**xs:float** |リセット後または最後にクリアされた後の、チャネルあたりのピーク サンプル絶対値。  単位は dBFS です。 |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> 必須 |**xs:anySimpleType** |サンプル ピーク単位。 |
| **TruePeak**<br/><br/> 必須 |**xs:float** |リセット後または最後にクリアされた後の、ITU-R BS.1770-2 のチャネルあたりの最大トゥルー ピーク値。 単位は dBTP です。 |
| **TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> 必須 |**xs:anySimpleType** |トゥルー ピーク単位。 |

## <a name="schema-code"></a>スキーマ コード
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            elementFormDefault="qualified">  
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
              <xs:element name="Sources">  
                <xs:annotation>  
                  <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:attribute name="Name" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>input source file name</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
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
                        <xs:attribute name="FourCC" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>video codec FourCC code</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Profile" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Level" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                        <xs:attribute name="Framerate" use="required">  
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
                        <xs:attribute name="TargetFramerate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:decimal">  
                              <xs:minInclusive value="0"/>  
                              <xs:fractionDigits value="3"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="TargetBitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                 <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:sequence>  
                          <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                            <xs:annotation>  
                              <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                            </xs:annotation>  
                            <xs:complexType>  
                              <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="DialogNormalization" use="required">  
                                <xs:annotation>  
                                  <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:int">  
                                    <xs:minInclusive value="-31"/>  
                                    <xs:maxInclusive value="-1"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudness" use="required">  
                                <xs:annotation>  
                                  <xs:documentation>Integrated loudness</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="-70"/>  
                                    <xs:maxInclusive value="10"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Gating identifier</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                <xs:annotation>  
                                  <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="0"/>  
                                    <xs:maxInclusive value="100"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                              </xs:attribute>  
                              <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                              </xs:attribute>  
                            </xs:complexType>  
                          </xs:element>  
                        </xs:sequence>  
                        <xs:attribute name="Id" use="required">  
                          <xs:annotation>  
                            <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Codec" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>audio track codec string</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="EncoderVersion" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="BitsPerSample" use="required">  
                          <xs:annotation>  
                            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
            <xs:attribute name="Duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:duration"/>  
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

出力メタデータ ファイルの XML の例を次に示します。  

```xml
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
  <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
      <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
