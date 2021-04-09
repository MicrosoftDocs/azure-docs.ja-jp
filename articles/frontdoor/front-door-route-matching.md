---
title: Azure Front Door - ルーティング規則照合の監視 | Microsoft Docs
description: この記事は、着信要求の照合において、Azure Front Door がどのルーティング規則を使用するかを理解するためのものです。
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
ms.openlocfilehash: 67940db973f494cd4a12c2f16db528e0b113d656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449210"
---
# <a name="how-requests-are-matched-to-a-routing-rule"></a>ルーティング規則に対する要求の照合方法

接続の確立と TLS ハンドシェイクが行われ、要求が Front Door 環境に届いたときに、Front Door で最初に行われることの 1 つとして、その要求と一致する特定のルーティング規則が決定され、構成内で定義されているアクションが実行されます。 以下のドキュメントは、HTTP 要求の処理において、Front Door がどのルート構成を使用するかを判断する方法について説明します。

## <a name="structure-of-a-front-door-route-configuration"></a>Front Door のルート構成の構造
Front Door のルーティング規則の構成は、大きく分けて「左側」と「右側」の 2 つの部分から成っています。 ルートの左側への着信要求は照合を実行し、右側はその要求をどう処理するかを定義します。

### <a name="incoming-match-left-hand-side"></a>着信の照合 (左側)
以下のプロパティにより、着信要求がルーティング規則 (または左側) と一致するかどうかが判断されます。

* **HTTP プロトコル** (HTTP/HTTPS)
* **ホスト** (www\.foo.com、\*.bar.com など)
* **パス** (/\*、/users/\*、/file.gif など)

これらのプロパティは、内部で展開されているので、Protocol/Host/Path のすべての組み合わせが照合の対象となります。

### <a name="route-data-right-hand-side"></a>ルートデータ (右側)
要求の処理方法の決定は、キャッシュが有効として設定されているか、または特定のルートのものでないかによって下されます。 そのため、要求へのキャッシュ応答が存在しない場合は、構成されたバックエンド プール内の適切なバックエンドに当該要求が転送されます。

## <a name="route-matching"></a>ルートの照合
このセクションは、Front Door のルーティング規則への照合方法に焦点を当てます。 基本的な概念は、まず「左側」のみを見て、**もっとも一致率が高いもの** を常に照合するということです。  まず HTTP プロトコル、フロント エンドのホスト、Path の順で照合します。

### <a name="frontend-host-matching"></a>フロント エンド ホストの照合
フロント エンドのホストを照合するときは、次に定義するロジックを使用します。

1. ホストに完全に一致するルートを探します。
2. 完全に一致するフロント エンドのホストが存在しない場合は、要求を拒否し、400 Bad Request エラーを送信します。

このプロセスをさらに説明するために、Front Door のルート (左側のみ) の構成の例を見ます。

| ルーティング ルール | フロント エンド ホスト | Path |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C | www\.fabrikam.com, foo.adventure-works.com  | /\*、/images/\* |

次の着信要求が Front Door に送信された場合、以下のルーティング規則に対して、上から照合されます。

| 着信のフロント エンド ホスト | 一致するルーティング規則 |
|---------------------|---------------|
| foo.contoso.com | A、B |
| www\.fabrikam.com | C |
| images.fabrikam.com | Error 400: 正しくない要求 |
| foo.adventure-works.com | C |
| contoso.com | Error 400: 正しくない要求 |
| www\.adventure-works.com | Error 400: 正しくない要求 |
| www\.northwindtraders.com | Error 400: 正しくない要求 |

### <a name="path-matching"></a>Path の照合
Front Door は、フロント エンド ホストを特定し、そのフロント エンド ホストへのルートのみに関するルーティング規則のフィルター処理をした後、要求されたパスに基づきルーティング規則のフィルター処理をします。 フロント エンド ホストと同様のロジックを使用します。

1. 当該 Path 上で、完全に一致するルーティング規則を探します
2. 完全に一致する Path が存在しない場合、一致するワイルドカード パスが含まれるルーティング規則を探します
3. Path が一致するルーティング規則が存在しない場合、要求を拒否し、400: Bad Request エラー HTTP 応答を返します。

>[!NOTE]
> ワイルドカードが無い Path の場合、完全一致の Path とみなされます。 Path の最後にスラッシュがある場合であっても、完全一致とみなされます。

さらなる説明として、他の例を見ます。

| ルーティング ルール | フロントエンド ホスト    | Path     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

この構成の場合、照合結果は次のとおりとなります。

| 着信要求    | 一致するルート |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| www\.contoso.com/abc/        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> キャッチオール ルートの Path (`/*`) でフロント エンド ホストと完全に一致するルーティング規則が存在しない場合、いずれのルーティング規則とも一致しません。
>
> 構成の例
>
> | ルート | Host             | Path    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> 照合テーブル
>
> | 着信要求       | 一致するルート |
> |------------------------|---------------|
> | profile.domain.com/other | [なし] : Error 400: 正しくない要求 |

### <a name="routing-decision"></a>ルーティングの決定
一つの Front Door のルーティング規則と一致した場合、次にその要求の処理方法を選択する必要があります。 一致したルーティング規則に対して、Front Door にキャッシュされたレスポンスがある場合、そのレスポンスがクライアントに送信されます。 ない場合は、一致したルーティング規則に対して、[URL Rewrite (カスタム フォワーディング パス)](front-door-url-rewrite.md) が構成されているかどうかの確認がおこなわれます。 カスタム フォワーディング パスが定義されていない場合、その要求は、構成されたバックエンド プール内の適切なバックエンドにそのまま転送されます。 それ以外の場合、要求パスは、定義された[カスタム フォワーディング パス](front-door-url-rewrite.md)に従って更新された後、バックエンドに転送されます。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
