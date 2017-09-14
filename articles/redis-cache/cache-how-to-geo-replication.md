---
title: "Azure Redis Cache の geo レプリケーションの構成方法 | Microsoft Docs"
description: "地理的リージョン全体で Azure Redis Cache インスタンスをレプリケートする方法を説明します。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 71b0d4add7e642487f6d67cda692c500ee78b0e6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/08/2017


---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Azure Redis Cache の geo レプリケーションの構成方法

geo レプリケーションは、Premium レベルの Azure Redis Cache の 2 つのインスタンスをリンクするメカニズムを用意しています。 一方のキャッシュはプライマリ リンク キャッシュとして、他方はセカンダリ リンク キャッシュとして指定されます。 セカンダリ リンク キャッシュは読み取り専用になり、プライマリ キャッシュに書き込まれたデータがセカンダリ リンク キャッシュにレプリケートされます。 この機能は、Azure リージョン間でキャッシュをレプリケートする際に使用できます。 この記事では、Premium レベルの Azure Redis Cache インスタンスの geo レプリケーションを構成するためのガイドを提供します。

## <a name="geo-replication-prerequisites"></a>geo レプリケーションの前提条件

2 つのキャッシュの間に geo レプリケーションを構成するには、次の前提条件を満たす必要があります。

- 両方のキャッシュに [Premium レベル](cache-premium-tier-intro.md)のキャッシュが必要です。
- 両方のキャッシュが同じ Azure サブスクリプションにある必要があります。
- セカンダリ リンク キャッシュは、プライマリ リンク キャッシュと同じ価格レベルまたはそれより高い価格レベルにある必要があります。
- プライマリ リンク キャッシュでクラスタリングが有効になっている場合、セカンダリ リンク キャッシュではプライマリ リンク キャッシュと同じ数のシャードでクラスタリングが有効になっている必要があります。
- 両方のキャッシュが作成され、実行状態になっている必要があります。
- どちらのキャッシュでも永続化が有効になっていない必要があります。
- 同じ VNET 内のキャッシュ間の geo レプリケーションがサポートされています。 VNET 内のリソースが TCP 接続を使って互いに到達できるように 2 つの VNET が構成されている限り、異なる VNET 内のキャッシュ間の geo レプリケーションもサポートされます。

geo レプリケーションを構成した後、次の制限が、リンク キャッシュ ペアに適用されます。

