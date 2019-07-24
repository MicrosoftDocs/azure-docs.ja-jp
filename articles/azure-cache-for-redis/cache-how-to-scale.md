---
title: Azure Cache for Redis をスケーリングする方法 | Microsoft Docs
description: Azure Cache for Redis インスタンスをスケーリングする方法学習する
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 350db214-3b7c-4877-bd43-fef6df2db96c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: yegu
ms.openlocfilehash: 495fc031150d04f253279606baebb5d64d52bce7
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313434"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Azure Cache for Redis のスケーリング方法
Azure Cache for Redis には、キャッシュ サイズや機能の選択に柔軟性を持たせるために、さまざまなキャッシュ オファリングが用意されています。 キャッシュを作成した後でご利用のアプリケーションの要件が変わった場合、キャッシュのサイズと価格レベルをスケーリングできます。 この記事では、Azure Portal と、Azure PowerShell や Azure CLI などのツールを使用して、キャッシュをスケーリングする方法を説明します。

## <a name="when-to-scale"></a>スケーリングするタイミング
Azure Cache for Redis の [監視](cache-how-to-monitor.md)機能を使用して、お使いのキャッシュの正常性とパフォーマンスを監視し、キャッシュのスケーリングが必要であるかどうかを判断できます。 

次のメトリックを監視して、スケーリングの必要性の判断に役立てることができます。

* Redis サーバーの負荷
* メモリ使用量
* ネットワーク帯域幅
* CPU 使用率

キャッシュがアプリケーションの要件を満たさなくなったと判断した場合は、ご利用のアプリケーションにとって適切な価格レベルのキャッシュにスケーリングできます。 使用するキャッシュの価格レベルを決定する方法の詳細については、「[What Azure Cache for Redis offering and size should I use](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)」 (Azure Cache for Redis のサービス内容と適切なサイズの選択) をご覧ください。

