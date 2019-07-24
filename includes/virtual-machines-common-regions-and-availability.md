---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: zr-msft
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: include file
ms.openlocfilehash: fb296236fb73823690175b12f4e07c05b60cdbcf
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334034"
---
# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Azure の仮想マシンのリージョンと可用性について
Azure は、世界中の複数のデータセンターで動作しています。 これらのデータセンターは、地理的なリージョンにグループ化されていて、アプリケーションの作成場所を選択するときの柔軟性を与えています。 Azure で仮想マシン (VM) がどこで、どのように運用されているかを理解するのは重要なことです。また、パフォーマンス、可用性、冗長性を最大化するためのオプションとしてどのようなものがあるかを知っておくことにも、大きな意義があります。 この記事では、可用性と冗長性に関する Azure の各種機能の概要を紹介します。

## <a name="what-are-azure-regions"></a>Azure リージョンとは?
Azure のリソースは、"米国西部"、"北ヨーロッパ"、"東南アジア" などの定義された地理的リージョンで作成します。 [リージョンとその場所の一覧](https://azure.microsoft.com/regions/)をご確認ください。 各リージョンには、冗長性と可用性を確保するために複数のデータ センターが存在します。 こうすることで、アプリケーションを柔軟に設計でき、ユーザーに最も近い場所に VM を作成したり、法律、コンプライアンス、税金などの各種の目的に合致させたりすることができます。

## <a name="special-azure-regions"></a>特殊な Azure リージョン
Azure には、コンプライアンスまたは法的な目的に合致したアプリケーションを構築する際に使用できる特別なリージョンがいくつかあります。 特殊なリージョンは次のとおりです。

* **米国政府バージニア**および **US Gov アイオワ**
  * 物理的および論理的にネットワークが分離された Azure インスタンス。米国の政府機関やパートナー (選別された米国の担当者が運営する) 向け。 [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP)、[DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA) などのその他のコンプライアンス証明書が含まれます。 詳細については [Azure Government](https://azure.microsoft.com/features/gov/) に関するページをご覧ください。
* **中国東部**および**中国北部**
  * これらのリージョンは、Microsoft と 21Vianet 間の特異なパートナーシップを通じてご利用いただけます。この契約により、Microsoft はデータ センターを直接管理しません。 詳細については、[Microsoft Azure の中国語ページ](http://www.windowsazure.cn/)をご覧ください。
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

リージョンのペアの完全な一覧は [こちら](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions)で確認できます。

## <a name="feature-availability"></a>使用可能な機能
特定の VM サイズやストレージ タイプなど、一部のサービスや VM 機能は特定のリージョンのみでご利用いただけます。 [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md)、[Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md)、[Azure DNS](../articles/dns/dns-overview.md) など、特定のリージョンを選択する必要がないグローバル Azure サービスもあります。 アプリケーション環境の設計をスムーズにするために、 [各リージョンの Azure サービスの可用性のページ](https://azure.microsoft.com/regions/#services)をご確認ください。 [各リージョンでサポートされている VM のサイズと制限をプログラムでクエリする](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md)こともできます。

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
| 複数施設にわたってのデータのレプリケート |いいえ  |可能  |はい |はい |
| 1 次拠点と 2 次拠点からデータの読み取りが可能 |いいえ  |いいえ  |いいえ  |はい |
| 個別のノードで保持されるデータ コピーの数 |3 |3 |6 |6 |

詳細については、 [Azure ストレージのレプリケーション オプションのページ](../articles/storage/common/storage-redundancy.md)を参照してください。 マネージド ディスクの詳細については、「[Azure Managed Disks の概要](../articles/virtual-machines/windows/managed-disks-overview.md)」をご覧ください。

### <a name="storage-costs"></a>ストレージのコスト
価格は、ストレージの種類と選択した可用性によって異なります。

**Azure Managed Disks**
* Premium Managed Disks はソリッド ステート ドライブ (SSD) を使用し、Standard Managed Disks は標準のスピニング ディスクを使用します。 Premium Managed Disks と Standard Managed Disks はどちらもディスク用にプロビジョニングされた容量に基づいて課金されます。

**非管理対象ディスク**
* Premium ストレージはソリッド ステート ドライブ (SSD) を使用し、ディスクの容量に基づいて課金されます。
* Standard ストレージは通常のスピニング ディスクにバックアップされ、使用中の容量と希望するストレージの可用性に基づいて課金されます。
  * RA-GRS では、別の Azure リージョンへのデータのレプリケーションにかかる帯域幅に対して、Geo レプリケーション データ転送料金がかかります。

さまざまなストレージの種類と可用性オプションの料金情報については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

## <a name="availability-sets"></a>可用性セット
可用性セットはデータセンター内の VM の論理グループで、これによって Azure は、冗長性と可用性を提供するためにアプリケーションが構築された方法を理解することができます。 高可用性アプリケーションを提供し、[99.95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) に適合するために、1 つの可用性セット内に 2 つ以上の VM を作成することをお勧めします。 可用性セット自体にはコストはかかりません。料金は、作成した各 VM インスタンスに対してのみ発生します。 1 つの VM が [Azure Premium SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd) を使用している場合、Azure SLA は計画外メンテナンス イベントに適用されます。

可用性セットは、ハードウェアの障害から保護する障害ドメイン (FD) と、更新の安全な適用を可能にする更新ドメイン (UD) という 2 つの追加グループで構成されています。 [Linux VM](../articles/virtual-machines/linux/manage-availability.md) または [Windows VM](../articles/virtual-machines/windows/manage-availability.md) の可用性を管理する方法については、詳細をお読みください。

### <a name="fault-domains"></a>障害ドメイン
障害ドメインは、オンプレミスのデータ センター内のラックのように、共通の電源やネットワーク スイッチを共有する基盤となるハードウェアの論理グループです。 可用性セット内に作成した VM は、Azure プラットフォームにより自動で複数の障害ドメインに分散して配布されます。 これにより、物理ハードウェアの障害、ネットワークの停止、または停電が発生した場合の影響を抑えることが可能になります。

### <a name="update-domains"></a>更新ドメイン
更新ドメインは、メンテナンスや再起動が同時に行われる可能性のある、基盤となるハードウェアの論理グループです。 可用性セット内に作成した VM は、Azure プラットフォームにより自動で複数の更新ドメインに分散して配布されます。 これにより、Azure プラットフォームに定期メンテナンスを実施している間もアプリケーションのインスタンスが常に 1 つは稼働している状態を確保することができます。 計画的メンテナンス中は、更新ドメインの再起動が順番に処理されない場合がありますが、一度に再起動される更新ドメインは 1 つのみです。

![可用性セット](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>管理ディスクの障害ドメイン
[Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) を使用している VM の場合、VM は管理対象の可用性セットを使用している場合にマネージド ディスクの障害ドメインに合わせて配置されます。 この配置により、VM に接続されたすべてのマネージド ディスクは必ず同じマネージド ディスクの障害ドメイン内にあります。 管理対象の可用性セットには、マネージド ディスクを持つ VM だけを作成できます。 マネージド ディスクの障害ドメインの数はリージョンによって異なり、マネージド ディスクの障害ドメインはリージョンあたり 2 つまたは 3 つになります。 [Linux VM](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) または [Windows VM](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) のこれらのマネージド ディスク障害ドメインの詳細を参照してください。

![可用性セットの管理](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>可用性ゾーン

[可用性ゾーン](../articles/availability-zones/az-overview.md)は可用性セットに代わる機能で、よりきめ細やかな制御により VM 上のアプリケーションとデータの可用性を維持します。 可用性ゾーンとは、Azure リージョンの物理的に独立したゾーンのことです。 サポートされている Azure リージョンごとに 3 つの可用性ゾーンがあります。 可用性ゾーンはそれぞれ異なる電源、ネットワーク、および冷却装置を持ちます。 複数のゾーンにレプリケートされた VM を使用するソリューションを構築することで、1 つのデータセンターで障害が発生してもアプリケーションとデータを保護することができます。 1 つのゾーンが侵害された場合、レプリケートされたアプリとデータが別のゾーンですぐに利用可能になります。 

![可用性ゾーン](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

可用性ゾーンに [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) または [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) の VM をデプロイする方法の詳細をご覧ください。

## <a name="next-steps"></a>次の手順
可用性と冗長性の機能を使って、Azure 環境を構築できるようになりました。 ベスト プラクティスに関する情報については、[Azure の可用性のベスト プラクティス](../articles/best-practices-availability-checklist.md)に関するページをご覧ください。

