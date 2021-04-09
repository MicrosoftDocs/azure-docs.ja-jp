---
title: Premium Azure Cache for Redis インスタンスの geo レプリケーションを構成する
description: Azure リージョンをまたいで Azure Cache for Redis Premium インスタンスをレプリケートする方法について説明します。
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 8701f7bcb2e7ff705e4f1d1b401f4eb3e680f28b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501041"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Premium Azure Cache for Redis インスタンスの geo レプリケーションを構成する

この記事では、Azure portal を使用し、geo レプリケートされた Azure Cache を構成する方法について説明します。

geo レプリケーションによって 2 つの Premium Azure Cache for Redis インスタンスがリンクされ、データ レプリケーション関係が作られます。 これらのキャッシュ インスタンスは通常、異なる Azure リージョンに置かれますが、必須ではありません。 1 つのインスタンスはプライマリとして、もう 1 つはセカンダリとして機能します。 プライマリによって読み取りと書き込み要求が処理され、変更がセカンダリに反映されます。 2 つのインスタンス間のリンクが削除されるまで、このプロセスが続きます。

> [!NOTE]
> geo レプリケーションは、ディザスター リカバリー ソリューションとして設計されています。
>
>

## <a name="geo-replication-prerequisites"></a>geo レプリケーションの前提条件

2 つのキャッシュの間に geo レプリケーションを構成するには、次の前提条件を満たす必要があります。

