---
title: コンテナーのリポジトリとイメージ
services: cognitive-services
author: aahill
manager: nitinme
description: Cognitive Service のすべてのオファリングにおけるコンテナーのレジストリ、リポジトリ、イメージ名を表す 2 つの表を示します。
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: ac8e49c85147576e489b16a3a421c75d60ad4aee
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86104413"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なコンテナー イメージの一覧です。 利用できるコンテナー イメージ名とその利用できるタグの完全一覧が必要であれば、[Cognitive Services コンテナー イメージ タグ](../container-image-tags.md)に関するページを参照してください。 

#### <a name="generally-available"></a>一般公開 

Microsoft コンテナー レジストリ (MCR) からは、Cognitive Services 用に一般提供されているすべてのコンテナーが配信されています。 コンテナーは、[Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) から直接入手することもできます。

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

詳細については、[LUIS コンテナーの実行およびインストール方法](../../LUIS/luis-container-howto.md)に関するページを参照してください。

#### <a name="text-analytics"></a>[Text Analytics](#tab/text-analytics)

| Text Analytics コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|
| 感情分析 v3 (英語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| 感情分析 v3 (スペイン語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| 感情分析 v3 (フランス語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| 感情分析 v3 (イタリア語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| 感情分析 v3 (ドイツ語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| 感情分析 v3 (簡体字中国語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| 感情分析 v3 (繁体字中国語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| 感情分析 v3 (日本語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| 感情分析 v3 (ポルトガル語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| 感情分析 v3 (オランダ語) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

詳細については、[Text Analytics コンテナーの実行およびインストール方法](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md)に関するページを参照してください。

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>パブリックの "ゲートなし" プレビュー (コンテナー レジストリ: `mcr.microsoft.com`)

次のプレビュー コンテナーは、一般公開されています。 Microsoft コンテナー レジストリ (MCR) では、Cognitive Services 向けに一般提供されているゲートなしコンテナーをすべて配信しています。 コンテナーは、[Docker Hub](https://hub.docker.com/_/microsoft-azure-cognitive-services) から直接入手することもできます。

| サービス | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>パブリックの "ゲート付き" プレビュー (コンテナー レジストリ: `containerpreview.azurecr.io`)

次のゲート付きプレビュー コンテナーはコンテナー プレビュー レジストリでホストされており、アクセスするにはアプリケーションが必要です。 詳細については、次のコンテナーに関する記事を参照してください。

| サービス | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Read | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | カスタム音声変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | カスタム テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Text Analytics for Health](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Text Analytics for Health | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
