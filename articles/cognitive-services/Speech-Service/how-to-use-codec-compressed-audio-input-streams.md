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
ms.openlocfilehash: 4585538e552e73f8f7a4b7b105153a9d26eeb4c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934100"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK でコーデック圧縮オーディオを使用する

Speech サービス SDK の **圧縮オーディオ入力ストリーム** API では、`PullStream` または `PushStream` を使用して、圧縮オーディオを Speech Services にストリーミングする方法を提供します。

現在、圧縮入力オーディオのストリーミングは、Windows (UWP アプリケーションはサポートされていません) と Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7/8、CentOS 7/8) では C#、C++、Java、および Python でサポートされています。 Android の Java でもサポートされています。
* RHEL 8 と CentOS 8 では、Speech SDK バージョン 1.10.0 以降が必要です
* Windows では、Speech SDK バージョン 1.11.0 以降が必要です。

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="prerequisites"></a>前提条件

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
> [音声を認識する方法](quickstarts/speech-to-text-from-microphone.md)
