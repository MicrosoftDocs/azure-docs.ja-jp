---
title: ソブリン クラウド - 音声サービス
titleSuffix: Azure Cognitive Services
description: ソブリン クラウドの使用方法について説明します
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228081"
---
# <a name="speech-services-with-sovereign-clouds"></a>ソブリン クラウドを使用した音声サービス

## <a name="azure-government-united-states"></a>Azure Government (米国)

米国連邦政府、州政府、地方自治体、部族政府およびそれらのパートナーだけが、審査された米国市民によって運営が管理されているこの専用インスタンスにアクセスすることができます。
- リージョン:US Gov バージニア州
- SpeechSDK の SR: *config.FromHost("wss://virginia.stt.speech.azure.us", "\<your-key\>");*
- SpeechSDK の TTS:*config.FromHost("https[]()://virginia.tts.speech.azure.us", "\<your-key\>");*
- 認証トークン: https[]()://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Custom Speech ポータル: https://virginia.cris.azure.us/Home/CustomSpeech
- 利用可能な SKU:S0
- サポートされている機能:
  - 音声テキスト変換
  - Custom Speech (音響適応および言語適応)
  - テキスト読み上げ
  - 音声翻訳
- サポートされていない機能
  - Custom Voice
  - テキスト読み上げ用のニューラル音声
- サポート対象のロケール:次の言語のロケールがサポートされています。
  - アラビア語 (ar-*)
  - 中国語 (zh-*)
  - 英語 (en-*)
  - フランス語 (fr-*)
  - ドイツ語 (de-*)
  - ヒンディー語
  - 韓国語
  - ロシア語
  - スペイン語 (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

中国に設置されている Azure データ センター。中国のユーザーが高速かつ安定したローカル ネットワーク アクセス エクスペリエンスを提供するため、China Mobile、China Telecom、China Unicom およびその他の主要な通信事業者のバックボーン ネットワークに直接アクセスできます。
- リージョン:中国東部 2 (上海)
- SpeechSDK の SR: *config.FromHost("wss://chinaeast2.stt.speech.azure.cn", "\<your-key\>");*
- SpeechSDK の TTS: *config.FromHost("https[]()://chinaeast2.tts.speech.azure.cn", "\<your-key\>");*
- 認証トークン: https[]()://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Custom Speech ポータル: https://speech.azure.cn/CustomSpeech
- 利用可能な SKU:S0
- サポートされている機能:
  - 音声テキスト変換
  - Custom Speech (音響適応および言語適応)
  - テキスト読み上げ
  - 音声翻訳
- サポートされていない機能
  - Custom Voice
  - テキスト読み上げ用のニューラル音声
- サポート対象のロケール:次の言語のロケールがサポートされています。
  - アラビア語 (ar-*)
  - 中国語 (zh-*)
  - 英語 (en-*)
  - フランス語 (fr-*)
  - ドイツ語 (de-*)
  - ヒンディー語
  - 韓国語
  - ロシア語
  - スペイン語 (es-*)