- 両方のキャッシュが [Premium レベル](cache-overview.md#service-tiers)のキャッシュである。
- 両方のキャッシュが同じ Azure サブスクリプションに存在する。
- セカンダリ リンク キャッシュがプライマリ リンク キャッシュと同じキャッシュ サイズであるか、またはそれより大きいキャッシュ サイズである。
- 両方のキャッシュが作成され、実行状態になっている。

geo レプリケーションでは一部の機能がサポートされていません。

- 永続化は geo レプリケーションではサポートされていません。
- クラスタリングは、両方のキャッシュでクラスタリングが有効になっており、かつ同じ数のシャードが存在する場合にサポートされます。
- 同じ VNET 内のキャッシュはサポートされています。
- 異なる VNET 内のキャッシュは、注意事項付きでサポートされています。 詳細については、「[VNET 内の自分のキャッシュで geo レプリケーションを使用することはできますか](#can-i-use-geo-replication-with-my-caches-in-a-vnet)」を参照してください。

geo レプリケーションを構成した後、次の制限が、リンク キャッシュ ペアに適用されます。

- セカンダリ リンク キャッシュは読み取り専用です。読み取ることはできますが、データを書き込むことはできません。 
- リンクが追加される前にセカンダリ リンク キャッシュにあったデータはすべて削除されます。 ただし、geo レプリケーションが後で削除された場合、レプリケートされたデータはセカンダリ リンク キャッシュに残ります。
- キャッシュがリンクされている間は、どちらのキャッシュも[スケーリング](cache-how-to-scale.md)できません。
- キャッシュでクラスタリングが有効になっている場合は、[シャードの数を変更](cache-how-to-premium-clustering.md)できません。
- どちらのキャッシュでも永続化を有効にすることはできません。
- どちらのキャッシュからも[エクスポート](cache-how-to-import-export-data.md#export)できます。
- セカンダリ リンク キャッシュには[インポート](cache-how-to-import-export-data.md#import)できません。
- キャッシュをリンク解除するまでは、リンクされたキャッシュ、またはそれらを含むリソース グループのどちらも削除できません。 詳しくは、「[リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)」をご覧ください。
- キャッシュが異なるリージョンに存在する場合、ネットワーク送信コストはリージョン間で移動されたデータに適用されます。 詳しくは、「[Azure リージョン間でデータをレプリケートするコストはどれくらいですか](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)」をご覧ください。
- プライマリ リンク キャッシュとセカンダリ リンク キャッシュの間で自動フェールオーバーは発生しません。 詳細およびクライアント アプリケーションをフェールオーバーする方法については、「[セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか](#how-does-failing-over-to-the-secondary-linked-cache-work)」を参照してください。

## <a name="add-a-geo-replication-link"></a>geo レプリケーション リンクの追加

1. geo レプリケーションのために 2 つのキャッシュをリンクするには、まずプライマリ リンク キャッシュにするキャッシュのリソース メニューの **[geo レプリケーション]** をクリックします。 次に、 **[geo レプリケーション]** ブレードの **[キャッシュ レプリケーション リンクの追加]** をクリックします。

    ![リンクの追加](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. **[互換性のあるキャッシュ]** の一覧で目的のセカンダリ キャッシュの名前をクリックします。 そのセカンダリ キャッシュが一覧に表示されていない場合は、セカンダリ キャッシュの [geo レプリケーションの前提条件](#geo-replication-prerequisites)が満たされていることを確認します。 キャッシュをリージョンでフィルター処理するには、マップ内のリージョンをクリックして、 **[互換性のあるキャッシュ]** の一覧にあるキャッシュのみを表示します。

    ![geo レプリケーションの互換性のあるキャッシュ](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    また、コンテキスト メニューを使用してリンク プロセスを開始したり、セカンダリ キャッシュに関する詳細を表示したりすることもできます。

    ![geo レプリケーションのコンテキスト メニュー](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. **[リンク]** をクリックして、2 つのキャッシュをリンクし、レプリケーション プロセスを開始します。

    ![キャッシュのリンク](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. **[geo レプリケーション]** ブレードで、レプリケーション プロセスの進行状況を表示できます。

    ![リンクの状態](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    プライマリ キャッシュとセカンダリ キャッシュの両方の **[概要]** ブレードで、リンクの状態を表示することもできます。

    ![プライマリ キャッシュとセカンダリ キャッシュのリンク状態を表示する方法を強調表示するスクリーンショット。](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    レプリケーション プロセスが完了すると、 **[リンクの状態]** が「**成功**」に変わります。

    ![キャッシュの状態](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    プライマリ リンク キャッシュは、リンク プロセス中も引き続き使用できます。 セカンダリ リンク キャッシュは、リンク プロセスが完了するまで使用できません。

## <a name="remove-a-geo-replication-link"></a>geo レプリケーション リンクの削除

1. 2 つのキャッシュ間のリンクを削除し、geo レプリケーションを停止するには、 **[geo レプリケーション]** ブレードで **[Unlink caches]\(キャッシュのリンク解除\)** をクリックします。
    
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
- [Redis の geo レプリケーションのレプリケーション スケジュールとは何ですか](#what-is-the-replication-schedule-for-redis-geo-replication)
- [geo レプリケーションのレプリケーションにはどのくらいの時間が必要ですか](#how-long-does-geo-replication-replication-take)
- [レプリケーションの回復ポイントは保証されますか](#is-the-replication-recovery-point-guaranteed)
- [PowerShell または Azure CLI を使って geo レプリケーションを管理することはできますか](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure リージョン間でデータをレプリケートするコストはどれくらいですか](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [セカンダリ リンク キャッシュにはどのリージョンを使う必要がありますか](#what-region-should-i-use-for-my-secondary-linked-cache)
- [セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [geo レプリケーションを使用してファイアウォールを構成することはできますか](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Standard または Basic レベル キャッシュで geo レプリケーションを使用することはできますか

geo レプリケーションは、Premium レベルのキャッシュにのみ使用できます。

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>リンクまたはリンク解除のプロセス中にキャッシュを使用できますか

- リンク時、プライマリ リンク キャッシュは、リンク プロセスの完了中も引き続き使用できます。
- リンク時、セカンダリ リンク キャッシュは、リンク プロセスが完了するまで使用できません。
- リンク解除時、リンク解除プロセスの完了中に両方のキャッシュを引き続き使用できます。

### <a name="can-i-link-more-than-two-caches-together"></a>3 つ以上のキャッシュをリンクできますか

いいえ。2 つのキャッシュしかリンクできません。

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>異なる Azure サブスクリプションからの 2 つのキャッシュをリンクすることはできますか

いいえ、両方のキャッシュが同じ Azure サブスクリプションにある必要があります。

### <a name="can-i-link-two-caches-with-different-sizes"></a>サイズが異なる 2 つのキャッシュをリンクすることはできますか

セカンダリ リンク キャッシュがプライマリ リンク キャッシュより大きい場合は可能です。

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>クラスタリングを有効にして geo レプリケーションを使用することはできますか

両方のキャッシュのシャード数が同じ場合は可能です。

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET 内の自分のキャッシュで geo レプリケーションを使用することはできますか

はい。VNET 内のキャッシュの geo レプリケーションは注意事項付きでサポートされています。

- 同じ VNET 内のキャッシュ間の geo レプリケーションがサポートされています。
- 異なる VNET 内のキャッシュ間の geo レプリケーションもサポートされています。
  - VNET が同じリージョンに存在する場合は、[VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)または [VPN Gateway VNET 間接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)を使用してそれらを接続できます。
  - VNET がさまざまなリージョンに存在する場合は、VNET ピアリングを使用した geo レプリケーションがサポートされますが、Basic 内部ロード バランサーの制約のため、VNET 1 (リージョン 1) のクライアント VM で DNS 名を使用して VNET 2 (リージョン 2) のキャッシュにアクセスすることはできなくなります。 VNET ピアリングの制約の詳細については、[Virtual Network - ピアリングの要件と制約](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)に関するページを参照してください。 推奨されるソリューションは、VPN Gateway VNET 間接続の使用です。
  
[この Azure テンプレート](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)を使用すると、2 つの geo レプリケートされたキャッシュを VPN Gateway VNET 間接続で接続された VNET にすばやくデプロイできます。

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redis の geo レプリケーションのレプリケーション スケジュールは何ですか

レプリケーションは継続的かつ非同期であるため、特定のスケジュールでは実行されません。 プライマリに対して実行された書き込みはすべて、セカンダリに瞬時にかつ非同期的にレプリケートされます。

### <a name="how-long-does-geo-replication-replication-take"></a>geo レプリケーションのレプリケーションにはどのくらいの時間が必要ですか

レプリケーションは増分的、非同期、および継続的であるため、かかる時間はリージョン間の待機時間とそれほど変わりません。 特定の状況では、セカンダリ キャッシュがプライマリからのデータの完全な同期を実行することが必要になる場合があります。 この場合のレプリケーション時間は、プライマリ キャッシュへの負荷、使用可能なネットワーク帯域幅、リージョン間の待機時間などのいくつかの要因によって異なります。 53 GB の完全な geo レプリケートされたペアのレプリケーション時間は 5 ～ 10 分であることがわかりました。

### <a name="is-the-replication-recovery-point-guaranteed"></a>レプリケーションの回復ポイントは保証されますか

geo レプリケーション モードのキャッシュの場合、永続化は無効になっています。 geo レプリケートされたペアがリンク解除された場合 (顧客始動のフェールオーバーなど)、セカンダリ リンク キャッシュは同期されたデータをその時点まで保持します。 このような状況では、復旧ポイントは保証されません。

復旧ポイントを取得するには、どちらかのキャッシュから[エクスポート](cache-how-to-import-export-data.md#export)します。 後で、プライマリ リンク キャッシュに[インポート](cache-how-to-import-export-data.md#import)できます。

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell または Azure CLI を使って geo レプリケーションを管理することはできますか

はい。geo レプリケーションは、Azure Portal、PowerShell、または Azure CLI を使用して管理できます。 詳細については、[PowerShell のドキュメント](/powershell/module/az.rediscache/#redis_cache)または [Azure CLI のドキュメント](/cli/azure/redis/server-link)を参照してください。

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure リージョン間でデータをレプリケートするコストはどれくらいですか

geo レプリケーションを使用している場合、プライマリ リンク キャッシュからのデータは、セカンダリ リンク キャッシュにレプリケートされます。 2 つのリンクされたキャッシュが同じリージョンに存在する場合、データ転送の料金は必要ありません。 2 つのリンクされたキャッシュが異なるリージョンに存在する場合、データ転送の料金は、どちらかのリージョン内を移動するデータのネットワーク送信コストです。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>リンクされたキャッシュを削除しようとすると、操作が失敗するのはどうしてですか

geo レプリケートされたキャッシュとそのリソース グループは、geo レプリケーション リンクを削除するまでは削除できません。 リンクされたキャッシュの一方または両方を含むリソース グループを削除しようとすると、リソース グループ内の他のリソースは削除されますが、リソース グループは `deleting` 状態のままとなり、リソース グループ内にあるリンクされたキャッシュは `running` 状態のままとなります。 リソース グループとその中のリンクされたキャッシュを完全に削除するには、「[geo レプリケーション リンクの削除](#remove-a-geo-replication-link)」の説明に従ってキャッシュをリンク解除します。

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>セカンダリ リンク キャッシュにはどのリージョンを使う必要がありますか

一般に、キャッシュは、それにアクセスするアプリケーションと同じ Azure リージョンに配置することをお勧めします。 プライマリおよびフォールバック リージョンが個別に存在するアプリケーションの場合は、プライマリおよびセカンダリ キャッシュをそれらの同じリージョンに配置することをお勧めします。 ペアになっているリージョンについて詳しくは、[ベスト プラクティス - ペアになっている Azure リージョンに関する記事](../best-practices-availability-paired-regions.md)をご覧ください。

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>セカンダリ リンク キャッシュへのフェールオーバーはどのように動作しますか

geo レプリケートされたキャッシュでは、Azure リージョン間の自動フェールオーバーはサポートされていません。 ディザスター リカバリー シナリオでは、顧客がアプリケーション スタック全体をバックアップ リージョンで調整された方法で復旧する必要があります。 バックアップに切り替えるタイミングを個々のアプリケーション コンポーネントに個別に判定させるようにすると、パフォーマンスに悪影響を与える可能性があります。 Redis の主な利点の 1 つは、それが非常に待機時間の短いストアであることです。 顧客の主要なアプリケーションがそのキャッシュとは別のリージョンに存在する場合は、追加されるラウンド トリップ時間がパフォーマンスに大きな影響を与えます。 このため、一時的な可用性の問題から、自動的なフェールオーバーを回避しています。

顧客始動のフェールオーバーを開始するには、まずキャッシュをリンク解除します。 次に、(以前にリンクされた) セカンダリ キャッシュの接続エンドポイントを使用するように Redis クライアントを変更します。 2 つのキャッシュがリンク解除されると、セカンダリ キャッシュが再び通常の読み取り/書き込みキャッシュになり、Redis クライアントから直接要求を受け付けます。

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>geo レプリケーションを使用してファイアウォールを構成することはできますか

はい。geo レプリケーションを使用して、[ファイアウォール](./cache-configure.md#firewall)を構成することができます。 geo レプリケーションをファイアウォールと共に機能させるには、セカンダリ キャッシュの IP アドレスが、プライマリ キャッシュのファイアウォール規則に追加されていることを確認します。

## <a name="next-steps"></a>次の手順

Azure Cache for Redis の機能について

* [Azure Cache for Redis サービス レベル](cache-overview.md#service-tiers)
* [Azure Cache for Redis の高可用性](cache-high-availability.md)
