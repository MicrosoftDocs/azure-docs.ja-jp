---
title: Azure Front Door Standard または Premium のルートとは
description: この記事は、Azure Front Door Standard または Premium で着信要求に使用するルーティング規則をどのように一致させるかを理解するのに役立ちます。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: db026c4903aa30a0a4c8154af8ad6eeb4b72b706
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098027"
---
# <a name="what-is-azure-front-door-standardpremium-preview-route"></a>Azure Front Door Standard または Premium (プレビュー) のルートとは

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door Standard または Premium のルートでは、着信要求が Azure Front Door 環境に到着したときのトラフィックの処理方法が定義されます。 ルート設定を使用して、ドメインとバックエンド配信元グループとの間の関連付けが定義されます。 一致させるパターン、ルール セットなどの高度な機能をオンにすることによって、トラフィックに対してより細かい制御を実現できます。

Front Door Standard または Premium のルーティング構成は、「左側」と「右側」という 2 つの主要な部分で構成されています。 着信要求は、ルートの左側に一致させます。右側には要求の処理方法が定義されています。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

### <a name="incoming-match-left-hand-side"></a>着信の照合 (左側)

以下のプロパティにより、着信要求がルーティング規則 (または左側) と一致するかどうかが判断されます。

* **HTTP プロトコル** (HTTP/HTTPS)
* **ホスト** (www\.foo.com、\*.bar.com など)
* **パス** (/\*、/users/\*、/file.gif など)

これらのプロパティは、内部で展開されているので、Protocol/Host/Path のすべての組み合わせが照合の対象となります。

### <a name="route-data-right-hand-side"></a>ルートデータ (右側)

要求の処理方法は、ルートに対してキャッシュが有効になっているかどうかで決まります。 キャッシュされた応答を使用できない場合、要求は適切なバックエンドに転送されます。

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

Azure Front Door Standard または Premium では、特定のフロントエンド ホストを決定した後、候補のルーティング規則をフィルター処理して、そのフロントエンド ホストのルートのみに絞り込みます。 次に、要求パスに基づいてルーティング規則をフィルター処理します。 フロント エンド ホストと同様のロジックを使用します。

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

Azure Front Door Standard または Premium では、1 つのルーティング規則に一致した場合、次にその要求の処理方法を選択する必要があります。 一致したルーティング規則に対して使用できるキャッシュ応答が Azure Front Door Standard または Premium ある場合、要求はクライアントに返されます。 Azure Front Door Standard または Premium で次に評価されるのは、一致したルーティング規則用のルール セットがあるかどうかです。 ルール セットが定義されていない場合、要求はそのままバックエンド プールに転送されます。 それ以外の場合は、構成されている順序でルール セットが実行されます。

## <a name="next-steps"></a>次のステップ

[Front Door Standard または Premium を作成する](create-front-door-portal.md)方法について学習します。
