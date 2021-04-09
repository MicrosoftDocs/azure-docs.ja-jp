---
title: Azure Cache for Redis 計画に関するよくあるご質問
description: Azure Cache for Redis の計画に役立つ、よくある質問に対する回答について説明します。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398b567e173691068e6dd932e075d6598dcfe601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537407"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Azure Cache for Redis 計画に関するよくあるご質問

この記事では、Azure Cache for Redis の計画に役立つ、よくある質問に対する回答について説明します。

## <a name="common-questions-and-answers"></a>一般的な質問と回答
このセクションでは、次のよくあるご質問を取り上げます。

* [Azure Cache for Redis のパフォーマンス](#azure-cache-for-redis-performance)
* [キャッシュを配置するリージョン](#in-what-region-should-i-locate-my-cache)
* [キャッシュ データが存在する場所](#where-do-my-cached-data-reside)
* [Azure Cache for Redis の課金方法](#how-am-i-billed-for-azure-cache-for-redis)
* [Azure Cache for Redis を Azure Government Cloud、Azure China 21Vianet Cloud、または Microsoft Azure Germany で使用できるか](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure Cache for Redis のパフォーマンス
次の表に、Azure Cache for Redis のエンドポイントに対して IaaS VM から `redis-benchmark.exe` を使用して、Standard および Premium キャッシュのさまざまなサイズをテストした際に測定された最大帯域幅を示します。 TLS のスループットについては、Azure Cache for Redis エンドポイントに接続するため、Redis ベンチマークは stunnel と共に使用されています。

>[!NOTE] 
>これらの値は保証された値ではなく、これらの値の SLA もありません。これらの値は、標準的な値と考えてください。 アプリケーションに最適なキャッシュ サイズを特定するには、アプリケーションに対してロード テストを実行する必要があります。
>定期的に新しい結果を公表しているため、これらの数字は変わる可能性があります。
>

この表からは次のような結論が得られます。

* キャッシュのサイズが同じ場合のスループットは、Standard レベルより Premium レベルの方が高くなります。 たとえば、P1 のスループットは、6 GB キャッシュでは 180,000 要求/秒 (RPS) であるのに対し、C3 では 100,000 RPS となります。
* Redis クラスタリングでは、クラスターのシャード (ノード) の数を増やすと、スループットもそれに比例して増加する。 たとえば、10 個のシャードから成る P4 クラスターを作成すると、利用可能なスループットは 400,000 * 10 = 4 百万 RPS になります。
* キー サイズを大きくしたときのスループットは、Standard レベルより Premium レベルのほうが高い。

| Pricing tier | サイズ | CPU コア数 | 使用可能な帯域幅 | 1 KB 値サイズ | 1 KB 値サイズ |
| --- | --- | --- | --- | --- | --- |
| **Standard のキャッシュ サイズ** | | |**メガビット/秒 (Mb/s) / メガバイト/秒 (MB/s)** |**1 秒あたりの要求数 (RPS) 非 SSL** |**1 秒あたりの要求数 (RPS) SSL** |
| C0 | 250 MB | 共有 | 100 / 12.5  |  15,000 |   7,500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2.5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100,000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60,000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Premium のキャッシュ サイズ** | |**シャードあたりの CPU コア数** | **メガビット/秒 (Mb/s) / メガバイト/秒 (MB/s)** |**1 秒あたりの要求数 (RPS) 非 SSL、シャードあたり** |**1 秒あたりの要求数 (RPS) SSL、シャードあたり** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

stunnel の設定や `redis-benchmark.exe` などの Redis ツールのダウンロードの詳細については、「[Redis コマンドの実行方法](cache-development-faq.md#how-can-i-run-redis-commands)」を参照してください。

### <a name="in-what-region-should-i-locate-my-cache"></a>キャッシュを配置するリージョン
最大限のパフォーマンスと最短の待機時間を実現するには、キャッシュ クライアント アプリケーションと同じリージョンに Azure Cache for Redis を配置します。

### <a name="where-do-my-cached-data-reside"></a>キャッシュ データが存在する場所
Azure Cache for Redis は、レベルに応じて、キャッシュをホストする VM の RAM にアプリケーション データを格納します。 データは、既定で選択した Azure リージョンに厳密に格納されます。 データがリージョンの外に出る可能性があるケースが 2 つあります。
* キャッシュでの永続化を有効にすると、Azure Cache for Redis によって、自分が所有している Azure Storage アカウントにデータがバックアップされます。 指定したストレージ アカウントがたまたま別のリージョンにある場合、データのコピーは最終的にはそこに格納されます。
* geo レプリケーションを設定し、セカンダリ キャッシュが別のリージョンにある場合 (通常はこのケース)、データはそのリージョンにレプリケートされます。

これらの機能を使用するように、Azure Cache for Redis を明示的に構成する必要があります。 また、ストレージ アカウントまたはセカンダリ キャッシュが配置されているリージョンを完全に制御することもできます。

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Azure Cache for Redis の課金方法
Azure Cache for Redis の価格は[ここ](https://azure.microsoft.com/pricing/details/cache/)に記載されています。 価格ページには、1 時間単位の価格と月額料金が表示されます。 キャッシュは、キャッシュが作成された時間から削除された時間までの期間に関して、分単位で課金されます。 キャッシュの課金を停止または一時停止するオプションはありません。

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Azure Cache for Redis を Azure Government Cloud、Azure China 21Vianet Cloud、または Microsoft Azure Germany で使用できるか
はい。Azure Cache for Redis は、Azure Government Cloud、Azure China 21Vianet Cloud、Microsoft Azure Germany で使用できます。 ただし、Azure Cache for Redis のアクセスと管理を行うための URL については、これらのクラウドと、Azure パブリック クラウドとで異なります。

| クラウド   | Redis の DNS サフィックス            |
|---------|---------------------------------|
| パブリック  | *.redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| ドイツ | *.redis.cache.cloudapi.de       |
| 中国   | *.redis.cache.chinacloudapi.cn  |

その他のクラウドで Azure Cache for Redis を使用するときの考慮事項の詳細については、次のリンクを参照してください。

- [Azure Government データベース - Azure Cache for Redis](../azure-government/compare-azure-government-global-azure.md)
- [Azure China 21Vianet Cloud - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)

Azure Government Cloud、Azure China 21Vianet Cloud、Microsoft Azure Germany での PowerShell を使用した Azure Cache for Redis の利用の詳細については、[他のクラウドに接続する方法 - Azure Cache for Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds) に関するページを参照してください。

## <a name="next-steps"></a>次の手順

その他の [Azure Cache for Redis のよくあるご質問](cache-faq.md)について。