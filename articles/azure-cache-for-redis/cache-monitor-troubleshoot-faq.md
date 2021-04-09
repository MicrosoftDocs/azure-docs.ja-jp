---
title: Azure Cache for Redis の監視とトラブルシューティングに関するよくあるご質問
description: Azure Cache for Redis の監視とトラブルシューティングに役立つ、よくある質問に対する回答について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 8e96c73578a9341f67d90cd4482ed75179c6886d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537526"
---
# <a name="azure-cache-for-redis-monitoring-and-troubleshooting-faqs"></a>Azure Cache for Redis の監視とトラブルシューティングに関するよくあるご質問
この記事では、Azure Cache for Redis の監視とトラブルシューティングに関してよくある質問に対する回答を提供します。

## <a name="common-questions-and-answers"></a>一般的な質問と回答
このセクションでは、次のよくあるご質問を取り上げます。

* [キャッシュの正常性とパフォーマンスの監視方法](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [タイムアウトが発生する理由](#why-am-i-seeing-timeouts)
* [クライアントがキャッシュから切断される理由](#why-was-my-client-disconnected-from-the-cache)

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>キャッシュの正常性とパフォーマンスの監視方法
Microsoft Azure Cache for Redis のインスタンスは、[Azure Portal](https://portal.azure.com) で監視できます。 メトリックの表示、メトリック グラフのスタート画面へのピン留め、監視グラフの日付と時刻の範囲のカスタマイズ、グラフのメトリックの追加と削除、特定の条件が満たされた場合のアラートの設定を行うことができます。 詳細については、[Azure Cache for Redis の監視](cache-how-to-monitor.md)に関するページを参照してください。

Azure Cache for Redis の **[リソース] メニュー** にも、キャッシュの監視およびトラブルシューティングのためのツールがいくつか含まれています。

* **[問題の診断と解決]** では、一般的な問題と、その問題を解決するための戦略に関する情報を確認できます。
* **[リソース正常性]** ではリソースが監視され、そのリソースが意図したとおりに動いているかどうかが示されます。 Azure Resource Health サービスの詳細については、「 [Azure Resource Health の概要](../service-health/resource-health-overview.md)」を参照してください。
* **[新しいサポート要求]** には、キャッシュのサポート要求を開くためのオプションが用意されています。

これらのツールによって、Azure Cache for Redis インスタンスの正常性を監視でき、キャッシュ アプリケーションの管理が容易になります。 「[Azure Redis Cache の構成方法](cache-configure.md)」の「サポートおよびトラブルシューティング設定」を参照してください。

### <a name="why-am-i-seeing-timeouts"></a>タイムアウトが発生する理由
タイムアウトは、Redis との対話に使用されているクライアントで発生します。 Redis サーバーに送信されたコマンドは、キューに格納されます。コマンドは、最終的に Redis サーバーによって取得され、実行されます。 ただし、この処理中にクライアントがタイムアウトすることがあり、その場合は呼び出し元では例外が発生します。 タイムアウトの問題のトラブルシューティングについては、[クライアント側のトラブルシューティング](cache-troubleshoot-client.md)に関するページ、および「[StackExchange.Redis のタイムアウトの例外](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)」を参照してください。

### <a name="why-was-my-client-disconnected-from-the-cache"></a>クライアントがキャッシュから切断される理由
キャッシュが切断される一般的な理由のいくつかを次に示します。

* クライアント側の原因
  * クライアント アプリケーションが再デプロイされた。
  * クライアント アプリケーションがスケーリング操作を実行した。
    * Cloud Services または Web Apps では、これは自動スケーリングが原因である場合があります。
  * クライアント側のネットワーク レイヤーが変更された。
  * クライアントで、またはクライアントとサーバー間のネットワーク ノードで一時的なエラーが発生した。
  * 帯域幅のしきい値制限に達した。
  * CPU バインド型の操作の完了に時間がかかった。
* サーバー側の原因
  * Standard キャッシュ プランで、Azure Cache for Redis サービスがプライマリ ノードからレプリカ ノードへのフェールオーバーを開始した。
  * Azure により、キャッシュがデプロイされているインスタンスに修正プログラムが適用されていた。
    * Redis サーバーの更新または VM の一般的なメンテナンスの場合もこれに該当します。


## <a name="next-steps"></a>次の手順

Azure Cache for Redis インスタンスの監視とトラブルシューティングの詳細については、「[Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)」と、さまざまなトラブルシューティングのガイドを参照してください。

その他の [Azure Cache for Redis のよくあるご質問](cache-faq.md)について。