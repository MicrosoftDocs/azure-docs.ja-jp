---
title: "Azure Redis Cache の構成方法 | Microsoft Docs"
description: "Azure Redis Cache の既定の Redis 構成を理解し、Azure Redis Cache インスタンスの構成方法について説明します。"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 03/27/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: fb9d9d06eb746e720a17d87d7ab45c29c6543e8f
ms.lasthandoff: 03/28/2017


---
# <a name="how-to-configure-azure-redis-cache"></a>Azure Redis Cache の構成方法
このトピックでは、Azure Redis Cache インスタンスの構成を確認し、更新する方法と、Azure Redis Cache インスタンス用の既定の Redis サーバー構成について説明します。

> [!NOTE]
> Premium キャッシュ機能の構成と使用の詳細については、[永続化の構成方法](cache-how-to-premium-persistence.md)、[クラスタリングの構成方法](cache-how-to-premium-clustering.md)、[Virtual Network のサポートの構成方法](cache-how-to-premium-vnet.md)に関する各記事をご覧ください。
> 
> 

## <a name="configure-redis-cache-settings"></a>Redis Cache の設定の構成
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Redis Cache の設定の表示と構成は、**[Redis Cache]** ブレードの **[リソース]** メニューを使って行うことができます。

![Redis Cache の設定](./media/cache-configure/redis-cache-settings.png)

**[リソース]** メニューを使って、以下の設定を表示および構成できます。

