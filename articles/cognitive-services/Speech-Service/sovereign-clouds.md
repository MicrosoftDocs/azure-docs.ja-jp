---
title: ソブリン クラウド - 音声サービス
titleSuffix: Azure Cognitive Services
description: ソブリン クラウドの使用方法について説明します
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98061754"
---
# <a name="speech-services-in-sovereign-clouds"></a>ソブリン クラウドの Speech Services

## <a name="azure-government-united-states"></a>Azure Government (米国)

米国政府機関およびそのパートナーのみが利用できます。 Azure Government の詳細については、[こちら](../../azure-government/documentation-government-welcome.md)と[こちら](../../azure-government/compare-azure-government-global-azure.md)を参照してください。

- **Azure portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **リージョン:**
  - US Gov アリゾナ
  - US Gov バージニア州
- **利用可能な価格レベル:**
  - Free (F0) および Standard (S0)。 詳細については、[こちら](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)を参照してください
- **サポートされている機能:**
  - 音声テキスト変換
    - Custom Speech (音響モデル (AM) と言語モデル (LM) 適応)
      - [Speech Studio](https://speech.azure.us/)
  - テキスト読み上げ
    - 標準音声
    - ニューラル音声
  - 音声翻訳
- **サポートされていない機能:**
  - Custom Voice
- **サポートされている言語:**
  - [こちら](language-support.md)のサポートされている言語のリストを参照してください

### <a name="endpoint-information"></a>エンドポイント情報

このセクションには、[Speech SDK](speech-sdk.md)、[Speech to text REST API](rest-speech-to-text.md)、および [Text-to-speech REST API](rest-text-to-speech.md) の使用に関する Speech Services エンドポイント情報が含まれています。

#### <a name="speech-services-rest-api"></a>Speech Services REST API

Azure Government の Speech Services REST API エンドポイントの形式は次のとおりです。

|  REST API の種類/操作 | エンドポイントの形式 |
|--|--|
| アクセス トークン | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Text to Speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

次の表に示す、ご利用のサブスクリプションのリージョンと一致する識別子で `<REGION_IDENTIFIER>` を置き換えてください。

|                     | リージョン識別子 |
|--|--|
| **US Gov アリゾナ**  | `usgovarizona` |
| **US Gov バージニア州** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Speech SDK

ソブリン クラウドの Speech SDK の場合、`SpeechConfig` クラスの "ホストから" のインスタンス化や、[Speech CLI](spx-overview.md) の `--host` オプションを利用する必要があります。 ("エンドポイントから" のインスタンス化と `--endpoint` Speech CLI オプションを使用することもできます)。

`SpeechConfig` クラスは次のようにインスタンス化する必要があります。
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Speech CLI は、次のように使用する必要があります (`--host` オプションに注目してください)。
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey` をSpeech リソース キーに置き換えます。 `usGovHost` を、このテーブルの必要なサービス内容とサブスクリプションのリージョンと一致する式に置き換えます。

|  リージョン / サービス内容 | ホスト式 |
|--|--|
| **US Gov アリゾナ** | |
| 音声テキスト変換 | `wss://usgovarizona.stt.speech.azure.us` |
| 音声合成 | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov バージニア州** | |
| 音声テキスト変換 | `wss://usgovvirginia.stt.speech.azure.us` |
| 音声合成 | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure 中国

中国で事業を展開している組織で利用できます。 Azure China の詳細については、[こちら](/azure/china/overview-operations)を参照してください。 


- **Azure portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **リージョン:**
  - 中国東部 2
- **利用可能な価格レベル:**
  - Free (F0) および Standard (S0)。 詳細については、[こちら](https://www.azure.cn/pricing/details/cognitive-services/index.html)を参照してください
- **サポートされている機能:**
  - 音声テキスト変換
    - Custom Speech (音響モデル (AM) と言語モデル (LM) 適応)
      - [Speech Studio](https://speech.azure.cn/)
  - テキスト読み上げ
    - 標準音声
    - ニューラル音声
  - 音声翻訳
- **サポートされていない機能:**
  - Custom Voice
- **サポートされている言語:**
  - [こちら](language-support.md)のサポートされている言語のリストを参照してください

### <a name="endpoint-information"></a>エンドポイント情報

このセクションには、[Speech SDK](speech-sdk.md)、[Speech to text REST API](rest-speech-to-text.md)、および [Text-to-speech REST API](rest-text-to-speech.md) の使用に関する Speech Services エンドポイント情報が含まれています。

#### <a name="speech-services-rest-api"></a>Speech Services REST API

Azure China の Speech Services REST API エンドポイントの形式は次のとおりです。

|  REST API の種類/操作 | エンドポイントの形式 |
|--|--|
| アクセス トークン | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Speech to Text REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Speech to Text REST API for short audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Text to Speech REST API](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

次の表に示す、ご利用のサブスクリプションのリージョンと一致する識別子で `<REGION_IDENTIFIER>` を置き換えてください。

|                     | リージョン識別子 |
|--|--|
| **中国東部 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Speech SDK

ソブリン クラウドの Speech SDK の場合、`SpeechConfig` クラスの "ホストから" のインスタンス化や、[Speech CLI](spx-overview.md) の `--host` オプションを利用する必要があります。 ("エンドポイントから" のインスタンス化と `--endpoint` Speech CLI オプションを使用することもできます)。

`SpeechConfig` クラスは次のようにインスタンス化する必要があります。
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Speech CLI は、次のように使用する必要があります (`--host` オプションに注目してください)。
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey` をSpeech リソース キーに置き換えます。 `azCnHost` を、このテーブルの必要なサービス内容とサブスクリプションのリージョンと一致する式に置き換えます。

|  リージョン / サービス内容 | ホスト式 |
|--|--|
| **中国東部 2** | |
| 音声テキスト変換 | `wss://chinaeast2.stt.speech.azure.cn` |
| 音声合成 | `https://chinaeast2.tts.speech.azure.cn` |