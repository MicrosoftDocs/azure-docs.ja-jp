---
title: Azure Front Door - URL 書き換え | Microsoft Docs
description: この記事では、Azure Front Door で構成されている場合に、ルートに対する URL 書き換えがどのように実行されるかについて説明します。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445485"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 書き換え (カスタム転送パス)
Azure Front Door を使用すると、バックエンドに転送する要求を作成するときに使用するオプションの **カスタム転送パス** を構成することで、URL 書き換えを行うことができます。 既定では、カスタム転送パスが指定されていない場合、Front Door により、転送された要求で使用される URL に受信 URL パスがコピーされます。 転送された要求で使用されるホスト ヘッダーは、選択されたバックエンド用に構成されています。 その機能と構成方法については、「[バックエンド ホスト ヘッダー](front-door-backend-pool.md#hostheader)」をご覧ください。

URL 書き換えの強力な部分としては、カスタム転送パスによって、転送されたパスに、ワイルドカード パスと一致する受信パスの任意の部分がコピーされることです (これらのパス セグメントは、次の例の **緑色** のセグメントです)。
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure Front Door の URL 書き換え":::

## <a name="url-rewrite-example"></a>URL 書き換えの例
次のフロントエンド ホストとパスの組み合わせが構成されているルーティング規則を考えてみましょう。

| Hosts      | パス       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

次のテーブルの最初の列は、受信要求の例を示し、2 番目の列は、"代表的な" 一致するルート 'パス' を示しています。  テーブルの 3 番目とその後の列は、**カスタム転送パス** を構成した例です。

たとえば、2 番目の行全体を読み取ると、受信要求 `www.contoso.com/sub` についてカスタム転送パスが `/` である場合、転送されたパスは `/sub` になることがわかります。 カスタム転送パスが `/fwd/` である場合、転送されたパスは `/fwd/sub` になります。 残りの列についても同様です。 次のパス内の **太字で表示された** 部分は、ワイルドカード一致に含まれる部分を表しています。

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

* **[Cache Configuration]\(キャッシュ構成\)** - 無効になっているか、または指定されていない場合、このルーティング規則と一致する要求から、キャッシュされたコンテンツを使用しようとするのではなく、代わりに常にバックエンドからフェッチされます。 [Front Door でのキャッシュ](front-door-caching.md)に関する詳細をご覧ください。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
