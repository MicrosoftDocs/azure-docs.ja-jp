---
title: Azure Front Door - HTTP2 のサポート | Microsoft Docs
description: この記事は、Azure Front Door の HTTP/2 サポートについて学習するのに役立ちます
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91448698"
---
# <a name="http2-support-in-azure-front-door"></a>Azure Front Door の HTTP/2 サポート

現在、HTTP/2 サポートは、すべての Azure Front Door 構成に対してアクティブです。 顧客からのそれ以上のアクションは必要ありません。

HTTP/2 は HTTP/1.1 のメジャー リビジョンであり、応答時間を短縮することにより、Web パフォーマンスを向上させることができます。 HTTP/2 は、使い慣れた HTTP メソッド、状態コード、およびセマンティクスを維持することによって、ユーザー エクスペリエンスを向上させます。 HTTP/2 は HTTP および HTTPS と連携して動作するように設計されていますが、多くのクライアント Web ブラウザーは、HTTP/2 over TLS (トランスポート層セキュリティ) のみをサポートしています。

> [!NOTE]
> HTTP/2 プロトコルのサポートは、クライアントから Front Door への要求に対してのみ使用できます。 バックエンド プールでの Front Door からバックエンドへの通信は、HTTP/1.1 経由で行われます。 

### <a name="http2-benefits"></a>HTTP/2 の利点

HTTP/2 の利点には、次のものがあります。

*   **多重化およびコンカレンシー**

    HTTP 1.1 を使用した場合、複数のリソース要求には複数の TCP 接続が必要になるため、各接続に関連してパフォーマンス オーバーヘッドが発生します。 HTTP/2 では、1 つの TCP 接続で複数のリソースを要求できます。

*   **ヘッダーの圧縮**

    提供されたリソースの HTTP ヘッダーを圧縮することにより、転送時間が大幅に短縮されます。

*   **ストリームの依存関係**

    ストリームの依存関係により、クライアントはサーバーにどのリソースに優先順位が割り当てられているかを示すことができます。


## <a name="http2-browser-support"></a>HTTP/2 のブラウザー サポート

主要なブラウザーはすべて、現在のバージョンで HTTP/2 サポートを実装しています。 サポートされていないブラウザーは、自動的に HTTP/1.1 にフォールバックします。

|Browser|最小バージョン|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>次の手順

HTTP/2 の詳細については、次のリソースを参照してください。

- [HTTP/2 の仕様のホームページ](https://http2.github.io/)
- [公式の HTTP/2 FAQ](https://http2.github.io/faq/)
- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
