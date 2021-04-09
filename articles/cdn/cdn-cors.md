---
title: CORS を利用した Azure CDN の使用 | Microsoft Docs
description: クロス オリジン リソース共有 (CORS) を利用して Azure Content Delivery Network (CDN) を使用する方法について説明します。
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f7edf790e526329dd285d03a31137a26220e52ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018649"
---
# <a name="using-azure-cdn-with-cors"></a>CORS を利用した Azure CDN の使用
## <a name="what-is-cors"></a>CORS とは
CORS (クロス オリジン リソース共有) は、あるドメインで実行されている Web アプリケーションが別のドメイン内にあるリソースにアクセスできるようにする HTTP 機能です。 クロスサイト スクリプティング攻撃の可能性を低減させるために、すべての最新の Web ブラウザーには [同一オリジン ポリシー](https://www.w3.org/Security/wiki/Same_Origin_Policy)と呼ばれるセキュリティ制限が実装されています。  これにより、Web ページは他のドメイン内の API を呼び出すことができません。  CORS を使用すれば、あるオリジン (オリジン ドメイン) から他のオリジン内の API を安全に呼び出すことができます。

## <a name="how-it-works"></a>しくみ
CORS 要求には、"*簡単な要求*" と "*複雑な要求*" の 2 種類があります。

### <a name="for-simple-requests"></a>単純な要求の場合:

1. ブラウザーが、追加 **Origin** HTTP 要求ヘッダーを含む CORS 要求を送信します。 このヘッダーの値は、親ページを提供したオリジンで、"*プロトコル"、"* *ドメイン*"、および "*ポート*" の組み合わせとして定義されます。  https\://www.contoso.com からのページが fabrikam.com オリジンのユーザーのデータにアクセスしようとすると、次の要求ヘッダーが fabrikam.com に送信されます。

   `Origin: https://www.contoso.com`

2. サーバーからは次のいずれかの応答が返される場合があります。

   * 許可されるオリジン サイトを示す、応答の **Access-Control-Allow-Origin** ヘッダー。 次に例を示します。

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * 403 などの HTTP エラー コード (Origin ヘッダーの確認後、サーバーによってクロス オリジン要求が許可されなかった場合)

   * すべてのオリジンを許可するワイルドカードが含まれる **Access-Control-Allow-Origin** ヘッダー。

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>複雑な要求:

複雑な要求は CORS 要求です。この要求で、ブラウザーは実際の CORS 要求を送信する前に、"*プレフライト要求*" (準備プローブ) を送信します。 プレフライト要求は、サーバーのアクセス許可に対して、元の CORS 要求が処理を実行できるかどうかと、その要求が、同じ URL への `OPTIONS` 要求かどうかを尋ねます。

> [!TIP]
> CORS フローおよびよくある落とし穴の詳細については、[CORS for REST API ガイド](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/)を参照してください。
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>ワイルドカードまたは単一のオリジンのシナリオ
Azure CDN の CORS は、 **Access-Control-Allow-Origin** ヘッダーがワイルドカード (*) または単一のオリジンに設定されている場合、そのままの構成で自動的に動作します。  最初の応答が CDN によってキャッシュされ、後続の要求で同じヘッダーが使用されます。

CORS でオリジンが設定される前に CDN に対し要求が行われている場合、エンドポイント コンテンツのコンテンツを消去して、 **Access-Control-Allow-Origin** ヘッダーと共にそのコンテンツを再度読み込む必要があります。

## <a name="multiple-origin-scenarios"></a>複数のオリジンのシナリオ
複数のオリジンを CORS で許可する必要がある場合は、若干複雑になります。 最初の CORS オリジンの **Access-Control-Allow-Origin** ヘッダーが CDN にキャッシュされるときに問題が発生します。  異なる CORS オリジンが後続の要求を行った場合、CDN によってキャッシュされた **Access-Control-Allow-Origin** ヘッダーが提供されても、オリジンが一致しません。  これを修正するにはいくつかの方法があります。

### <a name="azure-cdn-standard-profiles"></a>Azure CDN Standard プロファイル
Microsoft の Azure CDN Standard では、[Standard ルール エンジン](cdn-standard-rules-engine-reference.md)でルールを作成して、要求の **Origin** ヘッダーを確認することができます。 オリジンが有効である場合、ルールによって、目的の値が **Access-Control-Allow-Origin** ヘッダーに設定されます。 この場合は、ファイルの配信元サーバーからの **Access-Control-Allow-Origin** ヘッダーは無視され、CDN のルール エンジンが、許可される CORS オリジンを完全に管理します。

![Standard ルール エンジンを使用したルールの例](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> 追加のアクションをルールに追加して、**Access-Control-Allow-Methods** などの追加の応答ヘッダーを変更できます。
> 

**Akamai の Azure CDN Standard** では、ワイルドカード オリジンを使用せずに複数のオリジンを許可するメカニズムは、[クエリ文字列のキャッシュ](cdn-query-string.md)を使用する方法だけです。 CDN エンドポイントのクエリ文字列設定を有効にしたうえで、許可される各ドメインからの要求について一意のクエリ文字列を使用してください。 これにより、CDN で一意のクエリ文字列ごとに個別のオブジェクトがキャッシュされるようになります。 この手法は最適ではありませんが、CDN でキャッシュされた同じファイルのコピーが複数得られるようになります。  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium from Verizon
Verizon Premium ルール エンジンを使用する場合、要求の **Origin** ヘッダーを確認するための [ルールを作成](./cdn-verizon-premium-rules-engine.md)する必要があります。  オリジンが有効である場合、ルールによって、要求で指定されたオリジンが **Access-Control-Allow-Origin** ヘッダーに設定されます。  **Origin** ヘッダーで指定されたオリジンが許可されない場合、ルールによって **Access-Control-Allow-Origin** ヘッダーが省略されます。その結果、ブラウザーは要求を拒否します。 

Premium ルール エンジンを使用してこれを行う方法は 2 つあります。 どちらの場合でも、ファイルの配信元サーバーからの **Access-Control-Allow-Origin** ヘッダーは無視され、CDN のルール エンジンが、許可される CORS オリジンを完全に管理します。

#### <a name="one-regular-expression-with-all-valid-origins"></a>有効なオリジンがすべて含まれる 1 つの正規表現
この場合、許可するオリジンがすべて含まれた正規表現を作成します。 

```http
https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
```

> [!TIP]
> **Azure CDN Premium from Verizon** では、正規表現を作成するエンジンとして [Perl Compatible Regular Expressions](https://pcre.org/) を使用します。  正規表現を検証するには、[Regular Expressions 101](https://regex101.com/) などのツールを使用できます。  スラッシュ (/) は正規表現で有効であり、エスケープする必要はありません。ただし、この文字のエスケープはベスト プラクティスだと考えられており、一部の正規表現検証ツールではエスケープするよう想定されていることに注意してください。
> 
> 

正規表現に一致すると、ルールによって、オリジンの **Access-Control-Allow-Origin** ヘッダーが (存在する場合)、要求の送信元のオリジンに置き換えられます。  **Access-Control-Allow-Methods** などのその他の CORS ヘッダーを追加することもできます。

![Rules example with regular expression](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>オリジンごとの要求ヘッダー ルール
正規表現を使用するのではなく、許可するオリジンごとに個別のルールを作成することもできます。この場合、**Request Header Wildcard (要求ヘッダーのワイルドカード)** [一致条件](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)を使用します。 正規表現の方法と同様に、ルール エンジンは単独で CORS ヘッダーを設定します。 

![Rules example without regular expression](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> 上記の例では、ワイルドカード文字 * を使うことで、HTTP と HTTPS の両方を照合するようにルール エンジンに指示しています。
> 
>