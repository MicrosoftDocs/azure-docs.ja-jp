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
ms.openlocfilehash: 5025a68030f5dc3aec07d33af3f98370c8d64b87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338471"
---
# <a name="get-images-from-your-custom-view"></a>カスタム ビューから画像を取得する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Custom Images Search を使用すると、画像でカスタム検索エクスペリエンスを強化することができます。 Web の結果と同様に、カスタム検索では Web サイトのインスタンスのリストでの画像の検索がサポートされています。 Bing の Custom Images Search API または Hosted UI 機能を使用して、画像を取得できます。 ホステッド UI 機能は簡単に使用でき、検索エクスペリエンスを短時間で稼働させる場合に推奨されます。  画像を含むように ホステッド UI を構成する方法について詳しくは、「[ホステッド UI エクスペリエンスを構成する](hosted-ui.md)」をご覧ください。

検索結果の表示をより詳細に制御したい場合は、Bing の Custom Images Search API を使用することができます。 API の呼び出しは、Bing Image Search API の呼び出しと似ているので、API 呼び出しの例については、「[Bing Image Search](../Bing-Image-Search/overview.md)」をご覧ください。 ただしその前に、[Custom Images Search API リファレンス](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)の内容をよく理解してください。 主な違いは、サポートされているクエリ パラメーター (customConfig クエリ パラメーターを含める必要があります) と、要求送信先のエンドポイントです。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->