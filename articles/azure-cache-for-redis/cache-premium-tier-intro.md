---
title: Azure Cache for Redis Premium レベルの概要 | Microsoft Docs
description: Premium レベルの Azure Cache for Redis インスタンス向けの Redis の永続化、Redis クラスタリング、および VNET サポートの作成と管理方法について説明します。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 30f46f9f-e6ec-4c38-a8cc-f9d4444856e5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 6757a038a945bf153cb62662bee8c715c87baf19
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237742"
---
# <a name="introduction-to-the-azure-cache-for-redis-premium-tier"></a>Azure Cache for Redis Premium レベルの概要
Azure Cache for Redis は、データへの超高速アクセスを提供することで、拡張性や応答性の高いアプリケーションの作成を可能にする分散マネージド キャッシュです。 

新しい Premium レベルは、Standard レベルの全機能に加えて、パフォーマンスの向上、ワークロードの増加、障害復旧、インポート/エクスポート、セキュリティの強化などを備えたエンタープライズ対応のレベルです。 Premium キャッシュ レベルの追加機能の詳細をお知りになりたい場合は、このドキュメントを引き続きお読みください。

## <a name="better-performance-compared-to-standard-or-basic-tier"></a>Standard/Basic レベルを超えるパフォーマンス
**Standard/Basic レベルを超えるパフォーマンス。** Premium レベルのキャッシュは、高速プロセッサを備え、Basic/Standard レベルと比較して優れたパフォーマンスを発揮するハードウェア上にデプロイされます。 Premium レベルのキャッシュは、スループットが高く、待機時間が低くなっています。 

**同じサイズのキャッシュでも Standard レベルと比べて Premium のスループットの方が高い。** たとえば、C6 (Standard) キャッシュのスループットが毎秒 15 万要求であるのに対し、53 GB の P4 (Premium) キャッシュは毎秒 25 万要求です。

Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)」(Azure Cache for Redis の FAQ) を参照してください。

## <a name="redis-data-persistence"></a>Redis データの保持
Premium レベルでは、Azure Storage アカウント内のキャッシュ データを永続化できます。 Basic/Standard のキャッシュでは、データはすべてメモリ内にのみ格納されます。 基盤となるインフラストラクチャに問題が発生した場合、データが失われる可能性があります。 Premium レベルの Redis データの保持機能を使用して、データ損失に対する復元性を高めることをお勧めします。 Azure Cache for Redis では、[Redis 永続化](https://redis.io/topics/persistence)の RDB オプションと AOF オプション (近日提供予定) が用意されています。 

永続化の構成手順については、[Premium Azure Cache for Redis の永続性の構成方法](cache-how-to-premium-persistence.md)に関するページを参照してください。

## <a name="redis-cluster"></a>Redis クラスター
53 GB を超えるキャッシュを作成するか、複数の Redis ノード間でデータを共有する場合、Premium レベルで利用可能な Redis クラスタリングを使用することができます。 各ノードは、Azure によって管理される高可用性対応のプライマリ/レプリカ キャッシュのペアで構成されています。 

**Redis クラスタリングでは、スケールとスループットを最大限に利用できます。** クラスター内のシャード (ノード) の数を増やすと、スループットは直線的に増加します。 例: 10 個のシャードで構成される P4 クラスターを作成すると、利用可能なスループットは毎秒 250 万要求 (25 万 * 10) となります。 Premium キャッシュのサイズ、スループット、帯域幅の詳細については、「[Azure Cache for Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)」(Azure Cache for Redis の FAQ) を参照してください。

クラスタリングを開始する場合は、[Premium Azure Cache for Redis のクラスタリングの構成方法](cache-how-to-premium-clustering.md)に関するページを参照してください。

## <a name="enhanced-security-and-isolation"></a>強化されたセキュリティと分離
Basic/Standard レベルで作成されたキャッシュは、パブリック インターネット上でアクセス可能です。 キャッシュへのアクセスは、アクセス キーに基づいて制限されます。 Premium レベルでは、さらに、指定されたネットワーク内のクライアントだけがキャッシュにアクセスできるようにすることが可能になります。 Azure Cache for Redis は、[Azure Virtual Network (VNet)](https://azure.microsoft.com/services/virtual-network/) 内にデプロイできます。 サブネット、アクセス制御ポリシー、およびその他の Redis へのアクセスをさらに制限する機能を始め、VNet のすべての機能を使用できます。

詳細については、[Premium Azure Cache for Redis の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)に関するページを参照してください。

## <a name="importexport"></a>Import/Export
Import/Export は Azure Cache for Redis のデータ管理操作です。Azure Cache for Redis データベース (RDB) のスナップショットを Premium キャッシュと Azure Storage アカウント内のページ BLOB の間でインポートとエクスポートを行うことで、データを Azure Cache for Redis にインポートしたり、Azure Cache for Redis からエクスポートしたりすることができます。 これにより、異なる Azure Cache for Redis インスタンス間での移行や、使用前のキャッシュへのデータ入力が可能になります。

インポートは、任意のクラウドまたは環境で稼働している任意の Redis サーバー (Linux や Windows のほか、アマゾン ウェブ サービスをはじめとする各種クラウド プロバイダーで稼働している Redis など) から Redis と互換性のある RDB ファイルを取り込むときに使用できます。 データをインポートすると、あらかじめデータが入力されたキャッシュを簡単に作成できます。 インポート処理中に、Azure Cache for Redis は RDB ファイルを Azure Storage からメモリに読み込み、キーをキャッシュに挿入します。

Export では、Azure Cache for Redis に格納されたデータを、Redis と互換性のある RDB ファイルにエクスポートできます。 この機能を使えば、ある Azure Cache for Redis インスタンスから、別のインスタンスまたは別の Redis サーバーにデータを移動できます。 エクスポート処理中に、Azure Cache for Redis サーバー インスタンスをホストしている VM に一時ファイルが作成され、そのファイルが、指定されているストレージ アカウントにアップロードされます。 エクスポート処理が完了したら、処理の成否にかかわらず、この一時ファイルは削除されます。

詳細については、[Azure Cache for Redis との間でデータのインポートとエクスポートを実行する方法](cache-how-to-import-export-data.md)に関するページを参照してください。

## <a name="reboot"></a>再起動
Premium レベルでは、キャッシュの 1 つ以上のノードをオンデマンドで再起動できます。 これにより、障害発生時のアプリケーションの回復性をテストすることができます。 次のノードを再起動できます。

* キャッシュのマスター ノード
* キャッシュのスレーブ ノード
* キャッシュのマスター ノードとスレーブ ノードの両方
* クラスタリングを備えた Premium キャッシュを使用している場合は、マスター ノード、スレーブ ノード、またはその両方を、キャッシュの個々のシャードに対して再起動できます

詳細については、「[再起動](cache-administration.md#reboot)」および「[再起動に関する FAQ](cache-administration.md#reboot-faq)」を参照してください。

>[!NOTE]
>再起動の機能は、すべての Azure Cache for Redis レベルで有効になりました。
>
>

## <a name="schedule-updates"></a>更新のスケジュール
更新のスケジュール機能では、キャッシュのメンテナンス時間を指定できます。 メンテナンス時間を指定すると、その時間にすべての Redis サーバーの更新が実行されます。 メンテナンス時間を指定するには、目的の曜日をオンにして、曜日ごとにメンテナンス時間の開始時刻を指定します。 メンテナンス時間の時刻は UTC 時間で指定します。 

詳細については、「[更新のスケジュール](cache-administration.md#schedule-updates)」および「[更新のスケジュールに関する FAQ](cache-administration.md#schedule-updates-faq)」を参照してください。

> [!NOTE]
> スケジュールされたメンテナンス時間に行われるのは、Redis サーバーの更新だけです。 メンテナンス時間は、Azure の更新や、VM のオペレーティング システムへの更新には適用されません。
> 
> 

## <a name="geo-replication"></a>geo レプリケーション

**geo レプリケーション**には、レベルが Premium である Azure Cache for Redis の 2 つのインスタンスをリンクするメカニズムが用意されています。 一方のキャッシュはプライマリ リンク キャッシュとして、他方はセカンダリ リンク キャッシュとして指定されます。 セカンダリ リンク キャッシュは読み取り専用になり、プライマリ キャッシュに書き込まれたデータがセカンダリ リンク キャッシュにレプリケートされます。 この機能は、Azure リージョン間でキャッシュをレプリケートする際に使用できます。

詳細については、[Azure Cache for Redis の geo レプリケーションの構成方法](cache-how-to-geo-replication.md)に関するページを参照してください。


## <a name="to-scale-to-the-premium-tier"></a>Premium レベルにスケーリングするには
Premium レベルにスケーリングするには、 **[価格レベルの変更]** ブレードで Premium レベルのいずれかを選択するだけです。 PowerShell および CLI を使用して、キャッシュを Premium レベルにスケーリングすることもできます。 詳細な手順については、「[How to Scale Azure Cache for Redis](cache-how-to-scale.md)」(Azure Redis Cache のスケーリング方法) と「[How to automate a scaling operation](cache-how-to-scale.md#how-to-automate-a-scaling-operation)」(スケーリング処理を自動化する方法) を参照してください。

## <a name="next-steps"></a>次の手順
キャッシュを作成し、Premium レベルの新機能を確認します。

* [Premium Azure Cache for Redis の永続化の構成方法](cache-how-to-premium-persistence.md)
* [Premium Azure Cache for Redis の Virtual Network のサポートを構成する方法](cache-how-to-premium-vnet.md)
* [Premium Azure Cache for Redis のクラスタリングの構成方法](cache-how-to-premium-clustering.md)
* [Azure Cache for Redis との間でデータのインポートとエクスポートを実行する方法](cache-how-to-import-export-data.md)
* [Azure Cache for Redis を管理する方法](cache-administration.md)

