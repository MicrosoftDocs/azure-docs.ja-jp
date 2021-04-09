---
title: Azure Cache for Redis の構成方法
description: Azure Cache for Redis の既定の Redis 構成について説明し、Azure Cache for Redis インスタンスの構成方法について説明します
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: ba021d280f71504ad5c1e1aea0dc1c8c6b507b1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581076"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Azure Cache for Redis の構成方法
このトピックでは、Azure Cache for Redis インスタンスで利用可能な構成について説明します。 このトピックでは、Azure Cache for Redis インスタンスの既定の Redis サーバー構成についても説明します。

> [!NOTE]
> Premium キャッシュ機能の構成と使用の詳細については、[永続化の構成方法](cache-how-to-premium-persistence.md)、[クラスタリングの構成方法](cache-how-to-premium-clustering.md)、[Virtual Network のサポートの構成方法](cache-how-to-premium-vnet.md)に関する各記事をご覧ください。
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Azure Cache for Redis の設定を構成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis の設定の表示と構成は、 **[Azure Cache for Redis]** ブレードの **[リソース]** メニューを使って行うことができます。

![Azure Cache for Redis の設定](./media/cache-configure/redis-cache-settings.png)

**[リソース]** メニューを使って、以下の設定を表示および構成できます。

* [概要](#overview)
* [アクティビティ ログ](#activity-log)
* [アクセス制御 (IAM)](#access-control-iam)
* [タグ](#tags)
* [問題の診断と解決](#diagnose-and-solve-problems)
* [設定](#settings)
    * [アクセス キー](#access-keys)
    * [詳細設定](#advanced-settings)
    * [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
    * [スケール](#scale)
    * [クラスター サイズ](#cluster-size)
    * [データの永続化](#redis-data-persistence)
    * [更新のスケジュール](#schedule-updates)
    * [geo レプリケーション](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [ファイアウォール](#firewall)
    * [Properties](#properties)
    * [Locks](#locks)
    * [Automation スクリプト](#automation-script)
* 管理
    * [データのインポート](#importexport)
    * [データのエクスポート](#importexport)
    * [Reboot](#reboot)
* [Monitoring](#monitoring)
    * [Redis メトリック](#redis-metrics)
    * [アラート ルール](#alert-rules)
    * [診断](#diagnostics)
* サポートおよびトラブルシューティング設定
    * [リソース ヘルス](#resource-health)
    * [新しいサポート要求](#new-support-request)


## <a name="overview"></a>概要

**[概要]** では、名前、ポート、価格レベル、選ばれたキャッシュ メトリックなど、キャッシュに関する基本的な情報を表示します。

### <a name="activity-log"></a>アクティビティ ログ

キャッシュに対して実行された操作を表示するには、 **[アクティビティ ログ]** をクリックします。 また、フィルター処理を使用すれば、ビューを拡張してその他のリソースを含めることができます。 監査ログの処理の詳細については、[Resource Manager の監査操作](../azure-resource-manager/management/view-activity-logs.md)に関するページを参照してください。 Azure Cache for Redis イベントの監視の詳細については、[操作とアラート](cache-how-to-monitor.md#operations-and-alerts)についてのページを参照してください。

### <a name="access-control-iam"></a>アクセス制御 (IAM)

**[アクセス制御 (IAM)]** セクションでは、Azure portal での Azure ロールベースのアクセス制御 (Azure RBAC) のサポートが提供されます。 この構成により、組織は簡単かつ正確にアクセス管理要件を満たすことができます。 詳細については、[Azure portal での Azure ロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)に関するページをご覧ください。

### <a name="tags"></a>Tags

**[タグ]** セクションでは、リソースを整理できます。 詳細については、 [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)に関するページを参照してください。


### <a name="diagnose-and-solve-problems"></a>問題の診断と解決

一般的な問題と、その問題を解決するための方法を確認するには、 **[問題の診断と解決]** をクリックします。



## <a name="settings"></a>設定
**[設定]** セクションでは、キャッシュに関する次の設定にアクセスして構成できます。

* [アクセス キー](#access-keys)
* [詳細設定](#advanced-settings)
* [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
* [スケール](#scale)
* [クラスター サイズ](#cluster-size)
* [データの永続化](#redis-data-persistence)
* [更新のスケジュール](#schedule-updates)
* [geo レプリケーション](#geo-replication)
* [Virtual Network](#virtual-network)
* [ファイアウォール](#firewall)
* [Properties](#properties)
* [Locks](#locks)
* [Automation スクリプト](#automation-script)



### <a name="access-keys"></a>アクセス キー
**[アクセス キー]** をクリックすると、キャッシュのアクセス キーを表示したり、再生成したりできます。 これらのキーは、キャッシュに接続するクライアントによって使用されます。

![Azure Cache for Redis のアクセス キー](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>詳細設定
次の設定は、 **[詳細設定]** ブレードで構成します。

* [アクセス ポート](#access-ports)
* [メモリ ポリシー](#memory-policies)
* [キースペース通知 (詳細設定)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>アクセス ポート
新しいキャッシュでは、TLS/SSL を使用しないアクセスが既定で無効になっています。 非 TLS ポートを有効にするには、 **[詳細設定]** ブレードにある **[SSL によるアクセスのみ許可する]** で **[いいえ]** をクリックしてから、 **[保存]** をクリックします。

> [!NOTE]
> Azure Cache for Redis に対する TLS アクセスでは現在、TLS 1.0、1.1、および 1.2 がサポートされていますが、バージョン 1.0 および 1.1 はまもなく廃止されます。  詳細については、[TLS 1.0 および 1.1 の削除に関するページ](cache-remove-tls-10-11.md)を参照してください。

![Azure Cache for Redis のアクセス ポート](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>メモリ ポリシー
**[詳細設定]** ブレードの **[Maxmemory ポリシー]** **[maxmemory-reserved]** **[maxfragmentationmemory-reserved]** の設定によって、キャッシュのメモリ ポリシーが構成されます。

![Azure Cache for Redis の Maxmemory ポリシー](./media/cache-configure/redis-cache-maxmemory-policy.png)

**[Maxmemory ポリシー]** ではキャッシュの削除ポリシーが構成されます。次の削除ポリシーから選択できます。

* `volatile-lru` -これは、既定の削除ポリシーです。
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

`maxmemory` ポリシーの詳細については、「[Eviction policies (削除ポリシー)](https://redis.io/topics/lru-cache#eviction-policies)」をご覧ください。

**maxmemory-reserved** 設定は、フェールオーバーに伴うレプリケーションなどのキャッシュ以外の操作のために予約されているメモリの量をクラスター内のインスタンスあたりの MB 単位で構成するものです。 この値を設定すると、負荷が変化するときでも、Redis サーバーの稼働状態がより安定します。 この値は、書き込みが大量に発生するワークロードに対しては、高く設定する必要があります。 メモリがこのような操作のために予約されていると、キャッシュされたデータの保存に使用できなくなります。

**maxfragmentationmemory-reserved** 設定では、メモリの断片化に対応するために予約されるメモリ量をクラスター内のインスタンスあたりの MB 単位で構成します。 この値を設定すると、キャッシュがいっぱいになった場合や、キャッシュがほとんどいっぱいで断片化の割合が高い場合でも、Redis サーバーの動作がより安定します。 メモリがこのような操作のために予約されていると、キャッシュされたデータの保存に使用できなくなります。

新しいメモリ予約値 (**maxmemory-reserved** または **maxfragmentationmemory-reserved**) を選択する際には、この変更によってすでに大量のデータが入っているキャッシュがどのような影響を受けるのかを考慮する必要があります。 たとえば、53 GB のキャッシュに 49 GB のデータが入っているときに、予約値を 8 GB に変更した場合、この変更によりシステムで利用可能な最大メモリは 45 GB まで低下します。 現在の `used_memory` または `used_memory_rss` が新しい上限値の 45 GB よりも大きい場合、システムでは、`used_memory` と `used_memory_rss` の両方が 45 GB を下回るまでデータを削除しなければならなくなります。 削除することによってサーバーの負荷やメモリの断片化が増える可能性もあります。 `used_memory` や `used_memory_rss` などのキャッシュに関するメトリックの詳細については、「[使用可能なメトリックとレポート期間](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)」をご覧ください。

> [!IMPORTANT]
> **maxmemory-reserved** と **maxfragmentationmemory-reserved** 設定は、Standard キャッシュと Premium キャッシュにのみ使用可能です。
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>キースペース通知 (詳細設定)
Redis キースペース通知は、 **[詳細設定]** ブレードで構成します。 キースペース通知により、特定のイベントが発生したときに、クライアントが通知を受信できます。

![Azure Cache for Redis の詳細設定](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> キースペース通知と **notify-keyspace-events** 設定は、Standard キャッシュと Premium キャッシュに対してのみ使用可能です。
>
>

詳細については、 [Redis キースペース通知](https://redis.io/topics/notifications)に関するトピックを参照してください。 サンプル コードについては、[Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) サンプルの [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) ファイルを参照してください。


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache for Redis Advisor
**[Azure Cache for Redis Advisor]** ブレードにキャッシュに関する推奨事項が表示されます。 通常の操作中に推奨事項は表示されません。

![どこに推奨事項が表示されるかを示すスクリーンショット。](./media/cache-configure/redis-cache-no-recommendations.png)

キャッシュの操作中に、高いメモリ使用量、ネットワーク帯域幅、サーバー負荷などの状況が発生した場合は、 **[Azure Cache for Redis]** ブレードにアラートが表示されます。

![Azure Cache for Redis セクションのどこにアラートが表示されるかを示すスクリーンショット。](./media/cache-configure/redis-cache-recommendations-alert.png)

詳細情報は、 **[推奨事項]** ブレードで確認できます。

![Recommendations](./media/cache-configure/redis-cache-recommendations.png)

**[Azure Cache for Redis]** ブレードの [[Monitoring charts]\(監視グラフ)](cache-how-to-monitor.md#monitoring-charts) セクションと [[Usage charts]\(使用状況グラフ)](cache-how-to-monitor.md#usage-charts) セクションでは、以下のメトリックを監視できます。

各価格レベルには、クライアント接続、メモリ、および帯域幅についてさまざまな制限があります。 長時間にわたり、キャッシュがこれらのメトリックの最大容量に近づいている場合は、推奨項目が作成されます。 **[推奨事項]** ツールで検証されるメトリックと制限の詳細については、次の表をご覧ください。

| Azure Cache for Redis のメトリック | 詳細情報 |
| --- | --- |
| ネットワーク帯域幅の使用量 |[キャッシュ パフォーマンス - 使用できる帯域幅](cache-planning-faq.md#azure-cache-for-redis-performance) |
| 接続されているクライアント数 |[既定の Redis サーバー構成 - maxclients](#maxclients) |
| サーバーの負荷 |[使用状況グラフ -  Redis サーバーの負荷](cache-how-to-monitor.md#usage-charts) |
| メモリ使用量 |[キャッシュのパフォーマンス - サイズ](cache-planning-faq.md#azure-cache-for-redis-performance) |

キャッシュをアップグレードするには、 **[今すぐアップグレード]** をクリックして、価格レベルを変更し、キャッシュの [スケーリング](#scale)を行います。 価格レベルの選択の詳細については、「[適切なサービス レベルを選択する](cache-overview.md#choosing-the-right-tier)」を参照してください


### <a name="scale"></a>スケール
キャッシュの価格レベルを表示または変更するには、 **[スケール]** をクリックします。 スケーリングの詳細については、「[Azure Cache for Redis のスケーリング方法](cache-how-to-scale.md)」をご覧ください。

![Azure Cache for Redis の価格レベル](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis クラスター サイズ
クラスタリングが有効になっている実行中の Premium キャッシュのクラスター サイズを変更するには、 **[クラスター サイズ]** をクリックします。

![クラスター サイズ](./media/cache-configure/redis-cache-redis-cluster-size.png)

クラスター サイズを変更するには、スライダーを使用するか、 **[シャード数]** ボックスに 1 ～ 10 の範囲の数値を入力し、 **[OK]** をクリックして保存します。

> [!IMPORTANT]
> Redis クラスタリングは、Premium キャッシュでのみ使用できます。 詳細については、「[How to configure clustering for a Premium Azure Cache for Redis](cache-how-to-premium-clustering.md)」 (Premium Azure Cache for Redis のクラスタリングの構成方法) を参照してください。
>
>


### <a name="redis-data-persistence"></a>Redis データの保持
**[データ永続化]** をクリックして、Premium Cache のデータ永続化を有効にする、無効にする、または構成することができます。 Azure Cache for Redis では、RDB の永続化または AOF の永続化を使用して Redis の永続化を行うことができます。

詳細については、[Premium Azure Cache for Redis の永続化の構成方法](cache-how-to-premium-persistence.md)についてのページを参照してください。


> [!IMPORTANT]
> Redis のデータ永続化は、Premium Cache でのみ使用できます。
>
>

### <a name="schedule-updates"></a>更新のスケジュール
**[更新のスケジュール]** ブレードでは、キャッシュの Redis サーバー更新のメンテナンス時間を指定できます。

> [!IMPORTANT]
> このメンテナンス時間は、Redis サーバーの更新にのみ適用されます。Azure の更新や、キャッシュをホストする VM のオペレーティング システムへの更新には適用されません。
>
>

![更新のスケジュール](./media/cache-configure/redis-schedule-updates.png)

メンテナンス時間を指定するには、目的の曜日をオンにし、曜日ごとにメンテナンス時間の開始時刻を指定して、 **[OK]** をクリックします。 メンテナンス時間の時刻は UTC 時間です。

詳細および手順については、[Azure Cache for Redis の管理の更新のスケジュール](cache-administration.md#schedule-updates)に関するページを参照してください

### <a name="geo-replication"></a>geo レプリケーション

**[geo レプリケーション]** ブレードには、レベルが Premium である Azure Cache for Redis の 2 つのインスタンスをリンクするメカニズムが用意されています。 一方のキャッシュはプライマリ リンク キャッシュとして、他方はセカンダリ リンク キャッシュとして指定されます。 セカンダリ リンク キャッシュは読み取り専用になり、プライマリ キャッシュに書き込まれたデータがセカンダリ リンク キャッシュにレプリケートされます。 この機能は、Azure リージョン間でキャッシュをレプリケートする際に使用できます。

> [!IMPORTANT]
> **geo レプリケーション** は、Premium レベルのキャッシュにのみ使用できます。 詳細と手順については、[Azure Cache for Redis の geo レプリケーションを構成する方法](cache-how-to-geo-replication.md)に関するページを参照してください。
>
>

### <a name="virtual-network"></a>Virtual Network
**[Virtual Network]** セクションでは、キャッシュの仮想ネットワークの設定を構成することができます。 VNET サポートでのプレミアム キャッシュの作成およびその設定の更新の詳細については、[Premium Azure Cache for Redis の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)に関するページを参照してください。

> [!IMPORTANT]
> 仮想ネットワークの設定は、キャッシュ作成時に VNET サポートで構成されたプレミアム キャッシュでのみ使用できます。
>
>

### <a name="firewall"></a>ファイアウォール

ファイアウォール ルール構成は、すべてのレベルの Azure Cache for Redis で利用できます。

キャッシュのファイアウォール ルールを表示して構成するには、 **[ファイアウォール]** をクリックします。

![ファイアウォール](./media/cache-configure/redis-firewall-rules.png)

IP アドレス範囲の開始アドレスと終了アドレスで、ファイアウォール ルールを指定できます。 ファイアウォール ルールを構成すると、指定した IP アドレス範囲からのクライアント接続のみがキャッシュに接続できます。 ファイアウォール ルールを保存してからルールが有効になるまでに、短い遅延があります。 通常、この遅延は 1 分未満です。

> [!IMPORTANT]
> ファイアウォール ルールが構成されている場合でも、Azure Cache for Redis 監視システムからの接続は常に許可されます。
>
>

### <a name="properties"></a>Properties
**[プロパティ]** をクリックすると、キャッシュ エンドポイントやポートなど、キャッシュに関する情報を表示できます。

![Azure Cache for Redis のプロパティ](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Locks
**[ロック]** セクションでは、サブスクリプション、リソース グループ、またはリソースにロックを適用し、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止できます。 詳細については、[「Azure Resource Manager によるリソースのロック」](../azure-resource-manager/management/lock-resources.md)を参照してください。

### <a name="automation-script"></a>Automation スクリプト

**[Automation スクリプト]** をクリックして、将来のデプロイのために、デプロイ済みのリソースのテンプレートをビルドおよびエクスポートします。 テンプレートを操作する方法の詳細については、 [Azure Resource Manager のテンプレートを使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)に関するページをご覧ください。

## <a name="administration-settings"></a>管理の設定
**[管理]** セクションの設定では、キャッシュに対して次の管理タスクを実行できます。

![管理](./media/cache-configure/redis-cache-administration.png)

* [データのインポート](#importexport)
* [データのエクスポート](#importexport)
* [Reboot](#reboot)


### <a name="importexport"></a>インポート/エクスポート
Import/Export は Azure Cache for Redis のデータ管理操作です。Azure Cache for Redis データベース (RDB) のスナップショットを Premium キャッシュからエクスポートし、Azure Storage アカウント内のページ BLOB にインポートすることで、キャッシュ内のデータのインポートとエクスポートが可能です。 Import/Export により、異なる Azure Cache for Redis インスタンス間での移行や、使用前のキャッシュへのデータ入力が可能になります。

Import は、任意のクラウドまたは環境で稼働している任意の Redis サーバー (Linux や Windows のほか、アマゾン ウェブ サービスをはじめとする各種クラウド プロバイダーで稼働している Redis など) から Redis と互換性のある RDB ファイルを取り込むときに使用できます。 データをインポートすると、あらかじめデータが入力されたキャッシュを簡単に作成できます。 インポート処理中に、Azure Cache for Redis は RDB ファイルを Azure ストレージからメモリに読み込み、キーをキャッシュに挿入します。

Export では、Azure Cache for Redis に格納されたデータを、Redis と互換性のある RDB ファイルにエクスポートできます。 この機能を使えば、ある Azure Cache for Redis インスタンスから、別のインスタンスまたは別の Redis サーバーにデータを移動できます。 エクスポート処理中に、Azure Cache for Redis サーバー インスタンスをホストしている VM に一時ファイルが作成され、そのファイルが、指定されているストレージ アカウントにアップロードされます。 エクスポート処理が完了したら、処理の成否にかかわらず、この一時ファイルは削除されます。

> [!IMPORTANT]
> Import/Export は、Premium レベル キャッシュにのみ使用可能です。 詳細および手順については、[Azure Cache for Redis でデータをインポートまたはエクスポートする](cache-how-to-import-export-data.md)ことに関するページを参照してください。
>
>

### <a name="reboot"></a>再起動
**[再起動]** ブレードでは、キャッシュのノードを再起動できます。 この再起動機能により、アプリケーションにキャッシュ ノードの障害が発生した場合の復元性をテストすることができます。

![再起動](./media/cache-configure/redis-cache-reboot.png)

クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するキャッシュのシャードを選択できます。

![再起動するキャッシュのシャードをどこで選択するかを示すスクリーンショット。](./media/cache-configure/redis-cache-reboot-cluster.png)

キャッシュのノードを再起動するには、目的のノードを選択し、 **[再起動]** をクリックします。 クラスタリングが有効になっている Premium キャッシュがある場合は、再起動するシャードを選択し、 **[再起動]** をクリックします。 数分後、選択したノードが再起動され、さらに数分後にオンラインに戻ります。

> [!IMPORTANT]
> 現在、再起動はすべての価格レベルで使用できます。 詳細および手順については、[Azure Redis Cache の管理の再起動](cache-administration.md#reboot)に関するページを参照してください。
>
>


## <a name="monitoring"></a>監視

**[監視]** セクションでは、Azure Cache for Redis に対する診断および監視を構成できます。
Azure Cache for Redis の監視と診断の詳細については、「[Azure Cache for Redis を監視する方法](cache-how-to-monitor.md)」を参照してください。

![診断](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis メトリック](#redis-metrics)
* [アラート ルール](#alert-rules)
* [診断](#diagnostics)

### <a name="redis-metrics"></a>[Redis メトリック]
**[Redis メトリック]** をクリックしてキャッシュの [メトリックを表示](cache-how-to-monitor.md#view-cache-metrics)します。

### <a name="alert-rules"></a>アラート ルール

**[アラート ルール]** をクリックして、Azure Cache for Redis のメトリックに基づくアラートを構成します。 詳細については、「 [警告](cache-how-to-monitor.md#alerts)」を参照してください。

### <a name="diagnostics"></a>診断

既定では、Azure Monitor のキャッシュ メトリックは [30 日間格納](../azure-monitor/essentials/data-platform-metrics.md)され、その後削除されます。 キャッシュ メトリックを 30 日よりも長く保持するには、 **[診断]** をクリックし、キャッシュ診断の格納に使用する [ストレージ アカウントを構成](cache-how-to-monitor.md#export-cache-metrics)します。

>[!NOTE]
>キャッシュ メトリックをストレージにアーカイブする以外に、[イベント ハブにストリーム配信したり、Azure Monitor ログに送信したり](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)できます。
>
>

## <a name="support--troubleshooting-settings"></a>サポートおよびトラブルシューティング設定
**[サポート + トラブルシューティング]** セクションでは、キャッシュの問題を解決するためのオプションを設定できます。

![[サポート + トラブルシューティング]](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [リソース ヘルス](#resource-health)
* [新しいサポート要求](#new-support-request)

### <a name="resource-health"></a>リソース ヘルス
**[リソース正常性]** ではリソースが監視され、そのリソースが意図したとおりに動いているかどうかが示されます。 Azure Resource Health サービスの詳細については、「 [Azure Resource Health の概要](../service-health/resource-health-overview.md)」を参照してください。

> [!NOTE]
> 現在、[リソース正常性] では、仮想ネットワークでホストされている Azure Cache for Redis インスタンスの正常性については報告できません。 詳細については、「 [VNET でキャッシュをホストしている場合、キャッシュ機能はすべて動作しますか](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>新しいサポート要求
**[新しいサポート要求]** をクリックして、キャッシュのサポート要求を開きます。





## <a name="default-redis-server-configuration"></a>既定の Redis サーバー構成
新しい Azure Cache for Redis インスタンスは、以下の既定の Redis 構成値を使用して構成されます。

> [!NOTE]
> `StackExchange.Redis.IServer.ConfigSet` メソッドを使用してこのセクションの設定を変更することはできません。 このセクションのいずれかのコマンドでこのメソッドを呼び出すと、次の例のような例外がスローされます。  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> **max-memory-policy** などの構成可能な値を、Azure Portal またはコマンド ライン管理ツール (Azure CLI、PowerShell など) を使用して構成できます。
>
>

| 設定 | 既定値 | 説明 |
| --- | --- | --- |
| `databases` |16 |データベースの既定の数は 16 ですが、価格レベルに基づいてさまざまな数を構成できます。<sup>1</sup> 既定のデータベースは DB 0 です。`dbid` が `0` ～ `databases - 1` の間の数値である `connection.GetDatabase(dbid)` を使用して、接続ごとに異なるデータベースを選択できます。 |
| `maxclients` |価格レベルによって異なります。<sup>2</sup> |この値は、同時に接続が許可されているクライアントの最大数です。 制限に達すると、Redis はすべての新しい接続を終了し、"max number of clients reached" エラーを返します。 |
| `maxmemory-policy` |`volatile-lru` |Maxmemory ポリシーとは、`maxmemory` (キャッシュ作成時に選択したキャッシュのサイズ) に達したときに、Redis が削除する項目を選択する方法についての設定です。 Azure Cache for Redis の既定の設定は `volatile-lru` で、LRU アルゴリズムを使用して、有効期限が設定されているキーを削除します。 この設定は、Azure ポータルで構成できます。 詳細については、「[メモリ ポリシー](#memory-policies)」をご覧ください。 |
| `maxmemory-samples` |3 |LRU アルゴリズムと最小 TTL アルゴリズムは精緻なアルゴリズムではなく、メモリを節約するための近似アルゴリズムです。 既定では、Redis はキーを 3 つ確認し、直近の使用頻度が比較的低いものを 1 つ選択します。 |
| `lua-time-limit` |5,000 |Lua スクリプトの最大実行時間 (ミリ秒)。 最大実行時間に達した場合は、Redis は、最大許容時間の後もスクリプトが実行中であることをログに記録し、クエリに対してエラーを知らせる応答を開始します。 |
| `lua-event-limit` |500 |スクリプト イベント キューの最大サイズです。 |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |このクライアントの出力バッファー制限を使用して、なんらかの理由 (一般的には、パブリッシャーがメッセージを作成するのと同じ速度で Pub/Sub クライアントがメッセージを利用できないという理由) により、サーバーから十分な速度でデータを読み込んでいないクライアントを強制的に切断することができます。 詳細については、[https://redis.io/topics/clients](https://redis.io/topics/clients) を参照してください。 |

<a name="databases"></a>
<sup>1</sup>`databases` の制限は、Azure Cache for Redis の価格レベルごとに異なっていて、キャッシュの作成時に設定できます。 キャッシュの作成中に `databases` を設定しない場合は、既定値の 16 が使用されます。

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
  * Redis クラスターが有効なすべての Premium キャッシュ - Redis クラスターは、データベース 0 の使用のみをサポートするため、Redis クラスターが有効な Premium キャッシュの `databases` の制限は、実質的に 1 で、 [Select](https://redis.io/commands/select) コマンドは使用できません。 詳細については、「 [クラスタリングを使用するためにクライアント アプリケーションを変更する必要がありますか](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

データベースの詳細については、「[What are Redis databases? (Redis データベースとは)](cache-development-faq.md#what-are-redis-databases)」を参照してください

> [!NOTE]
> `databases` の設定は、キャッシュの作成中にのみ構成できます。また、PowerShell、CLI、その他の管理クライアントを使用する必要があります。 PowerShell を使用して、キャッシュの作成中に `databases` を構成する例については、[New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases) に関するページをご覧ください。
>
>

<a name="maxclients"></a>
<sup>2</sup>`maxclients` は、Azure Cache for Redis の価格レベルごとに異なっています。

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

> [!NOTE]
> 各キャッシュのサイズが特定の接続数 *まで* 許容される一方で、Redis への各接続はそれにオーバーヘッドが関連付けられています。 このようなオーバーヘッドの例には、TLS/SSL 暗号化の結果としての CPU とメモリの使用量があります。 指定したキャッシュ サイズの最大接続数の上限は、負荷が低いキャッシュを想定しています。 接続オーバーヘッドからの読み込みに *加えて*、クライアントの操作からの読み込みがシステムの容量を超える場合、現在のキャッシュ サイズが接続数の上限を超えていない場合でも、キャッシュ容量の問題が発生する可能性があります。
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Azure Cache for Redis でサポートされない Redis コマンド
> [!IMPORTANT]
> Azure Cache for Redis インスタンスの構成と管理は Microsoft によって管理されるため、以下のコマンドは無効にされています。 これらのコマンドを呼び出そうとすると、`"(error) ERR unknown command"` のようなエラー メッセージが送信されます。
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * SAVE
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER - クラスターの書き込みコマンドは無効ですが、読み取り専用のクラスター コマンドは許可されます。
>
>

Redis コマンドの詳細については、[https://redis.io/commands](https://redis.io/commands) を参照してください。

## <a name="redis-console"></a>Redis コンソール
**Redis コンソール** を使用して Azure Cache for Redis インスタンスにコマンドを安全に発行できます。このコンソールはすべてのキャッシュ レベルで、Azure portal で使用できます。

> [!IMPORTANT]
> - [VNET](cache-how-to-premium-vnet.md) では Redis コンソールを使用できません。 キャッシュが VNET の一部である場合は、VNET のクライアントだけがキャッシュにアクセスできます。 Redis コンソールは、VNET の外部にあるローカル ブラウザーで実行されるため、キャッシュに接続できません。
> - Azure Cache for Redis では、すべての Redis コマンドがサポートされているわけではありません。 Azure Cache for Redis に対しては無効な Redis コマンドの一覧については、前の「[Azure Cache for Redis でサポートされない Redis コマンド](#redis-commands-not-supported-in-azure-cache-for-redis)」セクションを参照してください。 Redis コマンドの詳細については、[https://redis.io/commands](https://redis.io/commands) を参照してください。
>
>

Redis コンソールにアクセスするには、 **[Azure Cache for Redis]** ブレードの **[コンソール]** をクリックします。

![[コンソール] ボタンが強調表示されているスクリーンショット。](./media/cache-configure/redis-console-menu.png)

キャッシュ インスタンスに対してコマンドを発行するには、目的のコマンドをコンソールに入力します。

![入力コマンドと結果を含む Redis コンソールを示すスクリーンショット。](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>クラスター化された Premium キャッシュでの Redis コンソールの使用

クラスター化された Premium キャッシュで Redis コンソールを使用する場合は、キャッシュの単一のシャードにコマンドを発行できます。 コマンドを特定のシャードに発行するには、まず、シャード ピッカーで目的のシャードをクリックして、そのシャードに接続します。

![Redis コンソール](./media/cache-configure/redis-console-premium-cluster.png)

接続したシャードとは別のシャードに格納されているキーにアクセスしようとすると、次のようなエラー メッセージが表示されます。

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

前の例ではシャード 1 を選択しましたが、エラー メッセージの `(shard 0)` 部分で示されているように、`myKey` はシャード 0 にあります。 この例では、`myKey` にアクセスするために、シャード ピッカーを使用してシャード 0 を選択してから、目的のコマンドを発行します。


## <a name="move-your-cache-to-a-new-subscription"></a>新しいサブスクリプションへのキャッシュの移動
新しいサブスクリプションにキャッシュを移動するには、 **[移動]** をクリックします。

![Azure Cache for Redis を移動する](./media/cache-configure/redis-cache-move.png)

リソース グループ間、およびサブスクリプション間でのリソースの移動については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/management/move-resource-group-and-subscription.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* Redis コマンドの使用の詳細については、「[How can I run Redis commands? (Redis コマンドの実行方法)](cache-development-faq.md#how-can-i-run-redis-commands)」をご覧ください。
