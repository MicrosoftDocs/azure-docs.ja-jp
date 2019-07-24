---
title: Project URL Preview とは
titlesuffix: Azure Cognitive Services
description: Project URL Preview の概要。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 7022c3b2d2f3618d55b0a70d2690abf1497ec6a6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865337"
---
# <a name="what-is-project-url-preview"></a>Project URL Preview とは
URL Preview エンドポイントは、URL クエリ パラメーターを受け取り、ターゲット リソース名、簡単な説明、プレビューで表示する画像へのリンクを含む JSON 応答を返します。 応答には、[isFamilyFriendly](url-preview-reference.md#query-parameters) フラグも含まれています。このフラグは、成人向けコンテンツ、海賊版コンテンツ、その他の違法なコンテンツが URL に含まれているかどうかを示します。 

URL のプレビュー結果を取得するには、GET 要求を送信し、有効なトークンを持つ *Ocp-Apim-Subscription-Key* ヘッダーを含めます。  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
応答は次のようになります。 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>シナリオ 

URL Preview API は、Web リソースの簡単な説明をサポートしています。 開発者はこれを使用して、高度なプレビュー エクスペリエンスを構築します。  ユーザーは、Web ページ、ニュース、ブログ、フォーラムなどを共有したり、ブックマークしたりできます。この API は、コンテンツ モデレーションにも使用できます。    

アプリケーションは URL Preview を使用して、プレビューする URL に割り当てられたクエリと共に Web 要求をエンドポイントに送信します。  JSON 応答に含まれるプレビュー情報には、名前、リソースの説明、*familyFriendly* フラグ、典型的な画像と完全なリソースにオンラインでアクセスするためのリンクがあります。 

## <a name="terms-of-use"></a>使用条件
Project URL Preview からのデータのみを使用して、ソーシャル メディアやチャットボットなどのサービスでエンド ユーザーによって開始される URL 共有に、ソース サイトにハイパーリンクされたプレビュー スニペットとサムネイル画像を表示します。 Project URL Preview から受信したデータは、いずれもコピー、保存、キャッシュしないでください。 Web サイトやコンテンツの所有者からプレビューを無効にするよう要請があった場合は、その意向を尊重してください。

お客様およびお客様の代理を務める第三者のいずれも、URL Preview API から取得したデータを Microsoft 以外のサービスまたは機能のテスト、開発、トレーニング、頒布、または提供を目的として利用、保持、保存、キャッシュ、共有、または配布することは認められません。 

## <a name="throttling-requests"></a>スロットル リクエスト

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>次の手順
- [C# のクイック スタート](csharp.md)
- [Java のクイック スタート](java-quickstart.md)
- [JavaScript のクイック スタート](javascript.md)
- [Node のクイック スタート](node-quickstart.md)
- [Python のクイック スタート](python-quickstart.md)
