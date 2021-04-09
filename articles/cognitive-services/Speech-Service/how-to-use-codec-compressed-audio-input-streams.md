---
title: Speech SDK を使用してコーデック圧縮オーディオをストリーミングする - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用して圧縮オーディオを Speech Service にストリーミングする方法について学習します。 Linux の場合は C++、C#、Java で、Android の場合は Java で、iOS の場合は Objective-C で使用できます。
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: 1e08203076de2073e39c5b5f5eb40b66c88490d7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103417708"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK でコーデック圧縮オーディオを使用する

Speech サービス SDK の **圧縮オーディオ入力ストリーム** API では、`PullStream` または `PushStream` を使用して、圧縮オーディオを Speech Services にストリーミングする方法を提供します。

プラットフォーム | 言語 | サポートされている GStreamer のバージョン
| :--- | ---: | :---:
Windows (UWP を除く)  | C++、C#、Java、Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++、C#、Java、Python | [サポートされている Linux ディストリビューションとターゲット アーキテクチャ](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>圧縮されたオーディオ入力に必要な Speech SDK バージョン
* RHEL 8 と CentOS 8 では、Speech SDK バージョン 1.10.0 以降が必要です
* Windows では、Speech SDK バージョン 1.11.0 以降が必要です。
* Windows と Android 上の最新の gstreamer には、Speech SDK バージョン 1.16.0 以降。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>圧縮されたオーディオを処理するために必要な GStreamer

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>コーデック圧縮オーディオ入力を使用するコード例

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [音声を認識する方法](./get-started-speech-to-text.md)