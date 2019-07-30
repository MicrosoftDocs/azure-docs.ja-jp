---
title: シナリオの可用性 - Speech Services
titlesuffix: Azure Cognitive Services
description: Speech Service のリージョンに関するリファレンスです。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: 7aa2c72a01f1887ea9680f8d5706b825a49039a1
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561285"
---
# <a name="scenario-availability"></a>シナリオの利用可否

Speech service SDK には、さまざまなプログラミング言語と環境にわたる多数のシナリオがあります。  まだ、すべてのシナリオがすべてのプログラミング言語またはすべての環境で利用できるわけではありません。  各シナリオの利用可否は以下のとおりです。

- **音声認識 (SR)、フレーズ リスト、意図、翻訳、オンプレミス コンテナー**
  - 矢印のリンク <img src="media/index/link.jpg" height="15" width="15"></img> が[こちら](https://aka.ms/csspeech)のクイック スタートの表に表示されているすべてのプログラミング言語/環境。
- **テキスト読み上げ (TTS)**
  - C++/Windows および Linux
  - C#/Windows および UWP および Unity
  - TTS REST API は他のすべての状況で使用できます。
- **ウェイク ワード (Keyword Spotter/KWS)**
  - C++/Windows および Linux
  - C#/Windows および Linux
  - Python/Windows および Linux
  - Java/Windows および Linux および Android (Speech Devices SDK)
  - ウェイク ワード (Keyword Spotter/KWS) の機能は任意の種類のマイクでも動作する可能性がありますが、公式の KWS サポートは、現時点では Azure Kinect DK ハードウェアまたは Speech Devices SDK 内のマイク アレイに限定されています。
- **音声優先仮想アシスタント**
  - C++/Windows、Linux、および macOS
  - C#/Windows
  - Java/Windows、Linux、macOS、および Android (Speech Devices SDK)
- **会話の文字起こし**
  - C++/Windows および Linux
  - C# (Framework および .NET Core)/Windows および UWP および Linux
  - Java/Windows および Linux および Android (Speech Devices SDK)
- **コール センターの文字起こし**
  - REST API、任意の状況で使用できます
- **コーデック圧縮オーディオ入力**
  - C++/Linux
  - C#/Linux
  - Java/Linux および Android