## <a name="scale-a-cache"></a>キャッシュのスケーリング
キャッシュをスケーリングするには、[Azure Portal](https://portal.azure.com) で[キャッシュを参照](cache-configure.md#configure-azure-cache-for-redis-settings)し、**[リソース] メニュー**の **[スケール]** をクリックします。

![スケール](./media/cache-how-to-scale/redis-cache-scale-menu.png)

**[価格レベルの選択]** ブレードから希望の価格レベルを選択し、**[選択]** をクリックします。

![価格レベル ][redis-cache-pricing-tier-blade]


別の価格レベルにスケーリングできますが、次のような制約があります。

* 上位の価格レベルから下位の価格レベルにスケーリングすることはできません。
  * **Premium** キャッシュから **Standard** または **Basic** キャッシュにスケールすることはできません。
  * **Standard** キャッシュから **Basic** キャッシュにスケールすることはできません。
* **Basic** キャッシュから **Standard** キャッシュにスケールすることはできますが、同時にサイズを変更することはできません。 サイズを変更する必要がある場合、後続のスケーリング操作でサイズを変更できます。
* **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、その後の操作で **Standard** から **Premium** にスケーリングします。
* **C0 (250 MB)** サイズにそれより大きなサイズからスケールダウンすることはできません。
 
キャッシュを新しい価格レベルにスケーリングするときに、**[Azure Cache for Redis]** ブレードで状態が **[拡大中]** と表示されます。

![スケーリング][redis-cache-scaling]

スケーリングが完了すると、状態が **[拡大中]** から **[実行中]** に変わります。

## <a name="how-to-automate-a-scaling-operation"></a>スケーリング処理を自動化する方法
Azure Portal でキャッシュ インスタンスをスケーリングするほか、PowerShell コマンドレット、Azure CLI、および Microsoft Azure 管理ライブラリ (MAML) を使用してスケーリングすることができます。 

* [PowerShell を使用したスケーリング](#scale-using-powershell)
* [Azure CLI を使用したスケーリング](#scale-using-azure-cli)
* [MAML を使用したスケーリング](#scale-using-maml)

### <a name="scale-using-powershell"></a>PowerShell を使用したスケーリング

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell を使用して Azure Cache for Redis インスタンスをスケーリングするには、`Size`、`Sku`、または `ShardCount` プロパティを変更するときに [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) コマンドレットを使用します。 次の例は、 `myCache` という名前のキャッシュを 2.5 GB のキャッシュにスケーリングする方法を示しています。 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

PowerShell によるスケーリングの詳細については、[PowerShell を使用した Azure Cache for Redis のスケーリングに関するページ](cache-howto-manage-redis-cache-powershell.md#scale)をご覧ください。

### <a name="scale-using-azure-cli"></a>Azure CLI を使用したスケーリング
Azure CLI を使用して Azure Cache for Redis インスタンスをスケーリングするには、`azure rediscache set` コマンドを呼び出し、必要なスケーリング操作に基づいて、新しいサイズ、sku、またはクラスター サイズを含む必要な構成変更を指定します。

Azure CLI によるスケーリングの詳細については、「[Change settings of an existing Azure Cache for Redis](cache-manage-cli.md#scale)」 (既存の Azure Cache for Redis の設定を変更する) をご覧ください。

### <a name="scale-using-maml"></a>MAML を使用したスケーリング
[Microsoft Azure 管理ライブラリ (MAML)](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/) を使用して Azure Cache for Redis インスタンスをスケーリングするには、`IRedisOperations.CreateOrUpdate` メソッドを呼び出して `RedisProperties.SKU.Capacity` に新しいサイズを指定します。

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

詳細については、[MAML を使用した Azure Cache for Redis の管理に関するページ](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML)のサンプルをご覧ください。

## <a name="scaling-faq"></a>スケーリングに関する FAQ
次の一覧は、Azure Cache for Redis のスケーリングに関するよく寄せられる質問への回答です。

* [Premium キャッシュへのスケーリング、Premium キャッシュからのスケーリング、または Premium キャッシュ内でのスケーリングは可能ですか](#can-i-scale-to-from-or-within-a-premium-cache)
* [スケーリング後にキャッシュ名やアクセス キーを変更する必要はありますか](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [スケーリングはどのように処理されますか](#how-does-scaling-work)
* [スケーリング中にキャッシュからデータは失われますか](#will-i-lose-data-from-my-cache-during-scaling)
* [スケーリング中に影響を受けるカスタム データベース](#is-my-custom-databases-setting-affected-during-scaling)
* [スケーリング中にキャッシュを使用できますか](#will-my-cache-be-available-during-scaling)
* geo レプリケーションを構成していますが、キャッシュをスケーリングしたり、クラスターのシャードを変更したりできません。
* [サポートされていない処理](#operations-that-are-not-supported)
* [スケーリングにはどのくらいの時間がかかりますか](#how-long-does-scaling-take)
* [スケーリングが完了したことをどのようにして確認できますか](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Premium キャッシュへのスケーリング、Premium キャッシュからのスケーリング、または Premium キャッシュ内でのスケーリングは可能ですか
* **Premium** キャッシュから **Basic** または **Standard** の価格レベルにスケーリングすることはできません。
* ある **Premium** キャッシュの価格レベルから別の Premium キャッシュの価格レベルにスケーリングすることはできます。
* **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、その後の操作で **Standard** から **Premium** にスケーリングします。
* **Premium** キャッシュを作成しているときにクラスタリングを有効にした場合、 [クラスター サイズを変更](cache-how-to-premium-clustering.md#cluster-size)できます。 クラスタリングを有効にせずに作成されたキャッシュの場合、後でクラスタリングを構成できます。
  
  詳細については、「[How to configure clustering for a Premium Azure Cache for Redis](cache-how-to-premium-clustering.md)」 (Premium Azure Cache for Redis のクラスタリングの構成方法) を参照してください。

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>スケーリング後にキャッシュ名やアクセス キーを変更する必要はありますか
いいえ、スケーリング処理中にキャッシュ名やキーが変更されることはありません。

### <a name="how-does-scaling-work"></a>スケーリングはどのように処理されますか
* **Basic** キャッシュを別のサイズにスケーリングすると、キャッシュはシャット ダウンされて新しいキャッシュが新しいサイズでプロビジョニングされます。 この間キャッシュは使用できず、キャッシュ内のすべてのデータは失われます。
* **Basic** キャッシュを **Standard** キャッシュにスケーリングすると、レプリカ キャッシュがプロビジョニングされ、データがプライマリ キャッシュからレプリカ キャッシュにコピーされます。 スケーリングの処理中、キャッシュは引き続き使用できます。
* **Standard** キャッシュを別のサイズまたは **Premium** キャッシュにスケーリングすると、一方のレプリカはシャットダウンし、新しいサイズに再プロビジョニングされてデータが転送されます。もう一方のレプリカは、キャッシュ ノードの 1 つでエラーが発生したときに実行される処理と同様に、フェールオーバーを実行してから、再プロビジョニングされます。

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>スケーリング中にキャッシュからデータは失われますか
* 新しいサイズに **Basic** キャッシュをスケーリングすると、すべてのデータが失われ、スケーリング処理中にキャッシュは使用できなくなります。
* **Basic** キャッシュを **Standard** キャッシュにスケーリングする場合、通常、キャッシュのデータは保存されます。
* **Standard** キャッシュをより大きいサイズまたは価格レベルにスケールアップする場合や、**Premium** キャッシュをより大きいサイズにスケールアップする場合、通常はすべてのデータが保持されます。 **Standard** または **Premium** キャッシュをより小さいサイズにスケールダウンする場合、キャッシュ内のデータ量と新しいサイズの関係によっては、スケーリング時にデータが失われる可能性があります。 スケールダウンのときにデータが失われた場合、 [allkeys-lru](https://redis.io/topics/lru-cache) 削除ポリシーを使用してキーが削除されます。 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>スケーリング中に影響を受けるカスタム データベース
キャッシュの作成中に `databases` の設定のカスタム値を構成した場合、価格レベルによってさまざまな[データベース制限](cache-configure.md#databases)があることに注意してください。 このシナリオでスケーリングを行う場合は、次の点を考慮します。

* 現在のレベルより低い `databases` の制限で価格レベルをスケーリングする場合:
  * すべての価格レベルが 16 の `databases` の既定の数を使っている場合、データは失われません。
  * スケーリングしているレベルの制限範囲に含まれるユーザー設定の数値の `databases` を使用している場合、この `databases` の設定は保持され、データは失われません。
  * 新しいレベルの制限を超えるユーザー設定の数値の `databases` を使用している場合、`databases` の設定は新しいレベルの制限より低くなり、削除されたデータベースのすべてのデータが失われます。
* 現在のレベル以上の `databases` の制限を持つ価格レベルにスケーリングする場合、`databases` の設定は保持され、データは失われません。

Standard および Premium キャッシュには可用性について 99.9% の SLA がある一方で、データの喪失については SLA がありません。

### <a name="will-my-cache-be-available-during-scaling"></a>スケーリング中にキャッシュを使用できますか
* **Standard** キャッシュと **Premium** キャッシュはスケーリング処理中でも使用できます。 ただし、Standard および Premium キャッシュのスケーリングや、Basic から Standard キャッシュへのスケーリングの際に接続の中断が発生する可能性があります。 こうした接続の中断はわずかで、Redis クライアントはすぐに接続を再確立できます。
* 別のサイズにスケーリングする場合、**Basic** キャッシュはオフラインになります。 **Basic** から **Standard** にスケーリングする際は、Basic キャッシュを引き続き利用できますが、接続の中断がわずかに発生する可能性があります。 接続の中断が発生しても、Redis クライアントはすぐに接続を再確立できます。


### <a name="scaling-limitations-with-geo-replication"></a>geo レプリケーションのスケーリング制限

2 つのキャッシュ間で geo レプリケーションを追加すると、スケーリング操作を監視したり、クラスターのシャード数を変更したりできなくなります。 これらのコマンドを発行するには、キャッシュのリンクを解除する必要があります。 詳細については、「[geo レプリケーションの構成](cache-how-to-geo-replication.md)」を参照してください。


### <a name="operations-that-are-not-supported"></a>サポートされていない処理
* 上位の価格レベルから下位の価格レベルにスケーリングすることはできません。
  * **Premium** キャッシュから **Standard** または **Basic** キャッシュにスケールすることはできません。
  * **Standard** キャッシュから **Basic** キャッシュにスケールすることはできません。
* **Basic** キャッシュから **Standard** キャッシュにスケールすることはできますが、同時にサイズを変更することはできません。 サイズを変更する必要がある場合、後続のスケーリング操作でサイズを変更できます。
* **Basic** キャッシュから直接 **Premium** キャッシュにスケールすることはできません。 まず、1 回のスケーリング操作で **Basic** から **Standard** にスケーリングし、その後の操作で **Standard** から **Premium** にスケーリングします。
* **C0 (250 MB)** サイズにそれより大きなサイズからスケールダウンすることはできません。

スケール処理でエラーが発生すると、サービスは処理を取り消してキャッシュを元のサイズに戻すように試行します。


### <a name="how-long-does-scaling-take"></a>スケーリングにはどのくらいの時間がかかりますか
スケーリングには約 20 分かかりますが、キャッシュ内のデータ量によって変わります。

### <a name="how-can-i-tell-when-scaling-is-complete"></a>スケーリングが完了したことをどのようにして確認できますか
スケール処理の進捗は Azure Portal で確認できます。 スケーリングが完了すると、キャッシュの状態が **[実行中]** に変わります。

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png



