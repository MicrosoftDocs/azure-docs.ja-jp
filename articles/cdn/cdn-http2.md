---
title: Azure CDN での HTTP/2 サポート | Microsoft Docs
description: HTTP/2 と CDN のサポートについて学習します。
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849973"
---
# <a name="http2-support-in-azure-cdn"></a>Azure CDN での HTTP/2 サポート

HTTP/2 は HTTP/1.1 に対するメジャー リビジョンです。 これにより、Web パフォーマンスの高速化、応答時間の短縮、ユーザー エクスペリエンスの向上が実現される一方で、使い慣れた HTTP メソッド、状態コード、およびセマンティクスは維持されます。 HTTP/2 は HTTP や HTTPS と連携して動作するように設計されていますが、多くのクライアント Web ブラウザーは HTTP/2 over TLS のみをサポートしています。

### <a name="http2-benefits"></a>HTTP/2 の利点

HTTP/2 の利点には、次のものがあります。

*   **多重化およびコンカレンシー**

    HTTP 1.1 を使用した場合、複数のリソース要求には複数の TCP 接続が必要になるため、各接続に関連してパフォーマンス オーバーヘッドが発生します。 HTTP/2 では、1 つの TCP 接続で複数のリソースを要求できます。

*   **ヘッダーの圧縮**

    提供されたリソースの HTTP ヘッダーを圧縮することにより、転送時間が大幅に短縮されます。

*   **ストリームの依存関係**

    ストリームの依存関係により、クライアントはサーバーにどのリソースに優先順位が割り当てられているかを示すことができます。


## <a name="http2-browser-support"></a>HTTP/2 のブラウザーサポート

主要なブラウザーはすべて、現在のバージョンで HTTP/2 サポートを実装しています。 サポートされていないブラウザーは、自動的に HTTP/1.1 にフォールバックします。

|Browser|最小バージョン|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Azure CDN での HTTP/2 サポートの有効化

現在、HTTP/2 サポートは、すべての Azure CDN プロファイルに対してアクティブです。 顧客からのそれ以上のアクションは必要ありません。

## <a name="next-steps"></a>次の手順

アクションにおける HTTP/2 の利点を確認するには、[Akamai のこのデモ](https://http2.akamai.com/demo)を参照してください。

HTTP/2 の詳細については、次のリソースを参照してください。

*   [HTTP/2 の仕様のホームページ](https://http2.github.io/)
*   [公式の HTTP/2 FAQ](https://http2.github.io/faq/)
*   [Akamai HTTP/2 の情報](https://http2.akamai.com/)

Azure CDN の使用可能な機能の詳細については、「[Azure CDN の概要](https://azure.microsoft.com/documentation/articles/cdn-overview/)」を参照してください。