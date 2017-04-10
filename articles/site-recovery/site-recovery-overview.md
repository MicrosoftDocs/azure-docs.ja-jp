---
title: "Site Recovery とは | Microsoft Docs"
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
ms.date: 03/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: d8e4e4bb7dd1e40d8c561adba04b8346fcb2127d
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-site-recovery"></a>Site Recovery とは

Azure Site Recovery サービスへようこそ。 この記事では、サービスの概要を簡単に説明します。

停止が発生する原因は、自然現象や動作障害です。 組織には、予定されたダウンタイムおよび予定外のダウンタイム時にデータを保護し、アプリケーションの可用性を維持して、ビジネスをできるだけ早く通常の状態に復旧させるために、ビジネス継続性および障害復旧 (BCDR) の戦略が必要です。

Azure Recovery Services は BCDR 戦略を支援します。 [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) サービスは、データを保護し、回復可能な状態に維持します。 Site Recovery は、ワークロードをレプリケート、フェールオーバー、回復することで、障害発生時のデータの可用性を維持します。

## <a name="what-does-site-recovery-provide"></a>Site Recovery で提供されるもの

- **クラウドでの障害復旧** - VM や物理サーバーで実行されているワークロードを、セカンダリ サイトではなく Azure にレプリケートできます。 これにより、セカンダリ データセンターの管理に伴うコストと手間が削減されます。
- **ハイブリッド環境向けの柔軟なレプリケーション** - サポートされているオンプレミスの Hyper-V VM、VMware VM、Windows/Linux 物理サーバーで実行されているワークロードをレプリケートできます。
- **移行** - Site Recovery を使用すると、オンプレミスの AWS インスタンスを Azure VM に移行できます。また、Azure リージョン間で Azure VM を移行することもできます。
- **BCDR の簡素化** - Azure Portal の&1; つの場所からレプリケーションをデプロイできます。  1 つまたは複数のマシンのシンプルなフェールオーバーとフェールバックを実行できます。
- **復元性** - Site Recovery は、アプリケーション データをインターセプトせずに、レプリケーションとフェールオーバーを調整します。
レプリケートされたデータは、元の復元性を備えた状態で Azure Storage に格納されます。 フェールオーバーが発生すると、レプリケートされたデータに基づいて Azure VM が作成されます。
- **レプリケーションのパフォーマンス** - Site Recovery は、Hyper-V では 30 秒のレプリケーション頻度を提供し、VMware では連続レプリケーションを実現します。 目標復旧時点 (RPO) のしきい値を設定して、データの復旧ポイントを作成する頻度を制御できます。また、Site Recovery の自動回復プロセス、および [Azure Traffic Manager](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) との統合により、目標復旧時間 (RTO) を短縮できます。
- **アプリケーションの整合性** - マシンでは、アプリケーションの整合性スナップショットを使用してレプリケーションを実行します。 アプリケーションの整合性スナップショットは、ディスク データをキャプチャするだけでなく、メモリのすべてのデータとプロセスのすべてのトランザクションもキャプチャします。
- **中断なしのテスト** - 運用環境に影響を与えずに、障害復旧の演習をサポートするテスト フェールオーバーを簡単に実行できます。
- **柔軟なフェールオーバーと復旧** - 予期された停止の場合は、データ損失ゼロの計画されたフェールオーバーを実行できます。予期しない障害が発生した場合は、(レプリケーションの頻度に応じて) データ損失を最小限に抑えて計画外のフェールオーバーを実行できます。 プライマリ サイトには、そのサイトが再度使用できるようになった時点で簡単にフェールバックできます。
- **カスタマイズされた復旧計画** - 復旧計画を使用すると、複数の VM に分散する多層アプリケーションのフェールオーバーと復旧をモデル化およびカスタマイズできます。 計画内のグループに順序付けし、スクリプトと手動アクションを追加します。 復旧計画は、Azure Automation Runbook と統合できます。
- **多層アプリケーション** - 多層アプリケーションの順序付けられたフェールオーバーと復旧の復旧計画を作成できます。 復旧計画内でさまざまな層 (データベース、Web、アプリケーションなど) のマシンをグループ化し、各グループのフェールオーバーと起動の方法をカスタマイズできます。
* **既存の BCDR テクノロジとの統合** - Site Recovery は、他の BCDR テクノロジと統合されています。 たとえば、Site Recovery を使用すると、企業のワークロードの SQL Server バックエンドを保護でき、可用性グループのフェールオーバーを管理するために SQL Server AlwaysOn のネイティブ サポートも提供されます。
* **Automation ライブラリとの統合** - 豊富な Azure Automation ライブラリには、運用環境ですぐに使えるアプリケーション固有のスクリプトが用意されています。これらのスクリプトをダウンロードし、Site Recovery と統合できます。
* **シンプルなネットワーク管理** - Site Recovery と Azure の高度なネットワーク管理により、IP アドレスの予約、ロード バランサーの構成、効率的なネットワーク切り替えを実現する Azure Traffic Manager の統合など、アプリケーション ネットワーク要件が簡略化されます。


## <a name="whats-supported"></a>サポート対象

**サポートされています** | **詳細**
--- | ---
**Site Recovery のサポート対象のリージョン** | [サポートされているリージョン](https://azure.microsoft.com/en-us/regions/services/) |
**レプリケート対象** | オンプレミスの VMware VM、Hyper-V VM、Windows および Linux 物理サーバー。
**レプリケート対象のマシンで必要なオペレーティング システム** | VMware VM の[サポートされているオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Hyper-V VM の場合、Azure でサポートされている[ゲスト OS](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) と Hyper-V がサポートされています。<br/><br/> 物理サーバーの[オペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**レプリケート先** | Azure ストレージ、またはセカンダリ データセンター<br/><br/> Hyper-V の場合、セカンダリ データセンターにレプリケートできるのは、System Center VMM クラウドで管理されている Hyper-V ホスト上の VM だけです。
**必要な VMware サーバー/ホスト** | レプリケートする VMware VM は、[サポートされている vSphere ホスト/vCenter サーバー](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)によって管理できます。
**レプリケートできるワークロード** | サポートされているレプリケーション マシンで実行されている任意のワークロードをレプリケートできます。 さらに、Site Recovery チームは、[いくつかのアプリ](site-recovery-workload.md#workload-summary)に対してアプリに固有のテストを行っています。


## <a name="which-azure-portal"></a>Azure Portal とは

* Site Recovery は、新しい [Azure Portal](https://portal.azure.com) と [Azure クラシック ポータル](https://manage.windowsazure.com/)のどちらでもデプロイできます。
* Azure クラシック ポータルでは、クラシック サービス管理モデルで Site Recovery をサポートできます。
* Azure Portal では、クラシック モデルをサポートすることも、新しい [Resource Manager デプロイ モデル](../azure-resource-manager/resource-manager-deployment-model.md)をサポートすることもできます。
- クラシック ポータルは、既存の Site Recovery デプロイを維持する目的でのみ使用します。 クラシック ポータルで新しいコンテナーを作成することはできません。

## <a name="next-steps"></a>次のステップ
* [ワークロードのサポート](site-recovery-workload.md)の詳細を確認する。
* [Site Recovery のアーキテクチャとコンポーネント](site-recovery-components.md)の詳細を確認する。

