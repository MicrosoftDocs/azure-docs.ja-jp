---
title: Azure Azure リージョン
description: Azure で仮想マシンを実行するためのリージョンについて学習します。
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/28/2019
ms.openlocfilehash: 094b78fa35da451101f4f5664cca46292d78d146
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650555"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Azure の仮想マシンのリージョン

Azure で仮想マシン (VM) がどこで、どのように運用されているかを理解するのは重要なことです。また、パフォーマンス、可用性、冗長性を最大化するためのオプションとしてどのようなものがあるかを知っておくことにも、大きな意義があります。 この記事では、可用性と冗長性に関する Azure の各種機能の概要を紹介します。


## <a name="what-are-azure-regions"></a>Azure リージョンとは?
Azure は、世界中の複数のデータセンターで動作しています。 これらのデータセンターは、地理的なリージョンにグループ化されていて、アプリケーションの作成場所を選択するときの柔軟性を与えています。 

Azure のリソースは、"米国西部"、"北ヨーロッパ"、"東南アジア" などの定義された地理的リージョンで作成します。 [リージョンとその場所の一覧](https://azure.microsoft.com/regions/)をご確認ください。 各リージョンには、冗長性と可用性を確保するために複数のデータ センターが存在します。 こうすることで、アプリケーションを柔軟に設計でき、ユーザーに最も近い場所に VM を作成したり、法律、コンプライアンス、税金などの各種の目的に合致させたりすることができます。

## <a name="special-azure-regions"></a>特殊な Azure リージョン
Azure には、コンプライアンスまたは法的な目的に合致したアプリケーションを構築する際に使用できる特別なリージョンがいくつかあります。 特殊なリージョンは次のとおりです。

* **米国政府バージニア**および **US Gov アイオワ**
  * 物理的および論理的にネットワークが分離された Azure インスタンス。米国の政府機関やパートナー (選別された米国の担当者が運営する) 向け。 [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP)、[DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA) などのその他のコンプライアンス証明書が含まれます。 詳細については [Azure Government](https://azure.microsoft.com/features/gov/) に関するページをご覧ください。
* **中国東部**および**中国北部**
  * これらのリージョンは、Microsoft と 21Vianet 間の特異なパートナーシップを通じてご利用いただけます。この契約により、Microsoft はデータ センターを直接管理しません。 [Azure China 21Vianet](https://www.windowsazure.cn/) の詳細を参照してください。
* **ドイツ中部**および**ドイツ北東部**
  * これらのリージョンは、データ トラスティ モデルを通してご利用いただけます。お客様のデータは、ドイツのデータ トラスティを務める Deutsche Telekom 傘下の T-Systems の管理の下、ドイツ国内で保管されます。

## <a name="region-pairs"></a>リージョンのペア
各 Azure リージョンは、同じ Geo 内 (米国、ヨーロッパ、アジアなど) の別のリージョンと組み合わせて使用します。 こうすることで、1 つの Geo のリソース (VM ストレージなど) のレプリケーションを考慮し、自然災害、暴動、停電、または両方のリージョンに同時に影響を与える物理的なネットワーク障害が発生する可能性を小さくします。 リージョンのペアには他にも次の利点があります。

* Azure の障害の規模が大きい場合に、各ペアの一方のリージョンを優先することで、アプリケーションの復元時間短縮を図ることができます。 
* 計画的な Azure の更新をペアになっているリージョンの 1 つずつにロールアウトすることで、ダウンタイムとアプリケーション停止のリスクを最小限に抑えことができます。
* 税および法の執行を目的として、同じ Geo に常にペアとしてデータが存在します (ブラジル南部を除く)。

リージョンのペアの例は次のとおりです。

| プライマリ | セカンダリ |
|:--- |:--- |
| 米国西部 |米国東部 |
| 北ヨーロッパ |西ヨーロッパ |
| 東南アジア |東アジア |

リージョンのペアの完全な一覧は [こちら](../best-practices-availability-paired-regions.md#what-are-paired-regions)で確認できます。

## <a name="feature-availability"></a>使用可能な機能
特定の VM サイズやストレージ タイプなど、一部のサービスや VM 機能は特定のリージョンのみでご利用いただけます。 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)、[Traffic Manager](../traffic-manager/traffic-manager-overview.md)、[Azure DNS](../dns/dns-overview.md) など、特定のリージョンを選択する必要がないグローバル Azure サービスもあります。 アプリケーション環境の設計をスムーズにするために、 [各リージョンの Azure サービスの可用性のページ](https://azure.microsoft.com/regions/#services)をご確認ください。 [各リージョンでサポートされている VM のサイズと制限をプログラムでクエリする](../azure-resource-manager/templates/error-sku-not-available.md)こともできます。

## <a name="storage-availability"></a>ストレージの可用性
Azure のリージョンと Geo を理解することは、使用可能なストレージのレプリケーション オプションを検討する際に重要になります。 ストレージのタイプに応じて、さまざまなレプリケーションのオプションがあります。

**Azure Managed Disks**
* ローカル冗長ストレージ (LRS)
  * ストレージ アカウントが作成されたリージョン内のデータを 3 回レプリケートします。

**ストレージ アカウント ベース ディスク**
* ローカル冗長ストレージ (LRS)
  * ストレージ アカウントが作成されたリージョン内のデータを 3 回レプリケートします。
* ゾーン冗長ストレージ (ZRS)
  * 1 つのリージョン内または 2 つのリージョンまたがって、2 ～ 3 つの施設でデータを 3 回レプリケートします。
* geo 冗長ストレージ (GRS)
  * プライマリ リージョンから数百マイル離れたセカンダリ リージョンにデータをレプリケートします。
* 読み取りアクセス geo 冗長ストレージ (RA-GRS)
  * GRS と同様にセカンダリ リージョンにデータをレプリケートしますが、2 次拠点のデータに対する読み取り専用アクセスも提供します。

次の表に、ストレージ レプリケーションの種類の違いの簡単な概要を示します。

| レプリケーションの方法 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 複数施設にわたってのデータのレプリケート |いいえ |はい |はい |はい |
| 1 次拠点と 2 次拠点からデータの読み取りが可能 |いいえ |いいえ |いいえ |はい |
| 個別のノードで保持されるデータ コピーの数 |3 |3 |6 |6 |

詳細については、 [Azure ストレージのレプリケーション オプションのページ](../storage/common/storage-redundancy.md)を参照してください。 マネージド ディスクの詳細については、「[Azure Managed Disks の概要](./managed-disks-overview.md)」をご覧ください。

### <a name="storage-costs"></a>ストレージ コスト
価格は、ストレージの種類と選択した可用性によって異なります。

**Azure Managed Disks**
* Premium Managed Disks はソリッド ステート ドライブ (SSD) を使用し、Standard Managed Disks は標準のスピニング ディスクを使用します。 Premium Managed Disks と Standard Managed Disks はどちらもディスク用にプロビジョニングされた容量に基づいて課金されます。

**非管理対象ディスク**
* Premium ストレージはソリッド ステート ドライブ (SSD) を使用し、ディスクの容量に基づいて課金されます。
* Standard ストレージは通常のスピニング ディスクにバックアップされ、使用中の容量と希望するストレージの可用性に基づいて課金されます。
  * RA-GRS では、別の Azure リージョンへのデータのレプリケーションにかかる帯域幅に対して、Geo レプリケーション データ転送料金がかかります。

さまざまなストレージの種類と可用性オプションの料金情報については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

## <a name="next-steps"></a>次のステップ

詳細については、「[Azure のリージョン](https://azure.microsoft.com/global-infrastructure/regions/)」をご覧ください。