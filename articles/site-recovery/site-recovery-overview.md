---
title: Azure Site Recovery について
description: Azure Site Recovery サービスの概要を説明し、ディザスター リカバリーと移行デプロイのシナリオについてまとめています。
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: 19860e64182cd73fe9f9fa1246f440a03109d465
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92281871"
---
# <a name="about-site-recovery"></a>Site Recovery について

Azure Site Recovery サービスへようこそ。 この記事では、サービスの概要を簡単に説明します。

組織は、計画済みまたは計画外の停止が発生した場合のために、お使いのデータの安全性を確保し、お使いのアプリやワークロードをオンラインにし、ビジネス継続性とディザスター リカバリー (BCDR) の戦略を導入する必要があります。

Azure Recovery Services はあなたの BCDR 戦略を支援します。

- **Site Recovery サービス**: Site Recovery により、機能停止中でもビジネス アプリとワークロードの実行状態を維持することで、ビジネス継続性を確保できます。 Site Recovery は、物理マシンと仮想マシン (VM) で実行中のワークロードを、プライマリ サイトからセカンダリ ロケーションにレプリケートします。 プライマリ サイトで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーし、そこからアプリにアクセスします。 プライマリの場所が再度実行中になったら、そこにフェールバックできます。
- **Backup サービス**: [Azure Backup](../backup/index.yml) サービスは、データを保護し、回復可能な状態に維持します。

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
**柔軟性の高いフェールオーバー** | 計画されたフェールオーバーでは、データの損失なしで計画フェールオーバーを実行できます。 また、計画外フェールオーバーでは、レプリケーション頻度に応じて、データの損失を最小限に抑えることができます。 プライマリ サイトには、そのサイトが再度使用できるようになった時点で簡単にフェールバックできます。
**復旧計画のカスタマイズ** | 復旧計画を使用すると、複数の VM で実行される多層アプリケーションのフェールオーバーと復旧をカスタマイズしたり、順序を指定したりすることができます。 複数のマシンを復旧計画内でグループ化し、必要に応じてスクリプトや手動アクションを追加します。 復旧計画は、Azure Automation Runbook と統合できます。
**BCDR の統合** | Site Recovery は、他の BCDR テクノロジと統合できます。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、可用性グループのフェールオーバーを管理するために SQL Server AlwaysOn のネイティブ サポートも提供されます。
**Azure Automation の統合** | 豊富な Azure Automation ライブラリに、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトは、ダウンロードして Site Recovery と統合できます。
**ネットワーク統合** | Site Recovery は、アプリケーション ネットワークの管理のために Azure に統合できます。 たとえば、IP アドレスの予約、ロードバランサーの構成、ネットワークの効率的な切り替えのための Azure Traffic Manager の使用が可能です。

## <a name="what-can-i-replicate"></a>レプリケート対象

**サポート状況** | **詳細**
--- | ---
**レプリケーション シナリオ** | Azure VM を 1 つの Azure リージョンから別のリージョンにレプリケートします。<br/><br/>  オンプレミスの VMware VM、Hyper-V VM、物理サーバー (Windows および Linux)、Azure Stack VM を Azure にレプリケートします。<br/><br/> AWS Windows インスタンスを Azure にレプリケートします。<br/><br/> オンプレミスの VMware VM、System Center VMM で管理されている HYPER-V VM、および物理サーバーを、セカンダリ サイトにレプリケートします。
**リージョン** | Site Recovery の[サポートされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery)を確認してください。 |
**レプリケートされたマシン** | [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) レプリケーション、[オンプレミスの VMware VM と物理サーバー](vmware-physical-azure-support-matrix.md#replicated-machines)、および[オンプレミスの Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms) のレプリケーション要件を確認してください。
**ワークロード** | レプリケーションがサポートされているマシンで実行されている任意のワークロードをレプリケートできます。 そして、Site Recovery チームでは、[多数のアプリ](site-recovery-workload.md#workload-summary)に対してアプリ固有のテストを行っています。

## <a name="next-steps"></a>次のステップ

- [ワークロードのサポート](site-recovery-workload.md)の詳細を確認します。
- [リージョン間での Azure VM レプリケーション](azure-to-azure-quickstart.md)の使用を開始します。
