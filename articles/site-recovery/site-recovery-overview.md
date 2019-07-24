---
title: Azure Site Recovery について | Microsoft Docs
description: Azure Site Recovery サービスの概要を説明し、ディザスター リカバリーと移行デプロイのシナリオについてまとめています。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: overview
ms.date: 03/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 3121c7e4b1b1adfb079368aa86c272e9ea473ead
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57855216"
---
# <a name="about-site-recovery"></a>Site Recovery について

Azure Site Recovery サービスへようこそ。 この記事では、サービスの概要を簡単に説明します。

組織では、機能が停止したときに、それが計画されたものであれ、計画外のものであれ、データの安全性を確保し、アプリやワークロードの実行状態を維持する、ビジネス継続性とディザスター リカバリー (BCDR) の戦略を導入する必要があります。

Azure Recovery Services は、組織の BCDR 戦略に活かすことができます。

- **Site Recovery サービス**:Site Recovery により、機能停止中でもビジネス アプリとワークロードの実行状態を維持することで、ビジネス継続性を確保できます。 Site Recovery は、物理マシンと仮想マシン (VM) で実行中のワークロードを、プライマリ サイトからセカンダリ ロケーションにレプリケートします。 プライマリ サイトで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーし、そこからアプリにアクセスします。 プライマリの場所が再度実行中になったら、そこにフェールバックできます。  
- **Backup サービス**:[Azure Backup](https://docs.microsoft.com/azure/backup/) サービスによってデータが Azure にバックアップされることで、データの安全性と回復可能性が確保されます。

Site Recovery で対応できるレプリケーションは次のとおりです。

- Azure リージョン間で Azure VM をレプリケートする。
- オンプレミス VM、Azure Stack VM、物理サーバー。


## <a name="what-does-site-recovery-provide"></a>Site Recovery で提供されるもの


**機能** | **詳細**
--- | ---
**単純な BCDR ソリューション** | Azure Portal から Site Recovery を使用して、レプリケーションとフェールオーバー、単一拠点からのフェールバックを設定、管理することができます。
**Azure VM レプリケーション** | プライマリ リージョンからセカンダリ リージョンへの Azure VM のディザスター リカバリーを設定できます。
**オンプレミスの VM レプリケーション** | オンプレミスの VM や物理サーバーは、Azure にレプリケートすることも、オンプレミスのセカンダリ データセンターにレプリケートすることもできます。 Azure へのレプリケートにより、セカンダリ データセンターの管理に伴うコストと手間が削減されます。
**ワークロードのレプリケーション** | サポートされている Azure VM、オンプレミスの Hyper-V VM、VMware VM、Windows/Linux 物理サーバーで実行されているあらゆるワークロードをレプリケートできます。
**データの回復力** | Site Recovery は、アプリケーション データをインターセプトせずに、レプリケーションを調整します。 Azure にレプリケートすると、データは元の復元性を備えた状態で Azure Storage に格納されます。 フェールオーバーが発生すると、レプリケートされたデータに基づいて Azure VM が作成されます。
**RTO と RPO のターゲット** | 目標復旧時間 (RTO) と目標復旧時点 (RPO) を組織の制限内に保ちます。 Azure VM と VMware VM に関しては連続レプリケーションが、Hyper-V では 30 秒ごとのレプリケーション頻度が実現されています。 [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) との統合により RTO をさらに短縮することができます。
**フェールオーバー中にアプリの整合性を維持** | アプリケーション コンシステントなスナップショットで使用中の復旧ポイントをレプリケートできます。 これらのスナップショットでは、ディスク データ、メモリ内の全データ、処理中の全トランザクションがキャプチャ対象となります。
**中断を伴わないテスト** | ディザスター リカバリーの演習を、実行中のレプリケーションに影響を与えずに、簡単に実行できます。
**柔軟性の高いフェールオーバー** | 予期された停止の場合はデータ損失ゼロの計画されたフェールオーバーを実行し、予期しない障害の場合は (レプリケーションの頻度に応じた) 最小限のデータ損失で計画外のフェールオーバーを実行することもできます。 プライマリ サイトには、そのサイトが再度使用できるようになった時点で簡単にフェールバックできます。
**復旧計画のカスタマイズ** | 復旧計画を使用して、複数の VM で実行される多層アプリケーションのフェールオーバーと復旧をカスタマイズしたり、順序を指定したりすることができます。 複数のマシンを復旧計画内でグループ化し、必要に応じてスクリプトや手動アクションを追加します。 復旧計画は、Azure Automation Runbook と統合できます。
**BCDR の統合** | Site Recovery は、他の BCDR テクノロジと統合できます。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、可用性グループのフェールオーバーを管理するために SQL Server AlwaysOn のネイティブ サポートも提供されます。
**Azure Automation の統合** | 豊富な Azure Automation ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。
**ネットワーク統合** | Site Recovery は、アプリケーションのネットワーク管理 (IP アドレスの予約、ロード バランサーの構成、Azure Traffic Manager の統合による効率的なネットワーク切り替えを含む) を簡素化するために、Azure と統合できます。


## <a name="what-can-i-replicate"></a>レプリケート対象

**サポートされています** | **詳細**
--- | ---
**レプリケーション シナリオ** | Azure VM を 1 つの Azure リージョンから別のリージョンにレプリケートします。<br/><br/>  オンプレミスの VMware VM、Hyper-V VM、物理サーバー (Windows および Linux)、Azure Stack VM を Azure にレプリケートします。<br/><br/> オンプレミスの VMware VM、System Center VMM で管理されている HYPER-V VM、および物理サーバーを、セカンダリ サイトにレプリケートします。
**リージョン** | Site Recovery の[サポートされているリージョン](https://azure.microsoft.com/regions/services/)を確認してください。 |
**レプリケートされたマシン** | [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) レプリケーション、[オンプレミスの VMware VM と物理サーバー](vmware-physical-azure-support-matrix.md#replicated-machines)、および[オンプレミスの Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms) のレプリケーション要件を確認してください。
**ワークロード** | レプリケーションがサポートされているマシンで実行されている任意のワークロードをレプリケートできます。 さらに、Site Recovery チームは、[いくつかのアプリ](site-recovery-workload.md#workload-summary)に対してアプリに固有のテストを行っています。



## <a name="next-steps"></a>次の手順
* [ワークロードのサポート](site-recovery-workload.md)の詳細を確認します。
* [リージョン間での Azure VM レプリケーション](azure-to-azure-quickstart.md)の使用を開始します。 
