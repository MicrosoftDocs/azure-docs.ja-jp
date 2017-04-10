---
title: "ビジネス継続性と障害復旧 (BCDR): Azure のペアになっているリージョン | Microsoft Docs"
description: "データセンターでの障害発生時にアプリケーションの耐障害性を確保するための Azure のリージョン ペアについて説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: c2d0a21c-2564-4d42-991a-bc31723f61a4
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 43e0bb0c921be3992321d0a202fce1fa09053f99
ms.openlocfilehash: 67edd136737e6af372c3c898c34a8bc6681a8c6d
ms.lasthandoff: 02/21/2017

---

# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>ビジネス継続性と障害復旧 (BCDR): Azure のペアになっているリージョン

## <a name="what-are-paired-regions"></a>ペアになっているリージョンとは

Azure は、世界中の複数の geo で動作します。 Azure の geo とは、少なくとも&1; つの Azure リージョンを含む、世界の定義済みの地域です。 Azure リージョンは、geo に含まれる領域で、1 つ以上のデータセンターが含まれます。

各 Azure リージョンは、同じ geo 内の別のリージョンと組み合わせて、リージョン ペアにして使用します。 例外はブラジル南部で、geo の外部のリージョンとペアになっています。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

図 1 – Azure リージョン ペアの図

| [地理的な場所] | ペアになっているリージョン |  |
|:--- |:--- |:--- |
| 北米 |米国中北部 |米国中南部 |
| 北米 |米国東部 |米国西部 |
| 北米 |米国東部 2 |米国中部 |
| 北米 |米国西部 2 |米国中西部 |
| ヨーロッパ |北ヨーロッパ |西ヨーロッパ |
| アジア |東南アジア |東アジア |
| 中国 |中国東部 |中国北部 |
| 日本 |東日本 |西日本 |
| ブラジル |ブラジル南部 (1) |米国中南部 |
| オーストラリア |オーストラリア東部 |オーストラリア南東部 |
| 米国政府 |米国政府アイオワ州 |米国政府バージニア州 |
| インド |インド中部 |インド南部 |
| カナダ |カナダ中部 |カナダ東部 |
| 英国 |英国西部 |英国南部 |

表 1 - Azure リージョン ペアの組み合わせ

> (1) ブラジル南部は、自身の geo 外のリージョンとペアになっているため特殊です。 ブラジル南部のセカンダリ リージョンは米国中南部ですが、米国中南部のセカンダリ リージョンはブラジル南部ではありません。


リージョン ペアの間でワークロードをレプリケートして、Azure の分離と可用性のポリシーを活用することをお勧めします。 たとえば、計画的な Azure システムの更新プログラムは、ペア リージョンに (同時にではなく) 順番にデプロイされます。 つまり、更新プログラムに不具合があっても (めったにありませんが)、両方のリージョンが同時に影響を受けることはありません。 さらに、万一、広範囲にわたって障害が発生した場合は、すべてのペアにおいて、少なくとも一方のリージョンの復旧が優先されます。

## <a name="an-example-of-paired-regions"></a>ペアになっているリージョンの例
以下の図 2 は、一対のリージョンを使って障害復旧を行う架空のアプリケーションです。 緑色の番号は、3 つの Azure サービス (Azure コンピューティング、ストレージ、およびデータベース) のリージョン間アクティビティと、そのアクティビティが、リージョン間でのレプリケートのためにどのように構成されているかを示しています。 リージョン ペアにデプロイするメリットは、オレンジ色の番号で示されています。

![ペア リージョンのメリットの概要](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

図 2 – Azure リージョン ペアの例

## <a name="cross-region-activities"></a>リージョン間アクティビティ
図 2 を参照してください。

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (PaaS)** – 障害発生時に他のリージョンでリソースを確実に使用できるように、追加の計算リソースを事前にプロビジョニングする必要があります。 詳細については、「[Azure の回復性技術ガイダンス](resiliency/resiliency-technical-guidance.md)」を参照してください。

![Storage](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** - Azure Storage アカウントの作成時に、geo 冗長ストレージ (GRS) が既定で構成されます。 GRS を使用すると、データはプライマリ リージョン内で&3; 回、ペア リージョンで&3; 回、自動的にレプリケートされます。 詳細については、「 [Azure Storage 冗長オプション](storage/storage-redundancy.md)」をご覧ください。

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – Azure SQL Standard geo レプリケーションを使用すると、対になっているリージョンへのトランザクションの非同期レプリケーションを構成できます。 Premium geo レプリケーションを使用すると、世界中のすべてのリージョンへのレプリケーションを構成できますが、通常の障害復旧では、これらのリソースをペア リージョンにデプロイすることをお勧めします。 詳細については、[Azure SQL Database の geo レプリケーション](sql-database/sql-database-geo-replication-overview.md)に関するページを参照してください。

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** - ARM では本質的に、リージョン全体のサービス管理コンポーネントが論理的に切り離されています。 つまり、1 つのリージョンで論理的な障害が発生しても、他のリージョンが影響を受ける可能性はそれほど高くありません。

## <a name="benefits-of-paired-regions"></a>ペアになっているリージョンのメリット
図 2 を参照してください。  

![分離](./media/best-practices-availability-paired-regions/5Orange.png)
**物理的な分離** – Azure が推奨するリージョン内の各データセンター間の距離は 300 マイル (480 km) ですが、これは一部の地域では現実的ではなく、実現できないこともあります。 物理データセンターを切り離すことで、自然災害、社会不安、停電、および物理ネットワーク障害が両方のリージョンにすぐに影響を及ぼす可能性が少なくなります。 分離は、geo 内の制約 (広さ、電源/ネットワーク インフラストラクチャの可用性、規制など) に左右されます。  

![レプリケーション](./media/best-practices-availability-paired-regions/6Orange.png)
**プラットフォームに備わっているレプリケーション** - geo 冗長ストレージなど一部のサービスには、ペア リージョンへの自動レプリケーション機能が用意されています。

![復旧](./media/best-practices-availability-paired-regions/7Orange.png)
**リージョン復旧順序** – 広範囲にわたって障害が発生した場合は、すべてのペアで一方のリージョンの復旧が優先されます。 ペア リージョンにまたがってデプロイされているアプリケーションについては、そのアプリケーションのリージョンのどちらかが必ず優先的に復旧します。 ペアになっていない複数のリージョンにアプリケーションがデプロイされていると、復旧が遅れる可能性があり、最悪の場合は、回復されてない最後の&2; つになってしまうことがあります。

![更新](./media/best-practices-availability-paired-regions/8Orange.png)
**順次更新** – 計画的な Azure システム更新プログラムは、ダウンタイム、バグの影響、および更新プログラムの不具合 (まれではありますが) による論理的な障害を最小限に抑えるために、ペア リージョンに (同時にではなく) 順番に展開されます。

![データ](./media/best-practices-availability-paired-regions/9Orange.png)
**データ常駐** – リージョンは、税および法の執行を目的としたデータ常駐要件を満たすために、ペアとして同じ geo に常駐しています (ブラジル南部を除く)。

