---
title: REST API を使用して LUIS モデルを変更する方法
titleSuffix: Azure Cognitive Services
description: この記事では、発話の例を追加してモデルを変更し、アプリをトレーニングします。
services: cognitive-services
manager: nitinme
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 7346037ad804da4f68a2bc6512494c461d7fc5bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96436183"
---
# <a name="how-to-change-the-luis-model-with-rest-apis"></a>REST API で LUIS モデルを変更する方法

この記事では、Pizza アプリに発話の例を追加してアプリをトレーニングします。 発話の例は、意図にマッピングされる会話形式のユーザー テキストです。 意図に対する発話の例を与えるには、ユーザーによって入力されるどのようなテキストが、どの意図に属しているかを LUIS に学習させます。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-model-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-model-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-model-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-model-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-model-rest-python.md)]
::: zone-end
