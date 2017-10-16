---
title: "Azure Site Recovery とは | Microsoft Docs"
description: "Azure Site Recovery サービスの概要について説明し、デプロイ シナリオについてまとめます。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-site-recovery"></a>Site Recovery とは

Azure Site Recovery サービスへようこそ。 この記事では、サービスの概要を簡単に説明します。

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Azure Recovery Services を使用したビジネス継続性とディザスター リカバリー (BCDR)

機能が停止したときに、それが計画されたものであれ、計画外のものであれ、どのような方法でデータの安全性を確保し、アプリやワークロードの実行状態を維持するかは、組織内で明確化しておく必要があります。

Azure Recovery Services は、組織の BCDR 戦略に活かすことができます。

- **Site Recovery サービス**: Site Recovery の利点は、サイトがダウンした場合でも VM や物理サーバー上で実行されているアプリの可用性が維持されて、ビジネス継続性が確保されることです。 VM や物理サーバー上で実行されているワークロードは、プライマリ サイトが利用できなくなった場合でもセカンダリ ロケーションで利用できるように、Site Recovery によってレプリケートされます。 ワークロードは、プライマリ サイトが稼働状態に戻った時点でプライマリ サイトに復元されます。
- **Backup サービス**: さらに、[Azure Backup](https://docs.microsoft.com/azure/backup/) サービスによってデータが Azure にバックアップされることで、データの安全性と回復可能性が確保されます。

Site Recovery で対応できるレプリケーションは次のとおりです。

- Azure リージョン間で Azure VM をレプリケートする。
- オンプレミスの仮想マシンと物理サーバーを Azure (またはセカンダリ サイト) にレプリケートする。


## <a name="what-does-site-recovery-provide"></a>Site Recovery で提供されるもの

**機能** | **詳細**
--- | ---
**単純な BCDR ソリューションのデプロイ** | Azure Portal から Site Recovery を使用して、レプリケーションとフェールオーバー、単一拠点からのフェールバックを設定、管理することができます。
**Azure VM のレプリケート** | Azure リージョン間で Azure VM をレプリケートするように BCDR 戦略を策定することができます。
**オンプレミスからオフサイトへの VM のレプリケート** | オンプレミスの VM や物理サーバーは、Azure にレプリケートすることも、オンプレミスのセカンダリ ロケーションにレプリケートすることもできます。 Azure へのレプリケートにより、セカンダリ データセンターの管理に伴うコストと手間が削減されます。
**すべてのワークロードのレプリケート** | サポートされている Azure VM、オンプレミスの Hyper-V VM、VMware VM、Windows/Linux 物理サーバーで実行されているあらゆるワークロードをレプリケートできます。
**データの復元性と安全性の維持** | Site Recovery は、アプリケーション データをインターセプトせずに、レプリケーションを調整します。 レプリケートされたデータは、元の復元性を備えた状態で Azure Storage に格納されます。 フェールオーバーが発生すると、レプリケートされたデータに基づいて Azure VM が作成されます。
**RTO と RPO の達成** | 目標復旧時間 (RTO) と回復ポイントの目標 (RPO) を組織の制限内に保ちます。 Azure VM と VMware VM に関しては連続レプリケーションが、Hyper-V では 30 秒ごとのレプリケーション頻度が実現されています。 [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) との統合により目標復旧時間 (RTO) をさらに短縮することができます。
**フェールオーバー中にアプリの整合性を維持** | アプリケーション コンシステントなスナップショットで復旧ポイントを構成することができます。 アプリケーション コンシステントなスナップショットでは、ディスク データ、メモリ内の全データ、処理中の全トランザクションがキャプチャ対象となります。
**中断を伴わないテスト** | ディザスター リカバリーの演習を支援するテスト フェールオーバーを簡単に実行できます。実行中のレプリケーションに影響が生じることはありません。
**柔軟性の高いフェールオーバーの実行** | 予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない障害の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。 プライマリ サイトには、そのサイトが再度使用できるようになった時点で簡単にフェールバックできます。
**復旧計画の作成** | 復旧計画を使用して、複数の VM 上に存在する多層アプリケーションのフェールオーバーと復旧をカスタマイズしたり順序を指定したりすることができます。 複数のマシンを計画内でグループ化し、スクリプトや手動アクションを追加します。 復旧計画は、Azure Automation Runbook と統合できます。
**既存の BCDR テクノロジとの統合** | Site Recovery は、他の BCDR テクノロジと統合できます。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、可用性グループのフェールオーバーを管理するために SQL Server AlwaysOn のネイティブ サポートも提供されます。
**自動化ライブラリとの統合** | 豊富な Azure Automation ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。
**ネットワーク設定の管理** | Site Recovery は、アプリケーションのネットワーク管理 (IP アドレスの予約、ロード バランサーの構成、Azure Traffic Manager の統合による効率的なネットワーク切り替えを含む) を簡素化するために、Azure と統合できます。


## <a name="what-can-i-replicate"></a>レプリケート対象

**サポートされています** | **詳細**
--- | ---
**レプリケート対象** | Azure VM を Azure リージョン間でレプリケートできます (プレビュー)。<br/><br/>  オンプレミスの VMware VM、Hyper-V VM、物理サーバー (Windows および Linux) を Azure にレプリケートできます。<br/<br/> オンプレミスの VMware VM、Hyper-V VM、物理サーバーをセカンダリ サイトにレプリケートできます。 Hyper-V VM に関して、セカンダリ サイトへのレプリケーションがサポートされるのは、Hyper-V ホストが System Center VMM で管理されている場合に限られます。
**Site Recovery のサポート対象のリージョン** | [サポートされているリージョン](https://azure.microsoft.com/regions/services/) |
**レプリケート対象のマシンで必要なオペレーティング システム** | [Azure VM の要件](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>[VMware VM の要件](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Hyper-V VM の場合、Azure でサポートされている[ゲスト OS](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) と Hyper-V がサポートされています。<br/><br/> [物理サーバーの要件](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**必要な VMware サーバー/ホスト** | VMware VM は、[サポートされている vSphere ホスト/vCenter サーバー](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)に配置してください。
**レプリケートできるワークロード** | サポートされているレプリケーション マシンで実行されている任意のワークロードをレプリケートできます。 さらに、Site Recovery チームは、[いくつかのアプリ](site-recovery-workload.md#workload-summary)に対してアプリに固有のテストを行っています。


## <a name="azure-portal-considerations"></a>Azure Portal に関する考慮事項

* Site Recovery は、[Azure Portal](https://portal.azure.com) でデプロイすることができます。
* Azure クラシック ポータルでは、クラシック サービス管理モデルで Site Recovery を管理できます。
- クラシック ポータルは、既存の Site Recovery デプロイを維持する目的でのみ使用します。 クラシック ポータルで新しいコンテナーを作成することはできません。

## <a name="next-steps"></a>次のステップ
* [ワークロードのサポート](site-recovery-workload.md)の詳細を確認する。
* [リージョン間の Azure VM のレプリケーション](site-recovery-azure-to-azure.md)、[Azure への VMware のレプリケーション](vmware-walkthrough-overview.md)、[Azure への Hyper-V のレプリケーション](hyper-v-site-walkthrough-overview.md)の概要を確認する。
