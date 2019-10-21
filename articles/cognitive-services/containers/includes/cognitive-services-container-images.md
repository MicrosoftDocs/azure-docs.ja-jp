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
ms.openlocfilehash: 181f4acd86dfacb15592ded6f2df3287e3dc13bf
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130158"
---
### <a name="container-repositories-and-images"></a>コンテナーのリポジトリとイメージ

次の表は、Azure Cognitive Services によって提供される使用可能なすべてのコンテナー イメージの一覧です。

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>パブリックで "ゲートされていない" (コンテナー レジストリ: `mcr.microsoft.com`)

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
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | 読み取り | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | 音声テキスト変換 | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | テキスト読み上げ | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
