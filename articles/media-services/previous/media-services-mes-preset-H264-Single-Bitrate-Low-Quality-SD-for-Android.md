---
title: H264 Single Bitrate Low Quality SD for Android | Microsoft Docs
description: このトピックでは、**H264 Single Bitrate Low Quality SD for Android** タスク プリセットの概要を説明します。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 67d3446d-e3b8-419f-bbf8-e5149c108531
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 6844d920b8726dcfee38234d539a5314afd2e40b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61129629"
---
# <a name="h264-single-bitrate-low-quality-sd-for-android"></a>H264 Single Bitrate Low Quality SD for Android
`Media Encoder Standard` は、エンコード ジョブの作成時に使用できる一連のエンコード プリセットを定義します。 `preset name`を使用して、メディア ファイルをエンコードする形式を指定することも、 (UTF-8 または UTF-16 エンコードを使用して) 独自の JSON または XML ベースのプリセットを作成することもできます。 その後、カスタム プリセットをエンコーダーに渡します。 この `Media Encoder Standard` エンコーダーでサポートされているすべてのプリセット名の一覧については、[Media Encoder Standard 用のタスク プリセット](media-services-mes-presets-overview.md)に関する記事を参照してください。  
  
 このトピックでは、XML 形式と JSON 形式の `H264 Single Bitrate Low Quality SD for Android` を示します。  
  
 このプリセットにより、ビットレートが 56 kbps、音声が AAC ステレオである単一の MP4 ファイルが生成されます。 このプリセットのプロファイル、ビットレート、サンプリング レートなどの詳細については、下に定義されている XML または JSON を確認してください。 これらのプリセット内の各要素の意味と各要素に有効な値の説明については、「[Media Encoder Standard スキーマ](media-services-mes-schema.md)」を参照してください。  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:05</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>56</Bitrate>  
          <Width>176</Width>  
          <Height>144</Height>  
          <FrameRate>12/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>2</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>56</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>HEAACV2</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>24</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:05",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Baseline",  
          "Level": "2",  
          "Bitrate": 56,  
          "MaxBitrate": 56,  
          "BufferWindow": "00:00:05",  
          "Width": 176,  
          "Height": 144,  
          "ReferenceFrames": 3,  
          "EntropyMode": "Cavlc",  
          "Type": "H264Layer",  
          "FrameRate": "12/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "HEAACV2",  
      "Channels": 2,  
      "SamplingRate": 48000,  
      "Bitrate": 24,  
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
