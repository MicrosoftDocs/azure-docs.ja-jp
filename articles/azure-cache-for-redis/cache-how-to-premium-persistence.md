---
title: Premium Azure Cache for Redis のデータ永続化の構成方法
description: Premium レベルの Azure Cache for Redis インスタンス用にデータの永続化を構成して管理する方法について説明します
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: b01cf279-60a0-4711-8c5f-af22d9540d38
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: yegu
ms.openlocfilehash: de0b2e3ef7b0268540ef4896ade132a297ee88ff
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233985"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Premium Azure Cache for Redis のデータ永続化の構成方法
Azure Cache for Redis には、クラスタリング、永続性、仮想ネットワークのサポートといった Premium レベルの機能など、キャッシュのサイズと機能を柔軟に選択できるさまざまなキャッシュ サービスがあります。 この記事では、Azure Cache for Redis インスタンスで永続化を構成する方法について説明します。

Premium キャッシュのその他の機能については、「[Introduction to the Azure Cache for Redis Premium tier (Azure Cache for Redis Premium レベルの概要)](cache-premium-tier-intro.md)」を参照してください。

## <a name="what-is-data-persistence"></a>データの永続化とは
[Redis 永続化](https://redis.io/topics/persistence)を使用すると、Redis に格納されたデータを保持できます。 また、スナップショットを取得したりデータをバックアップしたりして、ハードウェア障害のときに読み込むことができます。 これは、Basic レベルや Standard レベルにはない大きな利点です。Basic/Standard レベルでは、すべてのデータはメモリに格納され、Cache ノードがダウンするような障害時にはデータが失われる可能性があります。 

Azure Cache for Redis では、以下のモデルを使用した Redis 永続化を提供しています。

* **RDB 永続化** - RDB (Redis データベース) 永続化が構成されている場合、Azure Cache for Redis は、構成可能なバックアップ頻度に基づき Redis バイナリ形式でそのスナップショットをディスクに保持します。 プライマリとレプリカの両方のキャッシュが無効になるような致命的なイベントが発生した場合、最新のスナップショットを使用してキャッシュが再構築されます。 RDB 永続化の[長所](https://redis.io/topics/persistence#rdb-advantages)と[短所](https://redis.io/topics/persistence#rdb-disadvantages)について、詳細をご確認ください。
* **AOF 永続化** - AOF (追加専用ファイル) 永続化が構成されている場合、Azure Cache for Redis では、すべての書き込み操作をログに保存します。このログは最低でも 1 秒に 1 回、Azure ストレージ アカウントに保存されます。 プライマリとレプリカの両方のキャッシュが無効になるような致命的なイベントが発生した場合、保存されている書き込み操作を使用してキャッシュが再構築されます。 AOF 永続化の[長所](https://redis.io/topics/persistence#aof-advantages)と[短所](https://redis.io/topics/persistence#aof-disadvantages)について、詳細をご確認ください。

永続化は、キャッシュの作成中に **[New Azure Cache for Redis]\(新規 Azure Cache for Redis\)** ブレードから、また既存の Premium キャッシュ用の **[リソース] メニュー**で構成します。

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Premium 価格レベルを選択した後、 **[Redis の永続化]** をクリックします。

![[Redis の永続化]][redis-cache-persistence]

次のセクションの手順では、新しい Premium キャッシュで Redis 永続化を構成する方法を示します。 Redis の永続化が構成されたら、 **[作成]** をクリックして、Redis の永続化で新規 Premium キャッシュを作成します。

## <a name="enable-redis-persistence"></a>Redis 永続化の有効化

Redis 永続化は、**[Redis データ永続化]** ブレードで **[RDB]** または **[AOF]** 永続化のいずれかを選択して有効にします。 新規キャッシュでは、前のセクションで説明したように、このブレードにはキャッシュの作成プロセス中にアクセスします。 既存のキャッシュでは、**[Redis データ永続化]** ブレードには、キャッシュの **[リソース]** メニューからアクセスします。

![Redis の設定][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RDB 永続化の構成

RDB 永続化を有効にするには、**[RDB]** をクリックします。 以前から有効になっている Premium キャッシュの RDB 永続化を無効にするには、**[無効]** をクリックします。

![Redis RDB 永続化][redis-cache-rdb-persistence]

バックアップの間隔を構成するには、ドロップダウン リストから **[バックアップの頻度]** を選択します。 選択肢は、**15 分**、**30 分**、**60 分**、**6 時間**、**12 時間**、**24 時間**です。 前のバックアップ操作が正常に完了するとこの間隔のカウントダウンが開始し、期間が経過すると新しいバックアップが開始されます。

**[ストレージ アカウント]** をクリックして使用するストレージ アカウントを選択し、**[ストレージ キー]** ボックスの一覧から使用する**プライマリ キー**または**セカンダリ キー**を選択します。 Cache と同じリージョンのストレージ アカウントを選択する必要があり、また、スループットが高いため **Premium Storage** アカウントを使用することをお勧めします。 

> [!IMPORTANT]
> 永続化アカウントのストレージ キーを再生成した場合、**[ストレージ キー]** ドロップダウンから目的のキーを再構成する必要があります。
> 
> 

**[OK]** をクリックして永続化の構成を保存します。

バックアップ間隔が経過すると、次のバックアップ (または新しいキャッシュの最初のバックアップ) が開始されます。

## <a name="configure-aof-persistence"></a>AOF 永続化の構成

AOF 永続化を有効にするには、**[AOF]** をクリックします。 以前から有効になっている Premium キャッシュの AOF 永続化を無効にするには、**[無効]** をクリックします。

![Redis AOF 永続化][redis-cache-aof-persistence]

AOF 永続化を構成するには、**[最初のストレージ アカウント]** を指定します。 キャッシュと同じリージョンのストレージ アカウントを指定する必要があり、また、スループットが高いため **Premium Storage** アカウントを使用することをお勧めします。 必要に応じて **[2 つ目のストレージ アカウント]** という追加のストレージ アカウントを構成できます。 2 つ目のストレージ アカウントが構成されていると、レプリカ キャッシュへの書き込みはこの 2 つ目のストレージ アカウントに書き込まれます。 構成済みのストレージ アカウントごとに、**[ストレージ キー]** ボックスの一覧から、使用する **[プライマリ キー]** または **[セカンダリ キー]** を選択します。 

> [!IMPORTANT]
> 永続化アカウントのストレージ キーを再生成した場合、**[ストレージ キー]** ドロップダウンから目的のキーを再構成する必要があります。
> 
> 

AOF 永続化が有効になっていると、キャッシュへの書き込み操作は指定したストレージ アカウント (2 つ目のストレージ アカウントを構成している場合は 2 つのストレージ アカウント) に保存されます。 プライマリとレプリカの両方のキャッシュが削除されるような致命的なエラーが発生した場合は、保存されている AOF ログを使用してキャッシュが再構築されます。

## <a name="persistence-faq"></a>永続化の FAQ
次の一覧は、Azure Cache for Redis の永続化に関するよく寄せられる質問への回答です。

* [以前に作成したキャッシュで永続化を有効にできますか](#can-i-enable-persistence-on-a-previously-created-cache)
* [AOF 永続化と RDB 永続化を同時に有効にすることはできますか](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [どちらの永続化モデルを選択すべきですか](#which-persistence-model-should-i-choose)
* [別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB 永続化
* [キャッシュの作成後に RDB バックアップ頻度を変更できますか](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [RDB バックアップ頻度を 60 分に設定しているのに、バックアップの間隔が 60 分より長くなるのはなぜですか](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [新しいバックアップが作成されると、古い RDB バックアップはどうなりますか](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF 永続化
* [どのような場合に 2 つ目のストレージ アカウントを使用すべきですか](#when-should-i-use-a-second-storage-account)
* [AOF 永続化はキャッシュのスループット、待ち時間、またはパフォーマンスに影響しますか](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [2 つ目のストレージ アカウントを削除するには、どうすればよいですか](#how-can-i-remove-the-second-storage-account)
* [再書き込みとは何ですか。キャッシュにどのように影響しますか](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [AOF が有効になっているキャッシュをスケーリングする場合に、どのようなことを想定すべきですか](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [AOF データはストレージ内でどのように整理されますか](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>以前に作成したキャッシュで永続化を有効にできますか
はい、Redis の永続化はキャッシュの作成時と、既存の Premium キャッシュの両方に構成できます。

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>AOF 永続化と RDB 永続化を同時に有効にすることはできますか

いいえ、有効にできるのは RDB と AOF のいずれか 1 つのみで、両方を同時に有効にすることはできません。

### <a name="which-persistence-model-should-i-choose"></a>どちらの永続化モデルを選択すべきですか

AOF 永続化ではすべての書き込みがログに保存され、スループットに多少の影響があるのに対し、RDB 永続化では構成済みのバックアップ間隔に基づきバックアップが保存され、パフォーマンスへの影響は最小限に抑えられます。 主な目的がデータ損失の最小化で、キャッシュでのスループットの低下に対処できるという場合は、AOF 永続化を選択してください。 キャッシュでのスループットを最適に維持したいものの、データ復旧メカニズムも必要という場合は、RDB 永続化を選択してください。

* RDB 永続化の[長所](https://redis.io/topics/persistence#rdb-advantages)と[短所](https://redis.io/topics/persistence#rdb-disadvantages)について、詳細をご確認ください。
* AOF 永続化の[長所](https://redis.io/topics/persistence#aof-advantages)と[短所](https://redis.io/topics/persistence#aof-disadvantages)について、詳細をご確認ください。

AOF 永続化を使用しているときのパフォーマンスの詳細については、「[AOF 永続化はキャッシュのスループット、待ち時間、またはパフォーマンスに影響しますか](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)」を参照してください。

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか

RDB 永続化の場合も AOF 永続化の場合も、以下のように処理されます。

* 大きいサイズにスケーリングした場合、影響はありません。
* 小さいサイズにスケーリングした場合、新しいサイズの[データベースの制限](cache-configure.md#databases)より大きなカスタムの[データベース](cache-configure.md#databases)設定が存在すると、そのデータベースのデータは復元されません。 詳細については、「[スケーリング中に影響を受けるカスタム データベース](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling)」を参照してください
* 小さいサイズにスケーリングしていて、最新のバックアップからのデータをすべて保持するにはサイズが小さいためスペースが足りない場合、キーは復元プロセス中に削除されます。通常は [allkeys-lru](https://redis.io/topics/lru-cache) 削除ポリシーを使用します。

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>キャッシュの作成後に RDB バックアップ頻度を変更できますか
はい、**[Redis データ永続化]** ブレードで RDB 永続化のバックアップ頻度を変更できます。 手順については、「Redis の永続化を構成する」をご覧ください。

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>RDB バックアップ頻度を 60 分に設定しているのに、バックアップの間隔が 60 分より長くなるのはなぜですか
RDB 永続化のバックアップ頻度の間隔は、その前のバックアップ プロセスが正常に完了するまでは開始しません。 バックアップ間隔を 60 分に設定し、バックアップ プロセスが正常に完了するのに 15 分かかる場合、次のバックアップは、前回のバックアップの開始時刻から 75 分経つまで開始しません。

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>新しいバックアップが作成されると、古い RDB バックアップはどうなりますか
最新のものを除くすべての RDB 永続化のバックアップは自動的に削除されます。 この削除はすぐに行われないことがありますが、古いバックアップは無期限には保持されません。


### <a name="when-should-i-use-a-second-storage-account"></a>どのような場合に 2 つ目のストレージ アカウントを使用すべきですか

AOF 永続化用の 2 つ目のストレージ アカウントは、キャッシュに対するセット操作の数が予想より多いと感じた場合に使用してください。  2 つ目のストレージ アカウントを設定することにより、キャッシュがストレージの帯域幅制限に達することを防止できます。

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>AOF 永続化はキャッシュのスループット、待ち時間、またはパフォーマンスに影響しますか

キャッシュによる負荷が最大負荷を下回っている (CPU およびサーバー負荷が両方とも 90% 未満である) 場合、AOF 永続化のスループットへの影響は約 15 ～ 20% となります。 キャッシュがこれらの制限内にある場合、待ち時間の問題は発生しません。 ただし、AOF が有効になっていると、キャッシュがより早くこれらの制限に達するようになります。

### <a name="how-can-i-remove-the-second-storage-account"></a>2 つ目のストレージ アカウントを削除するには、どうすればよいですか

AOF 永続化の 2 つ目のストレージ アカウントは、その 2 つ目のストレージ アカウントを最初のストレージ アカウントと同じように設定にすることで削除できます。 手順については、「[AOF 永続化の構成](#configure-aof-persistence)」を参照してください。

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>再書き込みとは何ですか。キャッシュにどのように影響しますか

AOF ファイルが十分な大きさになると、キャッシュに対する再書き込みが自動的にキューに登録されます。 再書き込みにより、現在のデータ セットを作成するために必要な最小の操作セットで AOF ファイルのサイズ変更が行われます。 再書き込み中はパフォーマンス制限に早く達するということを想定しておいてください (特に大型のデータセットを処理する場合)。 再書き込みの発生頻度は、AOF ファイルが大きくなるにつれて低下しますが、発生時にはかなりの時間を要します。

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>AOF が有効になっているキャッシュをスケーリングする場合に、どのようなことを想定すべきですか

スケーリング時の AOF ファイルが非常に大きい場合は、スケーリングの完了後にファイルが再度読み込まれるため、スケール操作に予想よりも長い時間がかかることを想定しておいてください。

スケーリングの詳細については、「[別のサイズにスケーリングしていて、スケーリング操作の前に作成したバックアップを復元したらどうなりますか](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)」を参照してください。

### <a name="how-is-my-aof-data-organized-in-storage"></a>AOF データはストレージ内でどのように整理されますか

AOF ファイルに格納されたデータは、ストレージへのデータの保存パフォーマンスを向上させるため、ノードごとに複数のページ BLOB に分割されます。 次の表は、各価格レベルで使用されるページ BLOB の数を示しています。

| Premium レベル | BLOB |
|--------------|-------|
| P1           | シャードあたり 4    |
| P2           | シャードあたり 8    |
| P3           | シャードあたり 16   |
| P4           | シャードあたり 20   |

クラスタリングが有効になっている場合は、前の表に示されているように、キャッシュ内の各シャードにそれぞれのページ BLOB のセットが含まれます。 たとえば、3 つのシャードがある P2 キャッシュでは、AOF ファイルが 24 個のページ BLOB (シャードあたり 8 BLOB で 3 シャード) に振り分けられています。

再書き込み後、ストレージ内には 2 セットの AOF ファイルが存在します。 再書き込みはバックグラウンドで発生して最初のファイル セットに追加され、一方で再書き込み中にキャッシュに送信されるセット操作は 2 つ目のファイル セットに追加されます。 バックアップはエラーが発生した場合に備えて再書き込み中に一時的に保存されますが、再書き込みの完了後すぐに削除されます。


## <a name="next-steps"></a>次の手順
Premium キャッシュ機能をさらに使用する方法を学習します。

* [Azure Cache for Redis Premium レベルの概要](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
