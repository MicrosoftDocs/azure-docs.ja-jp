---
title: クライアント ライブラリを使用した Bing Speech Recognition API の開始 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Microsoft Cognitive Services の Bing Speech クライアント ライブラリを使用して、音声をテキストに変換するアプリケーションを開発します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966855"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Bing Speech Service クライアント ライブラリを使ってみる

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

REST API を介して HTTP 要求を直接行う以外に、Bing Speech Service には開発者向けに Speech クライアント ライブラリがさまざまな言語で用意されています。 Speech クライアント ライブラリには、次の機能が備わっています。

- リアルタイムでの中間結果、長いオーディオ ストリーム (最大 10 分)、連続的認識など、音声認識に関するより高度な機能をサポートします。
- シンプルで慣用的な API を普段使用している言語で提供します。
- 低レベルの通信の詳細を非表示にします。

現時点では、次の Bing Speech クライアント ライブラリを使用できます。

- [C# デスクトップ ライブラリ](GetStartedCSharpDesktop.md)
- [C# サービス ライブラリ](GetStartedCSharpServiceLibrary.md)
- [JavaScript ライブラリ](GetStartedJSWebsockets.md)
- [Android 用 Java ライブラリ](GetStartedJavaAndroid.md)
- [iOS 用 Objective-C ライブラリ](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>その他のリソース

- [サンプル](../samples.md) ページには、Speech クライアント ライブラリを使用するためのすべてのサンプルが用意されています。
- まだサポートされていないクライアント ライブラリが必要な場合は、独自の SDK を作成できます。 プラットフォームに [Speech WebSocket プロトコル](../API-Reference-REST/websocketprotocol.md)を実装し、お好みの言語を使用してください。

## <a name="license"></a>ライセンス

すべての Cognitive Services SDK およびサンプルは、MIT ライセンスがあります。 詳しくは、[ライセンス](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)に関するページをご覧ください。
