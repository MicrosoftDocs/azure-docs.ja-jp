---
title: カスタム ビューから画像を取得する - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Bing Custom Search を使用して、Web のカスタム ビューから画像を取得する方法の概要です。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 42679635aab8df7174eb06928631e2b677d0a20b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55855916"
---
# <a name="get-images-from-your-custom-view"></a>カスタム ビューから画像を取得する

Bing Custom Images Search を使用すると、画像でカスタム検索エクスペリエンスを強化することができます。 Web の結果と同様に、カスタム検索では Web サイトのインスタンスのリストでの画像の検索がサポートされています。 Bing の Custom Images Search API または Hosted UI 機能を使用して、画像を取得できます。 ホステッド UI 機能は簡単に使用でき、検索エクスペリエンスを短時間で稼働させる場合に推奨されます。  画像を含むように ホステッド UI を構成する方法について詳しくは、「[ホステッド UI エクスペリエンスを構成する](hosted-ui.md)」をご覧ください。

検索結果の表示をより詳細に制御したい場合は、Bing の Custom Images Search API を使用することができます。 API の呼び出しは、Bing Image Search API の呼び出しと似ているので、API 呼び出しの例については、「[Bing Image Search](../Bing-Image-Search/overview.md)」をご覧ください。 ただしその前に、[Custom Images Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)の内容をよく理解してください。 主な違いは、サポートされているクエリ パラメーター (customConfig クエリ パラメーターを含める必要があります) と、要求送信先のエンドポイントです。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
