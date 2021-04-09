---
title: MES プリセットをカスタマイズして高度なエンコードを実行する | Microsoft Docs
description: このトピックでは、Media Encoder Standard のタスク プリセットをカスタマイズして、高度なエンコードを実行する方法を紹介します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 345fd56e5ff069ea222661a4820e75d96a9e2395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014504"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>MES プリセットをカスタマイズして高度なエンコードを実行する

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>概要

このトピックでは、Media Encoder Standard プリセットをカスタマイズする方法を説明します。 .NET を使ってエンコード タスクと、このタスクを実行するジョブを作成する方法については、「[Media Encoder Standard を使用した高度なエンコード](media-services-custom-mes-presets-with-dotnet.md)」を参照してください。 プリセットをカスタマイズしたら、カスタム プリセットをエンコード タスクに指定します。 

XML プリセットを使用する場合、下で示す XML サンプルのように要素の順序を保持するようにしてください (たとえば、KeyFrameInterval は SceneChangeDetection の前に来ます)。

> [!NOTE] 
> Media Services v2 の多くの高度な機能は v3 で現在利用できません。 詳細については、[移行ガイド](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

## <a name="support-for-relative-sizes"></a>相対サイズのサポート

サムネイルを生成するときに、出力の幅と高さを常にピクセル単位で指定しなければならないというわけではありません。 パーセント単位で指定することができます ([1%、…、100%] の範囲)。

### <a name="json-preset"></a>JSON プリセット

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>XML プリセット

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>サムネイルを生成する

このセクションでは、サムネイルを生成するプリセットをカスタマイズする方法を紹介します。 下に定義されているプリセットには、ファイルとサムネイルの生成に必要な情報をエンコードする方法に関する情報が含まれています。 [こちら](media-services-mes-presets-overview.md)のセクションに記載されている MES プリセットを使用し、サムネイルを生成するコードを追加できます。  

> [!NOTE]
> 単一ビットレートのビデオにエンコードする場合、次のプリセットの **SceneChangeDetection** 設定は true にのみ設定できます。 マルチビットレートのビデオにエンコードする場合、 **SceneChangeDetection** を true に設定すると、エンコーダーからエラーが返されます。  
>
>

スキーマの詳細については、 [この](media-services-mes-schema.md) トピックを参照してください。

必ず「 [考慮事項](#considerations) 」セクションを確認してください。

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML プリセット

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>4500</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>4500</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
    <JpgImage Start="{Best}">
      <JpgLayers>
        <JpgLayer>
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>考慮事項

次の考慮事項が適用されます。

* 明示的に Start、Step、Range でタイムスタンプを使用する場合、入力ソースは少なくとも 1 分であると仮定しています。
* Jpg/Png/BmpImage 要素には、Start、Step、Range の各文字列属性があります。これらは次のように解釈できます。

  * 負でない整数である場合は、フレーム番号 (例: "Start": "120")
  * % サフィックス付きで表現されている場合は、ソース期間に相対的 (例: "Start": "15%")
  * HH:MM:SS… 形式で表現されている場合は、タイムスタンプ たとえば、"Start": "00:01:00"

    必要に応じて、表記法を混在させたり、一致させたりすることができます。

    さらに、Start は、コンテンツの最初の "関連する" フレームを決定しようとする特殊なマクロ {Best} もサポートしています。注: (Start が {Best} に設定されている場合、Step と Range は無視されます)
  * 既定値: Start:{Best}
* 画像の形式ごとに出力形式を明示的に指定する必要があります (Jpg/Png/BmpFormat)。 指定されている場合、MES は JpgVideo を JpgFormat などに対応付けます。 OutputFormat には新しい画像コーデック固有のマクロである {Index} が導入されました。このマクロは、画像出力形式を指定する場合に (1 度だけ) 指定する必要があります。

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>動画をトリミングする (クリッピング)
このセクションでは、エンコーダー プリセットを変更し、入力がいわゆる中間ファイルまたはオンデマンド ファイルの入力動画をクリッピングまたはトリミングする方法について説明します。 エンコーダーを使用して、ライブ ストリームからキャプチャまたはアーカイブされた資産をクリッピングまたはトリミングすることもできます。詳細については、[こちらのブログ](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)をご覧ください。

動画をトリミングするには、[こちら](media-services-mes-presets-overview.md)のセクションに記載されている MES プリセットを利用し、**Sources** 要素を変更します (下記を参照)。 StartTime の値は、入力ビデオの絶対タイムスタンプと一致している必要があります。 たとえば、入力ビデオの最初のフレームのタイムスタンプが 12:00:10.000 の場合、StartTime は 12:00:10.000 以降でなければなりません。 次の例では、入力ビデオの開始タイムスタンプは 0 であると想定しています。 **Sources** はプリセットの先頭に配置する必要があります。

### <a name="json-preset"></a><a id="json"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1000,
          "MaxBitrate": 1000,
          "BufferWindow": "00:00:05",
          "Width": 640,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 650,
          "MaxBitrate": 650,
          "BufferWindow": "00:00:05",
          "Width": 640,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 400,
          "MaxBitrate": 400,
          "BufferWindow": "00:00:05",
          "Width": 320,
          "Height": 180,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML プリセット
動画をトリミングするには、 [ここ](media-services-mes-presets-overview.md) に記載されている MES プリセットを利用し、 **Sources** 要素を変更します (下記を参照)。

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>オーバーレイを作成する

Media Encoder Standard では、画像を既存の動画に重ね合わせることができます。 現在サポートされている形式は png、jpg、gif、bmp です。 下に定義されているプリセットはビデオ オーバーレイの基本例です。

プリセット ファイルの定義に加え、資産内のどのファイルがオーバーレイ画像であるか、また画像を重ね合わせるソース動画であるかを Media Services に認識させる必要もあります。 ビデオ ファイルは **プライマリ** ファイルである必要があります。

.NET を使用する場合は、次の 2 つの関数を[こちら](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet)のトピックで定義されている .NET の例に追加します。 **UploadMediaFilesFromFolder** 関数は、フォルダーからファイルをアップロードし (BigBuckBunny.mp4、Image001.png など)、mp4 ファイルを資産内のプライマリ ファイルとして設定します。 **EncodeWithOverlay** 関数は、渡されたカスタム プリセット ファイル (下記のプリセットなど) を使用して、エンコード タスクを作成します。

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> 現時点での制限事項:
>
> オーバーレイの不透明設定には対応していません。
>
> ソース ビデオ ファイルとオーバーレイ画像ファイルを同じ資産に格納する必要があります。また、ビデオ ファイルをその資産でプライマリ ファイルとして設定する必要があります。
>
>

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML プリセット

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>音声が入力されない場合、無音オーディオ トラックを挿入する
既定では、映像のみで音声の入っていない入力をエンコーダーに送信すると、映像データのみが含まれたファイルが出力資産に含まれます。 プレーヤーによっては、このような出力ストリームを処理できないことがあります。 そのような場合、この設定を利用すれば、無音のオーディオ トラックを出力に追加するようにエンコーダーに強制できます。

入力に音声が入っていないとき、無音オーディオ トラックが含まれる資産を生成するようにエンコーダーに強制するには、"InsertSilenceIfNoAudio" 値を指定します。

[こちら](media-services-mes-presets-overview.md)のセクションに記載されている MES プリセットを使用し、次のように変更します。

### <a name="json-preset"></a>JSON プリセット

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>XML プリセット

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>自動インターレース解除を無効にする
コンテンツのインターレース解除を自動的に行う場合は、何もする必要はありません。 自動インターレース解除がオンになっていれば (既定値)、MES がインターレース フレームを自動で検出し、解除処理を行います (インターレースとしてマークされているフレームのみが処理されます)。

自動インターレース解除はオフにすることもできますが、 このオプションは推奨されません。

### <a name="json-preset"></a>JSON プリセット

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>XML プリセット

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>オーディオのみのプリセット
このセクションでは、AAC Audio と AAC Good Quality Audio という 2 つのオーディオのみの MES プリセットを示します。

### <a name="aac-audio"></a>AAC Audio

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>AAC Good Quality Audio

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>複数のビデオ ファイルを連結する

複数のビデオ ファイルを連結するプリセットを生成する方法の例を次に示します。 最も一般的なシナリオは、ヘッダーまたはトレーラーをメイン ビデオに追加する場合です。 主な用途は、編集対象の複数のビデオ ファイルがプロパティ (ビデオの解像度、フレーム レート、オーディオ トラック数など) を共有している場合です。 フレーム レートやオーディオ トラック数が異なるビデオを混在させないように注意してください。

>[!NOTE]
>連結機能は現在、入力のビデオ クリップが解像度やフレーム レートの点で整合性があることを前提とした設計になっています。 

### <a name="requirements-and-considerations"></a>要件と考慮事項

* 入力ビデオのオーディオ トラック数の上限は 1 つです。
* 入力ビデオはすべて同じフレーム レートである必要があります。
* ビデオを別の資産にアップロードし、各資産でプライマリ ファイルとしてビデオを設定する必要があります。
* ビデオの再生時間を把握している必要があります。
* 次のプリセット例では、すべての入力ビデオがゼロのタイムスタンプで始まると想定されています。 ビデオの開始タイムスタンプが異なる場合、通常のライブ アーカイブの場合と同様に、StartTime 値を変更する必要があります。
* JSON プリセットは、入力資産の AssetID 値の明示的な参照を作成します。
* サンプル コードでは、JSON プリセットがローカル ファイル ("C:\supportFiles\preset.json" など) に保存されていると想定されています。 また、2 つの資産が 2 つのビデオ ファイルをアップロードして作成されたこと、結果の AssetID 値を知っていることも想定されています。
* このコード スニペットと JSON プリセットは、2 つのビデオ ファイルを連結する例です。 次の方法で、複数のビデオに拡張することができます。

  1. task.InputAssets.Add() を繰り返し呼び出して複数のビデオを順に追加する。
  2. 同じ順序でエントリを追加して、JSON の対応する "Sources" 要素を編集する。

### <a name="net-code"></a>.NET コード

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>JSON プリセット

連結する資産の ID と、各ビデオの適切な時間セグメントを使用して、カスタム プリセットを更新します。

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Media Encoder Standard を使用してビデオをトリミングする
「 [Media Encoder Standard を使用してビデオをトリミングする](media-services-crop-video.md) 」をご覧ください。

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>入力に映像が含まれていないときにビデオ トラックを挿入する

既定では、音声のみで映像の入っていない入力をエンコーダーに送信すると、音声データのみが含まれたファイルが出力資産に含まれます。 Azure Media Player ( [こちら](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)を参照) など、プレイヤーによっては、このようなストリームを処理できないことがあります。 その場合、この設定を使用することで、モノクロのビデオ トラックを出力に追加するようエンコーダーに強制できます。

> [!NOTE]
> 出力ビデオ トラックを挿入するようエンコーダーに強制すると、出力資産のサイズが増えるため、エンコード タスクのコストが発生します。 テストを実行して、この増加が月額料金に及ぼす影響がごくわずかであることを確認してください。
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>最も低いビットレートでのみビデオを挿入する

["H264 複数ビットレート 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) などの複数ビットレート エンコード プリセットを使用して、ビデオ ファイルと音声のみのファイルが混在する、ストリーミングの入力カタログ全体をエンコードするとします。 このシナリオでは、入力に映像が含まれていないときに、すべての出力ビットレートでビデオを挿入するのではなく、最も低いビットレートでのみモノクロのビデオ トラックを挿入するようエンコーダーに強制できます。 これを実現するには、**InsertBlackIfNoVideoBottomLayerOnly** フラグを使用する必要があります。

[こちら](media-services-mes-presets-overview.md)のセクションに記載されている MES プリセットを使用し、次のように変更します。

#### <a name="json-preset"></a>JSON プリセット

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML プリセット

XML を使用する場合、Condition="InsertBlackIfNoVideoBottomLayerOnly" を **H264Video** 要素の属性として使用し、Condition="InsertSilenceIfNoAudio" を **AACAudio** の属性として使用します。

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>すべての出力ビットレートでビデオを挿入する
["H264 複数ビットレート 720p"](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) などの複数ビットレート エンコード プリセットを使用して、ビデオ ファイルと音声のみのファイルが混在する、ストリーミングの入力カタログ全体をエンコードするとします。 このシナリオでは、入力に映像が含まれていないときに、すべての出力ビットレートでモノクロのビデオ トラックを挿入するようエンコーダーに強制できます。 これにより、ビデオ トラックとオーディオ トラックの数に関して、出力資産がすべて均一になります。 これを実現には、"InsertBlackIfNoVideo" フラグを指定する必要があります。

[こちら](media-services-mes-presets-overview.md)のセクションに記載されている MES プリセットを使用し、次のように変更します。

#### <a name="json-preset"></a>JSON プリセット

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML プリセット

XML を使用する場合、Condition="InsertBlackIfNoVideo" を **H264Video** 要素の属性として使用し、Condition="InsertSilenceIfNoAudio" を **AACAudio** の属性として使用します。

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>ビデオを回転させる
[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) は、0/90/180/270 の角度による回転をサポートしています。 既定の動作は "自動" であり、この場合は受信するビデオ ファイルの回転メタデータの検出と、それに対する補正を試みます。 [こちら](media-services-mes-presets-overview.md)のセクションに定義されているいずれかのプリセットに次の **Sources** 要素を含めます。

### <a name="json-preset"></a>JSON プリセット

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>XML プリセット

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

また、回転の補正がトリガーされた場合にエンコーダーでプリセット内の Width および Height 設定がどのように解釈されるかについて、詳しくは[こちら](media-services-mes-schema.md#PreserveResolutionAfterRotation)のトピックを参照してください。

入力ビデオ内に回転メタデータが存在する場合、それを無視するには、値 "0" を使用してエンコーダーに示します。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>参照
[Media Services Encoding の概要](media-services-encode-asset.md)