- セカンダリ リンク キャッシュは読み取り専用です。読み取ることはできますが、データを書き込むことはできません。 
- リンクが追加される前にセカンダリ リンク キャッシュにあったデータはすべて削除されます。 ただし、その後 geo レプリケーションが削除された場合、レプリケートされたデータはセカンダリ リンク キャッシュに残ります。
- キャッシュのクラスタリングが有効になっている場合、どちらかのキャッシュで[スケーリング操作](cache-how-to-scale.md)を開始したり、[シャード数を変更](cache-how-to-premium-clustering.md)したりすることはできません。
- どちらのキャッシュでも永続化を有効にすることはできません。
- どちらのキャッシュでも[エクスポート](cache-how-to-import-export-data.md#export)を使用できますが、[インポート](cache-how-to-import-export-data.md#import)できるのはプライマリ リンク キャッシュに対してだけです。
- リンクされたキャッシュのうちの片方やそれらのキャッシュを含むリソース グループは、geo レプリケーション リンクを削除するまで、削除できません。 詳しくは、「[リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)」をご覧ください。
- 2 つのキャッシュが異なるリージョンにある場合、ネットワーク送信のコストは、リージョン全体でセカンダリ リンク キャッシュにレプリケートされたデータに適用されます。 詳しくは、「[Azure リージョン間でデータをレプリケートするコストはどれくらいですか](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)」をご覧ください。
- プライマリ キャッシュ (およびそのレプリカ) がダウンした場合、セカンダリ リンク キャッシュへの自動フェールオーバーは行われません。 クライアント アプリケーションをフェールオーバーするには、geo レプリケーション リンクを手動で解除し、以前はセカンダリ リンク キャッシュであったキャッシュをクライアント アプリケーションでポイントする必要があります。 詳しくは、「[セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか](#how-does-failing-over-to-the-secondary-linked-cache-work)」をご覧ください。

## <a name="add-a-geo-replication-link"></a>geo レプリケーション リンクの追加

1. geo レプリケーションの 2 つの Premium キャッシュをリンクするには、プライマリ リンク キャッシュとするキャッシュの [リソース] メニューで **[geo レプリケーション]** をクリックし、**[geo レプリケーション]** ブレードで **[Add cache replication link]\(キャッシュ レプリケーション リンクの追加\)** をクリックします。

    ![リンクの追加](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. **[Compatible caches]\(互換性のあるキャッシュ\)** の一覧で目的のセカンダリ キャッシュの名前をクリックします。 目的のキャッシュが一覧に表示されていない場合は、目的のセカンダリ キャッシュの [geo レプリケーションの前提条件](#geo-replication-prerequisites)が満たされていることを確認します。 キャッシュをリージョンでフィルターするには、マップ内の目的のリージョンをクリックして、**[Compatible caches]\(互換性のあるキャッシュ\)** の一覧にあるキャッシュのみを表示します。

    ![geo レプリケーションの互換性のあるキャッシュ](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    コンテキスト メニューを使って、リンク プロセスの開始やセカンダリ キャッシュに関する詳細の表示を行うこともできます。

    ![geo レプリケーションのコンテキスト メニュー](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. **[リンク]**をクリックして、2 つのキャッシュをリンクし、レプリケーション プロセスを開始します。

    ![キャッシュのリンク](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. **[geo レプリケーション]** ブレードで、レプリケーション プロセスの進行状況を表示できます。

    ![リンクの状態](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    プライマリ キャッシュとセカンダリ キャッシュの両方の **[概要]** ブレードで、リンクの状態を表示することもできます。

    ![キャッシュの状態](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    レプリケーション プロセスが完了すると、**[リンクの状態]** が「**成功**」に変わります。

    ![キャッシュの状態](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    リンク プロセス中もプライマリ リンク キャッシュは使用可能なままですが、セカンダリ リンク キャッシュはリンク プロセスが完了するまで使用できません。

## <a name="remove-a-geo-replication-link"></a>geo レプリケーション リンクの削除

1. 2 つのキャッシュ間のリンクを削除し、geo レプリケーションを停止するには、**[geo レプリケーション]** ブレードで **[Unlink caches]\(キャッシュのリンク解除\)** をクリックします。
    
    ![[Unlink caches]\(キャッシュのリンク解除\)](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    リンク解除プロセスが完了すると、セカンダリ キャッシュが読み取りと書き込みの両方に対して使用可能になります。

>[!NOTE]
>geo レプリケーション リンクが削除された場合も、プライマリ リンク キャッシュからレプリケートされたデータはセカンダリ キャッシュに残ります。
>
>

## <a name="geo-replication-faq"></a>geo レプリケーションに関する FAQ

- [Standard または Basic レベル キャッシュで geo レプリケーションを使用することはできますか](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [リンクまたはリンク解除のプロセス中にキャッシュを使用できますか](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [3 つ以上のキャッシュをリンクできますか](#can-i-link-more-than-two-caches-together)
- [異なる Azure サブスクリプションからの 2 つのキャッシュをリンクすることはできますか](#can-i-link-two-caches-from-different-azure-subscriptions)
- [サイズが異なる 2 つのキャッシュをリンクすることはできますか](#can-i-link-two-caches-with-different-sizes)
- [クラスタリングを有効にして geo レプリケーションを使用することはできますか](#can-i-use-geo-replication-with-clustering-enabled)
- [VNET 内の自分のキャッシュで geo レプリケーションを使用することはできますか](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [PowerShell または Azure CLI を使って geo レプリケーションを管理することはできますか](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure リージョン間でデータをレプリケートするコストはどれくらいですか](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [セカンダリ リンク キャッシュにはどのリージョンを使う必要がありますか](#what-region-should-i-use-for-my-secondary-linked-cache)
- [セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Standard または Basic レベル キャッシュで geo レプリケーションを使用することはできますか

geo レプリケーションは、Premium レベルのキャッシュにのみ使用できます。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>リンクまたはリンク解除のプロセス中にキャッシュを使用できますか

- geo レプリケーションの 2 つのキャッシュをリンクする際、プライマリ リンク キャッシュは使用可能なままですが、セカンダリ リンク キャッシュはリンク プロセスが完了するまで使用できません。
- 2 つのキャッシュ間の geo レプリケーション リンクを削除する際には、両方のキャッシュが使用可能なままになります。

### <a name="can-i-link-more-than-two-caches-together"></a>3 つ以上のキャッシュをリンクできますか

いいえ、geo レプリケーションを使用している場合は、2 つのキャッシュのみをリンクできます。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>異なる Azure サブスクリプションからの 2 つのキャッシュをリンクすることはできますか

いいえ、両方のキャッシュが同じ Azure サブスクリプションにある必要があります。

### <a name="can-i-link-two-caches-with-different-sizes"></a>サイズが異なる 2 つのキャッシュをリンクすることはできますか

セカンダリ リンク キャッシュがプライマリ リンク キャッシュより大きい場合は可能です。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>クラスタリングを有効にして geo レプリケーションを使用することはできますか

両方のキャッシュのシャード数が同じ場合は可能です。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET 内の自分のキャッシュで geo レプリケーションを使用することはできますか

はい、VNET 内のキャッシュの geo レプリケーションはサポートされています。 

- 同じ VNET 内のキャッシュ間の geo レプリケーションがサポートされています。
- VNET 内のリソースが TCP 接続を使って互いに到達できるように 2 つの VNET が構成されている限り、異なる VNET 内のキャッシュ間の geo レプリケーションもサポートされます。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell または Azure CLI を使って geo レプリケーションを管理することはできますか

現時点では、Azure Portal を使った geo レプリケーションの管理のみ可能です。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure リージョン間でデータをレプリケートするコストはどれくらいですか

geo レプリケーションを使用している場合、プライマリ リンク キャッシュからのデータは、セカンダリ リンク キャッシュにレプリケートされます。 2 つのリンク キャッシュが同じ Azure リージョンにある場合、データ転送には課金されません。 2 つのリンク キャッシュが異なる Azure リージョンにある場合、geo レプリケーション データ転送料金は、そのデータを他の Azure リージョンにレプリケートする際の帯域幅コストです。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか

2 つのキャッシュがリンクされている場合、リンクされた片方のキャッシュや、それらのキャッシュを含むリソース グループは、geo レプリケーション リンクを削除するまで、削除することはできません。 リンクされたキャッシュの一方または両方を含むリソース グループを削除しようとすると、リソース グループ内の他のリソースは削除されますが、リソース グループは `deleting` 状態のままとなり、リソース グループ内にあるリンクされたキャッシュは `running` 状態のままとなります。 リソース グループとその中にあるリンクされたキャッシュを完全に削除するには、「[geo レプリケーション リンクの削除](#remove-a-geo-replication-link)」にある説明に従って、geo レプリケーション リンクを解除します。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>セカンダリ リンク キャッシュにはどのリージョンを使う必要がありますか

一般に、キャッシュはそれにアクセスするアプリケーションと同じ Azure リージョンに置くことをお勧めします。 アプリケーションにプライマリおよびフォールバック リージョンがある場合、プライマリとセカンダリのキャッシュは、その同じリージョンに存在する必要があります。 ペアになっているリージョンについて詳しくは、[ベスト プラクティス - ペアになっている Azure リージョンに関する記事](../best-practices-availability-paired-regions.md)をご覧ください。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか

geo レプリケーションの最初のリリースでは、Azure Redis Cache は Azure リージョン間の自動フェールオーバーをサポートしていません。 geo レプリケーションは、主にディザスター リカバリー シナリオで使われます。 ディザスター リカバリー シナリオでは、個々のアプリケーション コンポーネントで独自のバックアップに切り替えるタイミングを決定するのではなく、顧客が調整してバックアップ リージョンのアプリケーション スタック全体を起動する必要があります。 これは特に Redis に関連します。 Redis の主要な利点の 1 つは、待機時間がとても短いストアであることです。 アプリケーションで使われている Redis が異なる Azure リージョンにフェールオーバーし、コンピューティング層がフェールオーバーしない場合は、追加のラウンド トリップ時間がパフォーマンスに大きな影響を与えます。 このため、一時的な可用性の問題による Redis の自動的なフェールオーバーを回避します。

現時点では、フェールオーバーを開始するには、Azure Portal で geo レプリケーション リンクを削除してから、Redis クライアントの接続エンドポイントをプライマリ リンク キャッシュから (以前にリンクされていた) セカンダリ キャッシュに変更する必要があります。 2 つのキャッシュの関連付けが解除されると、レプリカが再び正規の読み取り/書き込みキャッシュになり、Redis クライアントからの要求を直接受け入れます。


## <a name="next-steps"></a>次のステップ

[Azure Redis Cache Premium レベル](cache-premium-tier-intro.md)の詳細を確認します。


