---
title: "Azure CDN での HTTP/2 サポート | Microsoft Docs"
description: "HTTP/2 と CDN のサポートについて学習します。"
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: 4f8dd685c3ae89535217d7a17a01c5129ca7e6e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="http2-support-in-azure-cdn"></a>Azure CDN での HTTP/2 サポート

Http/2 は、メジャー リビジョン HTTP/1.1\ です。 高速化は提供使い慣れた HTTP メソッド、ステータス コードとセマンティクスを維持しながら、web パフォーマンス、短縮された応答時間、および改良されたユーザーが発生します。 HTTP/2 は HTTP や HTTPS と連携して動作するように設計されていますが、多くのクライアント Web ブラウザーは HTTP/2 over TLS のみをサポートしています。

###<a name="http2-benefits"></a>HTTP/2 の利点

HTTP/2 の利点には、次のものがあります。

*   **多重化および同時実行**

    HTTP 1.1 を使用した場合、複数のリソース要求には複数の TCP 接続が必要になるため、各接続に関連してパフォーマンス オーバーヘッドが発生します。 HTTP/2 では、1 つの TCP 接続で複数のリソースを要求できます。

*   **ヘッダーの圧縮**

    提供されたリソースの HTTP ヘッダーを圧縮することにより、転送時間が大幅に短縮されます。

*   **ストリームの依存関係**

    ストリームの依存関係により、クライアントはサーバーにどのリソースに優先順位が割り当てられているかを示すことができます。


##<a name="http2-browser-support"></a>HTTP/2 のブラウザーサポート

主要なブラウザーはすべて、現在のバージョンで HTTP/2 サポートを実装しています。 サポートされていないブラウザーは、自動的に HTTP/1.1 にフォールバックします。

|[ブラウザー] ボタンを|最小バージョン|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

##<a name="enabling-http2-support-in-azure-cdn"></a>Azure CDN での HTTP/2 サポートの有効化

現在、HTTP/2 サポートは **Azure CDN from Akamai** および **Azure CDN from Verizon** プロファイルに対してアクティブです。 顧客からのそれ以上のアクションは必要ありません。

##<a name="next-steps"></a>次のステップ

アクションにおける HTTP/2 の利点を確認するには、[Akamai のこのデモ](https://http2.akamai.com/demo)を参照してください。

HTTP/2 の詳細については、次のリソースを参照してください。

*   [HTTP/2 の仕様のホームページ](https://http2.github.io/)
*   [公式の HTTP/2 FAQ](https://http2.github.io/faq/)
*   [Akamai HTTP/2 の情報](https://http2.akamai.com/)

Azure CDN の使用可能な機能の詳細については、「[Azure CDN の概要](https://azure.microsoft.com/documentation/articles/cdn-overview/)」を参照してください。