* [概要](#overview)
* [アクティビティ ログ](#activity-log)
* [アクセス制御 (IAM)](#access-control-iam)
* [タグ](#tags)
* [問題の診断と解決](#diagnose-and-solve-problems)
* [設定](#settings)
    * [アクセス キー](#access-keys)
    * [詳細設定](#advanced-settings)
    * [Redis Cache Advisor](#redis-cache-advisor)
    * [[価格レベル]](#pricing-tier)
    * [Redis クラスター サイズ](#cluster-size)
    * [Redis データの保持](#redis-data-persistence)
    * [更新のスケジュール](#schedule-updates)
    * [Virtual Network](#virtual-network)
    * [ファイアウォール](#firewall)
    * [プロパティ](#properties)
    * [ロック](#locks)
    * [Automation スクリプト](#automation-script)
* [管理](#administration)
    * [データのインポート](#importexport)
    * [データのエクスポート](#importexport)
    * [Reboot](#reboot)
* [監視](#monitoring)
    * [Redis メトリック](#redis-metrics)
    * [アラート ルール](#alert-rules)
    * [診断](#diagnostics)
* [サポートおよびトラブルシューティング設定](#support-amp-troubleshooting-settings)
    * [リソース ヘルス](#resource-health)
    * [新しいサポート要求](#new-support-request)


## <a name="overview"></a>概要

**[概要]** では、名前、ポート、価格レベル、選ばれたキャッシュ メトリックなど、キャッシュに関する基本的な情報を表示します。

### <a name="activity-log"></a>アクティビティ ログ

キャッシュに対して実行された操作を表示するには、 **[アクティビティ ログ]** をクリックします。 また、フィルター処理を使用すれば、ビューを拡張してその他のリソースを含めることができます。 監査ログの処理の詳細については、[Resource Manager の監査操作](../azure-resource-manager/resource-group-audit.md)に関するページを参照してください。 Azure Redis Cache イベントの監視の詳細については、「 [処理とアラート](cache-how-to-monitor.md#operations-and-alerts)」を参照してください。

### <a name="access-control-iam"></a>アクセス制御 (IAM)

**[アクセス制御 (IAM)]** セクションでは、Azure ポータルでのロールベースのアクセス制御 (RBAC) をサポートしているため、組織はアクセス管理の要件を簡単かつ正確に満たすことができます。 詳細については、 [Azure ポータルでのロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)に関するページをご覧ください。

### <a name="tags"></a>タグ

**[タグ]** セクションでは、リソースを整理できます。 詳細については、 [タグを使用した Azure リソースの整理](../azure-resource-manager/resource-group-using-tags.md)に関するページを参照してください。


### <a name="diagnose-and-solve-problems"></a>問題の診断と解決

一般的な問題と、その問題を解決するための方法を確認するには、 **[問題の診断と解決]** をクリックします。



## <a name="settings"></a>Settings
**[設定]** セクションでは、キャッシュに関する次の設定にアクセスして構成できます。

![Settings](./media/cache-configure/redis-cache-general-settings.png)

* [アクセス キー](#access-keys)
* [詳細設定](#advanced-settings)
* [Redis Cache Advisor](#redis-cache-advisor)
* [[価格レベル]](#pricing-tier)
* [Redis クラスター サイズ](#cluster-size)
* [Redis データの保持](#redis-data-persistence)
* [更新のスケジュール](#schedule-updates)
* [Virtual Network](#virtual-network)
* [ファイアウォール](#firewall)
* [プロパティ](#properties)
* [ロック](#locks)
* [Automation スクリプト](#automation-script)



### <a name="access-keys"></a>アクセス キー
**[アクセス キー]** をクリックすると、キャッシュのアクセス キーを表示したり、再生成したりできます。 これらのキーは、キャッシュに接続するクライアントによって使用されます。

![Redis Cache のアクセス キー](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>詳細設定
次の設定は、 **[詳細設定]** ブレードで構成します。

* [アクセス ポート](#access-ports)
* [maxmemory-policy と maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)
* [キースペース通知 (詳細設定)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>アクセス ポート
新しいキャッシュでは、SSL を使用しないアクセスが既定で無効になっています。 非 SSL ポートを有効にするには、**[詳細設定]** ブレードにある **[SSL によるアクセスのみ許可する]** で **[いいえ]** をクリックしてから、**[保存]** をクリックします。

![Redis Cache のアクセス ポート](./media/cache-configure/redis-cache-access-ports.png)

#### <a name="maxmemory-policy-and-maxmemory-reserved"></a>maxmemory-policy と maxmemory-reserved
**[詳細設定]** ブレードの **[Maxmemory ポリシー]** と **[xmemory-reserved]** の設定によって、キャッシュのメモリ ポリシーが構成されます。 **maxmemory-policy** 設定は、キャッシュの削除ポリシーを構成し、**maxmemory-reserved** は、キャッシュ以外のプロセスのために予約されたメモリを構成します。

![Redis Cache の Maxmemory ポリシー](./media/cache-configure/redis-cache-maxmemory-policy.png)

**[Maxmemory ポリシー]** では、次の削除ポリシーの中から選択できます。

* `volatile-lru` - 既定のプランです。
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

`maxmemory` ポリシーの詳細については、「[Eviction policies (削除ポリシー)](http://redis.io/topics/lru-cache#eviction-policies)」をご覧ください。

**maxmemory-reserved** 設定は、フェールオーバーに伴うレプリケーションなどのキャッシュ以外の操作のために予約されているメモリの量を、MB 単位で構成するものです。 また、断片化率が高い場合も使用できます。 この値を設定すると、負荷が変化するときでも、Redis サーバーの稼働状態がより安定します。 この値は、書き込みが大量に発生するワークロードに対しては、高く設定する必要があります。 メモリがこのような操作のために予約されていると、キャッシュされたデータの保存に使用できなくなります。

> [!IMPORTANT]
> **maxmemory-reserved** 設定は、Standard キャッシュと Premium キャッシュにのみ使用可能です。
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>キースペース通知 (詳細設定)
Redis キースペース通知は、 **[詳細設定]** ブレードで構成します。 キースペース通知により、特定のイベントが発生したときに、クライアントが通知を受信できます。

![Redis Cache の高度な設定](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> キースペース通知と **notify-keyspace-events** 設定は、Standard キャッシュと Premium キャッシュに対してのみ使用可能です。
> 
> 

詳細については、 [Redis キースペース通知](http://redis.io/topics/notifications)に関するトピックを参照してください。 サンプル コードについては、[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルの [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) ファイルを参照してください。


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor
**[Redis Cache Advisor]** ブレードにキャッシュに関する推奨事項が表示されます。 通常の操作中に推奨事項は表示されません。 

![[推奨事項]](./media/cache-configure/redis-cache-no-recommendations.png)

キャッシュの操作中に、高いメモリ使用量、ネットワーク帯域幅、サーバー負荷などの状況が発生した場合は、 **[Redis Cache]** ブレードにアラートが表示されます。

![[推奨事項]](./media/cache-configure/redis-cache-recommendations-alert.png)

詳細情報は、 **[推奨事項]** ブレードで確認できます。

![推奨事項](./media/cache-configure/redis-cache-recommendations.png)

**[Redis Cache]** ブレードの [[Monitoring charts (監視グラフ)]](cache-how-to-monitor.md#monitoring-charts) および [[Usage charts (使用状況グラフ)]](cache-how-to-monitor.md#usage-charts) セクションでは、以下のメトリックを監視できます。

各価格レベルには、クライアント接続、メモリ、および帯域幅についてさまざまな制限があります。 長時間にわたり、キャッシュがこれらのメトリックの最大容量に近づいている場合は、推奨項目が作成されます。 **[推奨事項]** ツールで検証されるメトリックと制限の詳細については、次の表をご覧ください。

| Redis Cache メトリック | 詳細情報 |
| --- | --- |
| ネットワーク帯域幅の使用量 |[キャッシュ パフォーマンス - 使用できる帯域幅](cache-faq.md#cache-performance) |
| 接続されているクライアント数 |[既定の Redis サーバー構成 - maxclients](#maxclients) |
| サーバーの負荷 |[使用状況グラフ -  Redis サーバーの負荷](cache-how-to-monitor.md#usage-charts) |
| メモリ使用量 |[キャッシュのパフォーマンス - サイズ](cache-faq.md#cache-performance) |

キャッシュをアップグレードするには、 **[今すぐアップグレード]** をクリックして、 [価格レベル](#pricing-tier) を変更し、キャッシュのスケーリングを行います。 価格レベルの選択の詳細については、「[Redis Cache のサービス内容と適切なサイズの選択](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)」をご覧ください。


### <a name="pricing-tier"></a>[価格レベル] 
キャッシュの価格レベルを表示または変更するには、 **[価格レベル]** をクリックします。 スケーリングの詳細については、「 [Azure Redis Cache のスケーリング方法](cache-how-to-scale.md)」をご覧ください。

![Redis Cache 価格レベル](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis クラスター サイズ
クラスタリングが有効になっている実行中の Premium キャッシュのクラスター サイズを変更するには、 **[(プレビュー) Redis クラスター サイズ]** をクリックします。

> [!NOTE]
> Azure Redis Cache の Premium レベルは一般公開されていますが、Redis クラスター サイズ機能は現在プレビュー段階であることに注意してください。
> 
> 

![Redis クラスター サイズ](./media/cache-configure/redis-cache-redis-cluster-size.png)

クラスター サイズを変更するには、スライダーを使用するか、**[シャード数]** ボックスに 1 ～ 10 の範囲の数値を入力し、**[OK]** をクリックして保存します。

> [!IMPORTANT]
> Redis クラスタリングは、Premium キャッシュでのみ使用できます。 詳細については、「 [Premium Azure Redis Cache のクラスタリングの構成方法](cache-how-to-premium-clustering.md)」を参照してください。
> 
> 


### <a name="redis-data-persistence"></a>Redis データの保持
**[Redis データ永続化]** をクリックして、Premium Cache のデータ永続化を有効にする、無効にする、または構成することができます。

![Redis データの保持](./media/cache-configure/redis-cache-persistence-settings.png)

Redis の永続化を有効にするには、 **[有効]** をクリックして RDB (Redis データベース) のバックアップを有効にします。 Redis の永続化を無効にするには、 **[無効]**をクリックします。

バックアップの間隔を構成するには、次の **[バックアップの頻度]** ボックスの一覧のうち、いずれかをクリックします。 

- **15 分**
- **30 分**
- **60 分**
- **6 時間**
- **12 時間**
- **24 時間**

前のバックアップ操作が正常に完了すると、バックアップ間隔のカウントダウンが開始され、期間が経過すると新しいバックアップが開始されます。

**[ストレージ アカウント]** をクリックして使用するストレージ アカウントを選択し、**[ストレージ キー]** ボックスの一覧から使用する**プライマリ キー**または**セカンダリ キー**を選択します。 Cache と同じリージョンのストレージ アカウントを選択する必要があり、また、スループットが高いため **Premium Storage** アカウントを使用することをお勧めします。 永続化アカウントのストレージ キーが再生成された場合は常に、**[ストレージ キー]** ボックスの一覧から目的のキーを再選択する必要があります。

**[OK]** をクリックして永続化の構成を保存します。

> [!IMPORTANT]
> Redis のデータ永続化は、Premium Cache でのみ使用できます。 詳細については、「 [Premium Azure Redis Cache の永続化の構成方法](cache-how-to-premium-persistence.md)」を参照してください。
> 
> 

### <a name="schedule-updates"></a>更新のスケジュール
**[更新のスケジュール]** ブレードでは、キャッシュの Redis サーバー更新のメンテナンス時間を指定できます。 

> [!IMPORTANT]
> このメンテナンス時間は、Redis サーバーの更新にのみ適用されます。Azure の更新や、キャッシュをホストする VM のオペレーティング システムへの更新には適用されません。
> 
> 

![更新のスケジュール](./media/cache-configure/redis-schedule-updates.png)

メンテナンス時間を指定するには、目的の曜日をオンにし、曜日ごとにメンテナンス時間の開始時刻を指定して、 **[OK]**をクリックします。 メンテナンス時間の時刻は UTC 時間で指定します。 

> [!IMPORTANT]
> **更新のスケジュール**機能は Premium レベルのキャッシュにのみ使用できます。 詳細および手順については、 [Azure Redis Cache の管理 - 更新のスケジュール](cache-administration.md#schedule-updates)に関するページをご覧ください。
> 
> 



### <a name="virtual-network"></a>Virtual Network
**[Virtual Network]** セクションでは、キャッシュの仮想ネットワークの設定を構成することができます。 VNET サポートでのプレミアム キャッシュの作成およびその設定の更新の詳細については、「 [Premium Azure Redis Cache の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)」をご覧ください。

> [!IMPORTANT]
> 仮想ネットワークの設定は、キャッシュ作成時に VNET サポートで構成されたプレミアム キャッシュでのみ使用できます。 
> 
> 

### <a name="firewall"></a>ファイアウォール

Premium Azure Redis Cache のファイアウォール ルールを表示して構成するには、[**ファイアウォール**] をクリックします。

![ファイアウォール](./media/cache-configure/redis-firewall-rules.png)

IP アドレス範囲の開始アドレスと終了アドレスで、ファイアウォール ルールを指定できます。 ファイアウォール ルールを構成すると、指定した IP アドレス範囲からのクライアント接続のみがキャッシュに接続できます。 ファイアウォール ルールを保存してからルールが有効になるまでに、短い遅延があります。 通常、この遅延は 1 分未満です。

> [!IMPORTANT]
> ファイアウォール ルールが構成されている場合でも、Azure Redis Cache 監視システムからの接続は常に許可されます。
> 
> ファイアウォール ルールは、Premium レベルのキャッシュにのみ使用できます。
> 
> 

### <a name="properties"></a>プロパティ
**[プロパティ]** をクリックすると、キャッシュ エンドポイントやポートなど、キャッシュに関する情報を表示できます。

![Redis Cache のプロパティ](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>ロック
**[ロック]** セクションでは、サブスクリプション、リソース グループ、またはリソースにロックを適用し、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止できます。 詳細については、「 [Azure リソース マネージャーによるリソースのロック](../azure-resource-manager/resource-group-lock-resources.md)」を参照してください。

### <a name="automation-script"></a>Automation スクリプト

**[Automation スクリプト]** をクリックして、将来のデプロイのために、デプロイ済みのリソースのテンプレートをビルドおよびエクスポートします。 テンプレートを操作する方法の詳細については、 [Azure Resource Manager のテンプレートを使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)に関するページをご覧ください。

## <a name="administration-settings"></a>管理の設定
**[管理]** セクションの設定では、Premium キャッシュに対して次の管理タスクを実行できます。 

![管理](./media/cache-configure/redis-cache-administration.png)

* [データのインポート](#importexport)
* [データのエクスポート](#importexport)
* [Reboot](#reboot)


> [!IMPORTANT]
> このセクションの設定は、Premium レベルのキャッシュにのみ使用できます。
> 
> 

### <a name="importexport"></a>Import/Export
Import/Export は Azure Redis Cache のデータ管理操作です。Redis Cache データベース (RDB) のスナップショットを Premium キャッシュからエクスポートし、Azure ストレージ アカウント内のページ BLOB にインポートすることで、キャッシュ内のデータのインポートとエクスポートを実行できます。 Import/Export により、異なる Azure Redis Cache インスタンス間での移行または使用前のキャッシュへのデータ入力が可能になります。

Import は、任意のクラウドまたは環境で稼働している任意の Redis サーバー (Linux や Windows のほか、アマゾン ウェブ サービスをはじめとする各種クラウド プロバイダーで稼働している Redis など) から Redis と互換性のある RDB ファイルを取り込むときに使用できます。 データをインポートすると、あらかじめデータが入力されたキャッシュを簡単に作成できます。 インポート処理中に、Azure Redis Cache は RDB ファイルを Azure ストレージからメモリに読み込み、キーをキャッシュに挿入します。

Export では、Azure Redis Cache に格納されたデータを、Redis と互換性のある RDB ファイルにエクスポートできます。 この機能を使えば、Azure Redis Cache インスタンス間でデータを移動したり、Azure Redis Cache インスタンスから別の Redis サーバーにデータを移動したりできます。 エクスポート処理中に、Azure Redis Cache サーバー インスタンスをホストする VM 上に一時ファイルが作成され、それが指定されたストレージ アカウントにアップロードされます。 エクスポート操作が完了したら、操作の成否にかかわらず、この一時ファイルは削除されます。

> [!IMPORTANT]
> Import/Export は、Premium レベル キャッシュにのみ使用可能です。 詳細および手順については、「 [Azure Redis Cache でデータをインポートまたはエクスポートする](cache-how-to-import-export-data.md)」をご覧ください。
> 
> 

### <a name="reboot"></a>Reboot
**[再起動]** ブレードでは、キャッシュのノードを再起動できます。 この再起動機能により、アプリケーションにキャッシュ ノードの障害が発生した場合の復元性をテストすることができます。

![Reboot](./media/cache-configure/redis-cache-reboot.png)

クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するキャッシュのシャードを選択できます。

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

キャッシュのノードを再起動するには、目的のノードを選択し、 **[再起動]**をクリックします。 クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するシャードを選択し、 **[再起動]**をクリックします。 数分後、選択したノードが再起動され、さらに数分後にオンラインに戻ります。

> [!IMPORTANT]
> 再起動は、Premium レベルのキャッシュにのみ使用できます。 詳細および手順については、 [Azure Redis Cache の管理 - 再起動](cache-administration.md#reboot)に関するページをご覧ください。
> 
> 


## <a name="monitoring"></a>監視

**[監視]** セクションでは、Redis Cache に対する診断および監視を構成できます。 Azure Redis Cache 監視および診断の詳細については、「[Azure Redis Cache の監視方法](cache-how-to-monitor.md)」をご覧ください。

![診断](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis メトリック](#redis-metrics)
* [アラート ルール](#alert-rules)
* [診断](#diagnostics)

### <a name="redis-metrics"></a>[Redis メトリック]
**[Redis メトリック]** をクリックしてキャッシュの[メトリックを表示](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts)します。

### <a name="alert-rules"></a>アラート ルール

**[アラート ルール]** をクリックして、Redis Cache のメトリックに基づいてアラートを構成します。 詳細については、「[処理とアラート](cache-how-to-monitor.md#operations-and-alerts)」を参照してください。

### <a name="diagnostics"></a>診断

**[診断]** をクリックすると、キャッシュ診断の格納に使用する [ストレージ アカウントを構成](cache-how-to-monitor.md#enable-cache-diagnostics) できます。

![Redis Cache の診断](./media/cache-configure/redis-cache-diagnostics-settings.png)

## <a name="support--troubleshooting-settings"></a>サポートおよびトラブルシューティング設定
**[サポート + トラブルシューティング]** セクションでは、キャッシュの問題を解決するためのオプションを設定できます。

![[サポート + トラブルシューティング]](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [リソース ヘルス](#resource-health)
* [新しいサポート要求](#new-support-request)

### <a name="resource-health"></a>リソース ヘルス
**[リソース正常性]** ではリソースが監視され、そのリソースが意図したとおりに動いているかどうかが示されます。 Azure Resource Health サービスの詳細については、「 [Azure Resource Health の概要](../resource-health/resource-health-overview.md)」を参照してください。

> [!NOTE]
> 現在、[リソース正常性] では、仮想ネットワークでホストされている Azure Redis Cache インスタンスの正常性については報告できません。 詳細については、「 [VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>新しいサポート要求
**[新しいサポート要求]** をクリックして、キャッシュのサポート要求を開きます。





## <a name="default-redis-server-configuration"></a>既定の Redis サーバー構成
新しい Azure Redis Cache インスタンスに適用される既定の Redis 構成値は、次のとおりです。

> [!NOTE]
> `StackExchange.Redis.IServer.ConfigSet` メソッドを使用してこのセクションの設定を変更することはできません。 このセクションのいずれかのコマンドでこのメソッドを呼び出すと、次のような例外がスローされます。  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> **max-memory-policy** などの構成可能な値を、Azure Portal またはコマンド ライン管理ツール (Azure CLI、PowerShell など) を使用して構成できます。
> 
> 

| Setting | 既定値 | Description |
| --- | --- | --- |
| `databases` |16 |データベースの既定の数は 16 ですが、価格レベルに基づいてさまざまな数を構成できます。<sup>1</sup> 既定のデータベースは DB 0 です。`dbid` が `0` ～ `databases - 1` の間の数値である `connection.GetDatabase(dbid)` を使用して、接続ごとに異なるデータベースを選択できます。 |
| `maxclients` |価格レベルによって異なります。<sup>2</sup> |これは、同時に接続が許可されているクライアントの最大数です。 制限に達すると、Redis はすべての新しい接続を終了し、"max number of clients reached" エラーを返します。 |
| `maxmemory-policy` |`volatile-lru` |Maxmemory ポリシーとは、`maxmemory` (キャッシュ作成時に選択したキャッシュのサイズ) に達したときに、Redis が削除する項目を選択する方法についての設定です。 Azure Redis Cache の既定の設定は `volatile-lru` で、LRU アルゴリズムを使用して有効期限が設定されたキーを削除します。 この設定は、Azure ポータルで構成できます。 詳細については、「 [maxmemory-policy と maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)」を参照してください。 |
| `maxmemory-sample`s |3 |LRU アルゴリズムと最小 TTL アルゴリズムは精緻なアルゴリズムではなく、メモリを節約するための近似アルゴリズムです。 既定では、Redis はキーを 3 つ確認し、直近の使用頻度が比較的低いものを 1 つ選択します。 |
| `lua-time-limit` |5,000 |Lua スクリプトの最大実行時間 (ミリ秒)。 最大実行時間に達した場合は、Redis は、最大許容時間の後もスクリプトが実行中であることをログに記録し、クエリに対してエラーを知らせる応答を開始します。 |
| `lua-event-limit` |500 |スクリプト イベント キューの最大サイズです。 |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |このクライアントの出力バッファー制限を使用して、なんらかの理由 (一般的には、パブリッシャーがメッセージを作成するのと同じ速度で Pub/Sub クライアントがメッセージを利用できないという理由) により、サーバーから十分な速度でデータを読み込んでいないクライアントを強制的に切断することができます。 詳細については、 [http://redis.io/topics/clients](http://redis.io/topics/clients)を参照してください。 |

<a name="databases"></a>
<sup>1</sup>`databases` の制限は、Azure Redis Cache の価格レベルによって異なり、キャッシュの作成時に設定できます。 キャッシュの作成中に `databases` を設定しない場合は、既定値の 16 が使用されます。

* Basic キャッシュおよび Standard キャッシュ
  * C0 (250 MB) キャッシュ - 最大 16 のデータベース
  * C1 (1 GB) キャッシュ - 最大 16 のデータベース
  * C2 (2.5 GB) キャッシュ - 最大 16 のデータベース
  * C3 (6 GB) キャッシュ - 最大 16 のデータベース
  * C4 (13 GB) キャッシュ - 最大 32 のデータベース
  * C5 (26 GB) キャッシュ - 最大 48 のデータベース
  * C6 (53 GB) キャッシュ - 最大 64 のデータベース
* Premium キャッシュ
  * P1 (6 GB ～ 60 GB) - 最大 16 のデータベース
  * P2 (13 GB ～ 130 GB) - 最大 32 のデータベース
  * P3 (26 GB ～ 260 GB) - 最大 48 のデータベース
  * P4 (53 GB ～ 530 GB) - 最大 64 のデータベース
  * Redis クラスターが有効なすべての Premium キャッシュ - Redis クラスターは、データベース 0 の使用のみをサポートするため、Redis クラスターが有効な Premium キャッシュの `databases` の制限は、実質的に 1 で、 [Select](http://redis.io/commands/select) コマンドは使用できません。 詳細については、「 [クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

データベースの詳細については、「[What are Redis databases? (Redis データベースとは)](cache-faq.md#what-are-redis-databases)」を参照してください

> [!NOTE]
> `databases` の設定は、キャッシュの作成中にのみ構成できます。また、PowerShell、CLI、その他の管理クライアントを使用する必要があります。 PowerShell を使用して、キャッシュの作成中に `databases` を構成する例については、[New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases) に関するページをご覧ください。
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients` は、Azure Redis Cache の価格レベルによって異なります。

* Basic キャッシュおよび Standard キャッシュ
  * C0 (250 MB) キャッシュ - 最大 256 接続
  * C1 (1 GB) キャッシュ - 最大 1,000 接続
  * C2 (2.5 GB) キャッシュ - 最大 2,000 接続
  * C3 (6 GB) キャッシュ - 最大 5,000 接続
  * C4 (13 GB) キャッシュ - 最大 10,000 接続
  * C5 (26 GB) キャッシュ - 最大 15,000 接続
  * C6 (53 GB) キャッシュ - 最大 20,000 接続
* Premium キャッシュ
  * P1 (6 ～ 60 GB) - 最大 7,500 接続
  * P2 (13 ～ 130 GB) - 最大 15,000 接続
  * P3 (26 ～ 260 GB) - 最大 30,000 接続
  * P4 (53 ～ 530 GB) - 最大 40,000 接続

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Azure Redis Cache でサポートされない Redis コマンド
> [!IMPORTANT]
> Azure Redis Cache インスタンスの構成と管理は Microsoft によって管理されるため、次のコマンドは無効です。 これらのコマンドを呼び出そうとすると、`"(error) ERR unknown command"` のようなエラー メッセージが送信されます。
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * [保存]
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER - クラスターの書き込みコマンドは無効ですが、読み取り専用のクラスター コマンドは許可されます。
> 
> 

Redis コマンドの詳細については、 [http://redis.io/commands](http://redis.io/commands)を参照してください。

## <a name="redis-console"></a>Redis コンソール
**Redis コンソール**を使用して Azure Redis Cache インスタンスにコマンドを安全に発行できます。このコンソールは Standard キャッシュと Premium キャッシュに対して使用できます。

> [!IMPORTANT]
> Redis コンソールは、VNET、クラスタリング、0 以外のデータベースでは機能しません。 
> 
> * [VNET](cache-how-to-premium-vnet.md) : キャッシュが VNET の一部である場合は、VNET のクライアントだけがキャッシュにアクセスできます。 Redis コンソールは、VNET の一部ではない VM でホストされている redis-cli.exe クライアントを使用するため、キャッシュに接続できません。
> * [クラスタリング](cache-how-to-premium-clustering.md): Redis コンソールは、現時点ではクラスタリングをサポートしていない redis-cli.exe クライアントを使用します。 GitHub で Redis リポジトリの[不安定な](http://redis.io/download)ブランチにある redis-cli ユーティリティは、`-c` スイッチ付きで起動した場合、基本的なサポートを実装しています。 詳細については、[http://redis.io](http://redis.io) の「[Redis cluster tutorial (Redis クラスター チュートリアル)](http://redis.io/topics/cluster-tutorial)」で「[Playing with the cluster (クラスターの使用)](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster)」をご覧ください。
> * Redis コンソールは、コマンドを送信するたびに、データベース 0 への新しい接続を作成します。 データベースは各コマンドで 0 にリセットされるため、 `SELECT` コマンドを使用して別のデータベースを選択することはできません。 別のデータベースへの変更など、Redis コマンドの実行については、「 [Redis コマンドの実行方法](cache-faq.md#how-can-i-run-redis-commands)
> 
> 

Redis コンソールにアクセスするには、**[Redis Cache]** ブレードの **[コンソール]** をクリックします。

![Redis コンソール](./media/cache-configure/redis-console-menu.png)

キャッシュ インスタンスに対してコマンドを発行するには、目的のコマンドをコンソールに入力します。

![Redis コンソール](./media/cache-configure/redis-console.png)

Azure Redis Cache で無効な Redis コマンドの一覧については、前の「 [Azure Redis Cache でサポートされない Redis コマンド](#redis-commands-not-supported-in-azure-redis-cache) 」セクションを参照してください。 Redis コマンドの詳細については、 [http://redis.io/commands](http://redis.io/commands)を参照してください。 

## <a name="move-your-cache-to-a-new-subscription"></a>新しいサブスクリプションへのキャッシュの移動
新しいサブスクリプションにキャッシュを移動するには、 **[移動]**をクリックします。

![Redis Cache の移動](./media/cache-configure/redis-cache-move.png)

リソース グループ間、およびサブスクリプション間でのリソースの移動については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* Redis コマンドの使用の詳細については、「[How can I run Redis commands? (Redis コマンドの実行方法)](cache-faq.md#how-can-i-run-redis-commands)」をご覧ください。


