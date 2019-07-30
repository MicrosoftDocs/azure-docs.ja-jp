---
title: Azure Front Door Service - URL 書き換え | Microsoft Docs
description: この記事では、Azure Front Door Service で構成されている場合に、ルートに対して URL 書き換えがどのように実行されるかについて説明します。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736185"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 書き換え (カスタム転送パス)
Azure Front Door Service では、バックエンドに転送する要求の作成時に使用する、省略可能な**カスタム転送パス**の構成を許可することで、URL 書き換えがサポートされています。 既定では、カスタム転送パスが指定されていない場合、Front Door により、転送された要求で使用される URL に受信 URL パスがコピーされます。 転送された要求で使用されるホスト ヘッダーは、選択されたバックエンド用に構成されています。 その機能と構成方法については、「[バックエンド ホスト ヘッダー](front-door-backend-pool.md#hostheader)」をご覧ください。

カスタム転送パスを使用した URL 書き換えの強力な部分では、転送されたパスに、ワイルドカード パスと一致する受信パスの任意の部分をコピーします (これらのパス セグメントは、次の例の**緑色**のセグメントです)。
</br>
![Azure Front Door の URL 書き換え][1]

## <a name="url-rewrite-example"></a>URL 書き換えの例
次のフロントエンド ホストおよびパスが構成されているルーティング規則を考えてみてください。

| ホスト      | パス       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

次のテーブルの最初の列は、受信要求の例を示し、2 番目の列は、"代表的な" 一致するルート 'パス' を示しています。  テーブルの最初の行の 3 番目以降の列は、構成された**カスタム転送パス**の例であり、これらの列の残りの行は、転送された要求パスがその行内の要求と一致した場合にどのようになるかの例を示しています。

たとえば、2 番目の行全体を読み取ると、受信要求 `www.contoso.com/sub` についてカスタム転送パスが `/` である場合、転送されたパスは `/sub` になることがわかります。 カスタム転送パスが `/fwd/` である場合、転送されたパスは `/fwd/sub` になります。 残りの列についても同様です。 次のパス内の**太字で表示された**部分は、ワイルドカード一致に含まれる部分を表しています。


| 着信要求       | 代表的な一致パス | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>オプション設定
所定のルーティング規則設定に指定できる、追加のオプション設定もあります。

* **[Cache Configuration]/(キャッシュ構成/)** - 無効になっているか、または指定されていない場合、このルーティング規則と一致する要求では、キャッシュされたコンテンツを使用しようとせず、代わりに常にバックエンドからフェッチします。 [Front Door でのキャッシュ](front-door-caching.md)に関する詳細をご覧ください。



## <a name="next-steps"></a>次の手順

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg