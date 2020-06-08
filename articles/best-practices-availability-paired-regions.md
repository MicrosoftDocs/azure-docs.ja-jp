---
title: Azure のペアになっているリージョンを使用したビジネス継続性とディザスター リカバリー
description: Azure リージョン ペアを使用してアプリケーションの回復性を確保する
author: barbkess
manager: barbkess
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: barbkess
ms.openlocfilehash: be6d2fb590dfefb170b3f23378c507b4d6b2295d
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980050"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>ビジネス継続性とディザスター リカバリー (BCDR):Azure のペアになっているリージョン

## <a name="what-are-paired-regions"></a>ペアになっているリージョンとは

Azure リージョンは一連のデータ センターで構成されています。それらのデータ センターは、待機時間で定義された境界内にデプロイされ、待機時間の短い専用ネットワークを介して接続されています。  これにより、Azure リージョン内の Azure サービスで最大限のパフォーマンスとセキュリティが確実に提供されます。  

Azure 地域とは、少なくとも 1 つの Azure リージョンを含む、世界の中の地域のことです。 地域には個別のマーケットが定義され、通常は 2 つ以上のリージョンを含み、データ所在地とコンプライアンスの境界を保持します。  Azure のグローバル インフラストラクチャの詳細については、[こちら](https://azure.microsoft.com/global-infrastructure/regions/)を参照してください。

リージョン ペアは、同じ地域内の 2 つのリージョンで構成されます。 Azure では、リージョン ペアに対してプラットフォームの更新 (計画メンテナンス) をシリアル化し、一度に更新されるリージョンが各ペアのうち 1 つのみになるようにしています。 停止によって複数のリージョンに影響がある場合、各ペアの少なくとも 1 つのリージョンが優先的に復旧されます。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

一部の Azure サービスでは、ペアのリージョンをさらに活用することでビジネス継続性を確保し、データの損失を防いでいます。  Azure には、ペアのリージョンを活用してデータの可用性を確保する[ストレージ ソリューション](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region)がいくつか用意されています。 たとえば、[Azure Geo 冗長ストレージ](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) では、データがセカンダリ リージョンに自動的にレプリケートされるため、プライマリ リージョンが復旧できない場合でもデータの持続性を保つことができます。 

すべての Azure サービスで自動的にデータがレプリケートされるわけではなく、また、すべての Azure サービスで障害が発生したリージョンからペアへの自動的なフォールバックが行われるわけでもないことに注意してください。  このような場合、復旧とレプリケーションはお客様が構成する必要があります。

## <a name="can-i-select-my-regional-pairs"></a>リージョン ペアは自分で選択できますか?

いいえ。 一部の Azure サービス (Azure の[冗長ストレージ](./storage/common/storage-redundancy.md)など) はリージョン ペアに依存しています。 それらのサービスでは、新しいリージョン ペアを作成することはできません。  同様に、リージョン ペアの計画メンテナンスと回復の優先順位付けが Azure によって制御されるため、それらのサービスを利用するために独自のリージョン ペアを定義することはできません。 ただし、任意の数のリージョン内にサービスを構築し、Azure サービスを利用してそれらのリージョンを組み合わせることで、独自のディザスター リカバリー ソリューションを作成できます。 

たとえば、[AzCopy](./storage/common/storage-use-azcopy-v10.md) などの Azure サービスを使用して、別のリージョンのストレージ アカウントへのデータ バックアップをスケジュールすることができます。  お客様は、[Azure DNS と Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md) を使用して、プライマリ リージョンの消失に耐えられる回復力のあるアーキテクチャをアプリケーション用に設計できます。

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>サービスの使用はリージョン ペア内に制限されていますか?

いいえ。 特定の Azure サービスはリージョン ペアに依存する場合がありますが、ビジネス ニーズを満たすご自身の他のサービスを任意のリージョンでホストすることができます。  Azure GRS ストレージ ソリューションでは、米国東部にあるコンピューティング リソースを使用しながら、カナダ東部のピアとカナダ中部のデータをペアリングできます。  

## <a name="must-i-use-azure-regional-pairs"></a>Azure リージョン ペアは必ず使用する必要があるのですか?

いいえ。 お客様は Azure サービスを活用して、Azure のリージョン ペアに依存することなく、回復力のあるサービスを設計できます。  ただし、リージョン ペアの間で事業継続とディザスター リカバリー (BCDR) を構成して、[分離](./security/fundamentals/isolation-choices.md)を活用し、[可用性](./availability-zones/az-overview.md)を向上させることをお勧めします。 複数のアクティブなリージョンをサポートするアプリケーションでは、可能な場合はリージョン ペアの両方のリージョンを使用することをお勧めします。 これにより、アプリケーションの最適な可用性が保証され、災害発生時の復旧時間が最小になります。 可能な限り、[最大の回復性](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)と[ディザスター リカバリー](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)の容易さを実現するようにアプリケーションを設計してください。

## <a name="azure-regional-pairs"></a>Azure リージョン ペア

| [地理的な場所] | リージョン ペア A | リージョン ペア B  |
|:--- |:--- |:--- |
| アジア太平洋 |東アジア (香港) | 東南アジア (シンガポール) |
| オーストラリア |オーストラリア東部 |オーストラリア南東部 |
| オーストラリア |オーストラリア中部 |オーストラリア中部 2 |
| ブラジル |ブラジル南部 |米国中南部 |
| カナダ |カナダ中部 |カナダ東部 |
| 中国 |中国北部 |中国東部|
| 中国 |中国北部 2 |中国東部 2|
| ヨーロッパ |北ヨーロッパ (アイルランド) |西ヨーロッパ (オランダ) |
| フランス |フランス中部|フランス南部|
| ドイツ |ドイツ中部 |ドイツ北東部 |
| インド |インド中部 |インド南部 |
| インド |インド西部 |インド南部 |
| 日本 |東日本 |西日本 |
| 韓国 |韓国中部 |韓国南部 |
| 北米 |米国東部 |米国西部 |
| 北米 |米国東部 2 |米国中部 |
| 北米 |米国中北部 |米国中南部 |
| 北米 |米国西部 2 |米国中西部 |
| ノルウェー | ノルウェー東部 | ノルウェー西部 |
| 南アフリカ | 南アフリカ北部 |南アフリカ西部 |
| スイス | スイス北部 |スイス西部 |
| 英国 |英国西部 |英国南部 |
| アラブ首長国連邦 | アラブ首長国連邦北部 | アラブ首長国連邦中部
| 米国国防総省 |US DoD East |US DoD Central |
| 米国政府 |US Gov アリゾナ |US Gov テキサス |
| 米国政府 |US Gov アイオワ |US Gov バージニア州 |
| 米国政府 |US Gov バージニア州 |US Gov テキサス |

> [!Important]
> - インド西部は、一方向のみでペアになっています。 インド西部のセカンダリ リージョンはインド南部ですが、インド南部のセカンダリ リージョンはインド中部です。
> - ブラジル南部は、地域外のリージョンとペアになっているため特殊です。 ブラジル南部のセカンダリ リージョンは米国中南部です。 米国中南部のセカンダリ リージョンはブラジル南部ではありません。


## <a name="an-example-of-paired-regions"></a>ペアになっているリージョンの例
以下の図は、リージョン ペアを使ってディザスター リカバリーを行う架空のアプリケーションを示しています。 緑色の番号は、3 つの Azure サービス (Azure コンピューティング、ストレージ、およびデータベース) のリージョン間アクティビティと、そのアクティビティが、リージョン間でのレプリケートのためにどのように構成されているかを示しています。 リージョン ペアにデプロイするメリットは、オレンジ色の番号で示されています。

![ペア リージョンのメリットの概要](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

図 2 – Azure リージョン ペアの例

## <a name="cross-region-activities"></a>リージョン間アクティビティ
図 2 を参照してください。

1. **Azure Compute (IaaS)** – 災害発生中に別のリージョンでリソースを確実に使用できるように、追加のコンピューティング リソースを事前にプロビジョニングする必要があります。 詳細については、「[Azure の回復性技術ガイダンス](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md)」をご覧ください。 

2. **Azure Storage** - マネージド ディスクを使用している場合は、Azure Backup を使用した[リージョン間のバックアップ](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)、および Azure Site Recovery を使用したリージョン間での [VM のレプリケーション](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)について確認してください。 ストレージ アカウントを使用している場合は、Azure Storage アカウントの作成時に、geo 冗長ストレージ (GRS) が既定で構成されます。 GRS を使用すると、データはプライマリ リージョン内で 3 回、ペア リージョンで 3 回、自動的にレプリケートされます。 詳細については、「 [Azure Storage 冗長オプション](storage/common/storage-redundancy.md)」をご覧ください。

3. **Azure SQL Database** – Azure SQL Database geo レプリケーションを使用すると、世界中の任意のリージョンへのトランザクションの非同期レプリケーションを構成できます。ただし、ほとんどのディザスター リカバリー シナリオでは、これらのリソースをペア リージョン内にデプロイすることをお勧めします。 詳細については、「[Azure SQL Database の geo レプリケーション](sql-database/sql-database-geo-replication-overview.md)」を参照してください。

4. **Azure Resource Manager** - Resource Manager では本質的に、リージョン全体のコンポーネントが論理的に切り離されています。 つまり、1 つのリージョンで論理的な障害が発生しても、他のリージョンが影響を受ける可能性はそれほど高くありません。

## <a name="benefits-of-paired-regions"></a>ペアになっているリージョンのメリット

5. **物理的な分離** – 可能な場合、Azure で推奨されるリージョン内の各データセンター間の距離は 300 マイル (480 km) ですが、これは一部の地域では現実的ではなく、実現できないこともあります。 物理データセンターを切り離すことで、自然災害、社会不安、停電、および物理ネットワーク障害が両方のリージョンにすぐに影響を及ぼす可能性が少なくなります。 分離は、geo 内の制約 (広さ、電源/ネットワーク インフラストラクチャの可用性、規制など) に左右されます。  

6. **プラットフォームに備わっているレプリケーション** - geo 冗長ストレージなどの一部のサービスには、ペア リージョンへの自動レプリケーション機能が用意されています。

7. **リージョン復旧順序** – 広範囲にわたって障害が発生した場合は、すべてのペアで一方のリージョンの復旧が優先されます。 ペア リージョンにまたがってデプロイされているアプリケーションについては、そのアプリケーションのリージョンのどちらかが必ず優先的に復旧します。 ペアになっていない複数のリージョンにアプリケーションがデプロイされていると、復旧が遅れる可能性があり、最悪の場合は、回復されてない最後の 2 つになってしまうことがあります。

8. **順次更新** – 計画的な Azure システム更新プログラムは、ダウンタイム、バグの影響、および更新プログラムの不具合 (まれではありますが) による論理的な障害を最小限に抑えるために、ペア リージョンに (同時にではなく) 順番に展開されます。

9. **データ所在地** – リージョンは、税および法の執行を目的としたデータ所在地の要件を満たすために、ペアとして同じ地域に存在しています (ブラジル南部を除く)。
