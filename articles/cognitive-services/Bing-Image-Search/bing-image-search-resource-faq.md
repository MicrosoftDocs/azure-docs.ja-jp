---
title: よく寄せられる質問 (FAQ) - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API に関連する概念、コード、シナリオについてよく寄せられる質問の回答を見つけることができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: e8502b1d01e0f3fbf5d42e924511cc978690bce4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342194"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Bing Image Search API についてよく寄せられる質問 (FAQ)

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Azure 上の Microsoft Cognitive Services の Bing Image Search API に関連する概念、コード、シナリオについてよく寄せられる質問に対する回答を示します。

## <a name="response-headers-in-javascript"></a>JavaScript 内の応答ヘッダー

次のヘッダーは、Bing Image Search API からの応答に含まれる可能性があります。

| 属性           | 説明   |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |Bing がユーザーに割り当てた一意の ID |
| `BingAPIs-Market`   |要求を満たすために使用された市場 |
| `BingAPIs-TraceId`  |この要求に対する Bing API サーバー上のログ エントリ (サポート用) |

クライアント ID を保持し、後続の要求で返すことが特に重要です。 これを行うときに、検索は検索結果の順位付けに過去のコンテキストを使用し、一貫性のあるユーザー エクスペリエンスも提供します。

ただし、JavaScript から Bing Image Search API を呼び出すときに、ブラウザーの組み込みのセキュリティ機能 (CORS) によっては、これらのヘッダーの値にアクセスできないことがあります。

ヘッダーにアクセスするために、CORS プロキシを介して Bing Image Search API 要求を行うことができます。 そのようなプロキシからの応答には、応答ヘッダーをフィルター処理して JavaScript で使用できるようにする `Access-Control-Expose-Headers` ヘッダーが含まれています。

CORS プロキシをインストールして[チュートリアル アプリ](tutorial-bing-image-search-single-page-app.md)が省略可能なクライアント ヘッダーにアクセスできるようにするのは簡単です。 まず、[Node.js をインストールします](https://nodejs.org/en/download/) (まだインストールしていない場合)。 その後、コマンド プロンプトで次のコマンドを入力します。

```console
npm install -g cors-proxy-server
```

次に、HTML ファイル内の Bing Image Search API エンドポイントを次のように変更します。\
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

最後に、次のコマンドを使用して、CORS プロキシを開始します。

```console
cors-proxy-server
```

チュートリアル アプリを使用している間はコマンド ウィンドウを開いたままにしておいてください。ウィンドウを閉じるとプロキシが停止します。 検索結果の下の展開可能な HTTP ヘッダー セクションに、`X-MSEdge-ClientID` ヘッダー (など) が表示され、各要求で同じであることを確認できます。

## <a name="response-headers-in-production"></a>実稼働環境での応答ヘッダー

前の回答で説明されている CORS プロキシ アプローチは、開発、テスト、学習に適しています。

一方、実稼働環境では、Bing Web Search API を使用する Web ページと同じドメイン上のサーバー側スクリプトをホストする必要があります。 このスクリプトは、実際には Web ページの JavaScript からの要求時に API 呼び出しを実行し、ヘッダーを含むすべての結果をクライアントに戻します。 2 つのリソース (ページとスクリプト) が配信元を共有するので、CORS は関与せず、特殊なヘッダーに Web ページ上の JavaScript からアクセスできます。

API キーはサーバー側スクリプトでのみ必要なので、このアプローチでは API キーもパブリックへの露出から保護されます。 スクリプトでは、別の方法 (HTTP 参照元など) を使用して、要求が承認されているかどうかを確認できます。

## <a name="next-steps"></a>次のステップ

不足している機能について質問がある場合は、 [ユーザーの声 Web サイト](https://cognitive.uservoice.com/forums/555907-bing-search)で、要求またはそれに対する投票をご検討ください。

## <a name="see-also"></a>関連項目

 [Stack Overflow: Cognitive Services](https://stackoverflow.com/questions/tagged/bing-api)