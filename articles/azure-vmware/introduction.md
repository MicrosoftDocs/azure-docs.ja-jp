---
title: はじめに
description: Azure で VMware ベースのワークロードをデプロイして管理するための、Azure VMware Solution の機能とメリットについて学習します。
ms.topic: overview
ms.date: 05/04/2020
ms.openlocfilehash: 3d6801a6cdec7600cc6003711a08e6af8c86483e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005056"
---
# <a name="what-is-azure-vmware-solution-preview"></a>Azure VMware Solution プレビューとは

Azure VMware Solution は、Azure でプライベート クラウドを提供します。 プライベート クラウドには、専用のベアメタル Azure インフラストラクチャから構築された vSphere クラスターが含まれます。 プライベート クラウド クラスターは、3 台から 16 台のホストに拡張できます。また、1 つのプライベート クラウドに複数のクラスターを含めることができます。 すべてのプライベート クラウドには、vCenter Server、vSAN、vSphere、NSX-T がプロビジョニングされます。 オンプレミス環境からワークロードを移行し、新しい仮想マシンを作成またはデプロイし、プライベート クラウドから Azure サービスを利用できます。

Azure VMware Solution は、VMware によって検証されたソリューションで、拡張機能とアップグレードが継続的に検証およびテストされます。 プライベート クラウドのインフラストラクチャとソフトウェアは Microsoft によって管理および保守されるため、ユーザーはプライベート クラウドでのワークロードの開発と実行に専念できます。

次の図は、Azure、Azure サービス、およびオンプレミス環境におけるプライベート クラウドと VNet の間の隣接性を示しています。 プライベート クラウドから Azure サービスまたは VNet へのネットワーク アクセスにより、SLA に基づく Azure サービス エンドポイントの統合が提供されます。 オンプレミス環境からのプライベート クラウド アクセスでは、ExpressRoute Global Reach を使用して、プライベートでセキュリティで保護された接続を実現します。

![Azure とオンプレミスへの Azure VMware Solution プライベート クラウドの隣接性の画像](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>ホスト、クラスター、およびプライベート クラウド

Azure VMware Solution プライベート クラウドとクラスターは、ベアメタルのハイパーコンバージド Azure インフラストラクチャ ホストから構築されます。 ハイエンドのホストには、576 GB の RAM と 2 基の Intel 18 コア 2.3 GHz プロセッサが搭載されています。 HE ホストには、2 つの vSAN ディスクグループがあり、合計 15.36 TB (SSD) の未加工の vSAN 容量階層と 3.2 TB (NVMe) の vSAN キャッシュ階層があります。

新しいプライベート クラウドは、Azure portal または Azure CLI を介してデプロイされます。

## <a name="networking"></a>ネットワーク

プライベート クラウドがデプロイされると、管理、プロビジョニング、および vMotion 用のプライベート ネットワークが作成されます。 これらのプライベート ネットワークは、vCenter および NSX-T Manager へのアクセス、および仮想マシンの vMotion またはデプロイに使用されます。 すべてのプライベート ネットワークには、Azure の VNet またはオンプレミス環境からアクセスできます。 ExpressRoute Global Reach は、プライベート クラウドをオンプレミス環境に接続するために使用されます。この接続には、お使いのサブスクリプションに ExpressRoute 回路を備えた VNet が必要です。

インターネットおよび Azure サービスへのアクセスは、プライベート クラウドのデプロイにプロビジョニングされます。 アクセスが提供されると、運用ワークロード ネットワーク上の VM が Azure またはインターネットベースのサービスを利用できるようになります。 インターネット アクセスは、新しいプライベート クラウドでは既定で無効になっています。これはいつでも有効または無効にできます。

ネットワークと相互接続の詳細については、[ネットワークの概念](concepts-networking.md)に関する記事を参照してください。

## <a name="access-and-security"></a>アクセスとセキュリティ

Azure VMware Solution プライベート クラウドでは、セキュリティを強化するために、vSphere のロールベースのアクセス制御が使用されます。 vSphere SSO LDAP 機能は、Azure Active Directory と統合できます。 ID と特権の詳細については、[アクセスと ID の概念](concepts-identity.md)に関する記事を参照してください。

vSAN の保存データの暗号化は既定で有効になっており、vSAN データストアのセキュリティを提供するために使用されます。 詳細については、[ストレージの概念](concepts-storage.md)に関する記事を参照してください。

## <a name="host-and-software-lifecycle-maintenance"></a>ホストとソフトウェアのライフサイクル メンテナンス

Azure VMware Solution プライベート クラウドと VMware ソフトウェアを定期的にアップグレードすることで、お使いのプライベート クラウドで最新のセキュリティ、安定性、および機能セットが確保されます。 プラットフォームのメンテナンスとアップグレードの詳細については、[アップグレードの概念](concepts-upgrades.md)に関する記事を参照してください。

## <a name="monitoring-your-private-cloud"></a>プライベート クラウドの監視

[Azure Monitor のログ](../azure-monitor/overview.md)を使用して、対象の Azure VMware Solution プライベート クラウドで実行されている仮想マシンのログを収集できます。 オンプレミス VM で実行するのと同じクエリを使用して、Azure VMware Solution プライベート クラウドで実行されている Linux および Windows 仮想マシンに [MMA エージェントをダウンロードしてインストール](../azure-monitor/platform/log-analytics-agent.md#installation-options)できます。 仮想マシンで通常実行するのと同じクエリをまったく同じように実行できます。 クエリの作成の詳細については、[クエリの作成方法](../azure-monitor/log-query/log-query-overview.md#how-can-i-learn-how-to-write-queries)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

次の手順では、重要な[プライベート クラウドとクラスターの概念](concepts-private-clouds-clusters.md)について学習します。

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
