---
title: コンテナーのリポジトリとイメージ
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Cognitive Service のすべてのオファリングにおけるコンテナーのレジストリ、リポジトリ、イメージ名を表す 2 つの表を示します。
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 600c01fc2a273141b06996369231c2389c5d6d57
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775870"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なすべてのコンテナー イメージの一覧です。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>パブリックの "ゲートなし" (コンテナー レジストリ: <ph id="ph1">`mcr.microsoft.com`</ph>)

Microsoft Container Registry では、Cognitive Services 向けに一般公開されている "ゲートなし" コンテナーをすべてホストしています。

| Service | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | 感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>パブリックの "ゲート付き" プレビュー (コンテナー レジストリ: `containerpreview.azurecr.io`)

Container プレビューのレジストリでは、Cognitive Services 向けに一般公開されている "ゲート付き" コンテナーをすべてホストしています。 これらのコンテナーを使用するには、正式なアクセス依頼が必要です。

| Service | コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|--|--|--|
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | テキスト認識 | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
