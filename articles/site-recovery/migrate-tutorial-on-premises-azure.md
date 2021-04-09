---
title: Azure Migrate を使用してオンプレミスのマシンを移行する
description: この記事では、オンプレミスのマシンを Azure に移行する方法について概説したうえで、Azure Migrate をお勧めします。
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.author: raynew
ms.openlocfilehash: e0e60ee346d20113b2ec7970390d9874522cc770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87847314"
---
# <a name="migrate-on-premises-machines-to-azure"></a>オンプレミスのマシンを Azure に移行する

この記事では、オンプレミスのマシンを Azure に移行するオプションについて説明します。 

## <a name="migrate-with-azure-migrate"></a>Azure Migrate を使用した移行

マシンを Azure に移行する際は、[Azure Migrate](../migrate/migrate-services-overview.md) サービスを使用することをお勧めします。 Azure Migrate は、サーバーの移行専用に設計されています。 Azure Migrate は、オンプレミスのマシンの検出、評価、および Azure への移行を行うための中心的なハブを提供します。

Azure Migrate で移行する場合は、次のリンクを参照してください。

- VMware VM の移行オプションについて[学習](../migrate/server-migrate-overview.md)し、[エージェントレス](../migrate/tutorial-migrate-vmware.md)または[エージェントを使用した](../migrate/tutorial-migrate-vmware-agent.md)移行を使用して VM を Azure に移行します。
- [Hyper-V VM](../migrate/tutorial-migrate-hyper-v.md) を Azure に移行します。
- [AWS インスタンス](../migrate/tutorial-migrate-aws-virtual-machines.md)を含む、[物理サーバーまたはその他の VM](../migrate/tutorial-migrate-physical-virtual-machines.md) を Azure に移行します。

## <a name="migrate-with-site-recovery"></a>Site Recovery を使用した移行
Site Recovery は、移行でなく、ディザスター リカバリーのみに使用する必要があります。

Azure Site Recovery を既に使用していて、移行にもそれを引き続き使用したい場合は、ディザスター リカバリーで使用したのと同じ手順を実行します。

- VMware VM:[Azure](tutorial-prepare-azure.md) と [VMware](vmware-azure-tutorial-prepare-on-premises.md) を準備し、[マシンのレプリケート](vmware-azure-tutorial.md)を開始し、すべてが正常に機能していることを[確認](tutorial-dr-drill-azure.md)し、[フェールオーバーを実行](vmware-azure-tutorial-failover-failback.md)します。
- Hyper-V VM:[Azure](tutorial-prepare-azure-for-hyperv.md) と [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) を準備し、[マシンのレプリケート](hyper-v-azure-tutorial.md)を開始し、すべてが正常に機能していることを[確認](tutorial-dr-drill-azure.md)し、[フェールオーバーを実行](hyper-v-azure-failover-failback-tutorial.md)します。
- 物理サーバー:[チュートリアル](physical-azure-disaster-recovery.md)に従い Azure を準備し、ディザスター リカバリー用のマシンを準備し、レプリケーションを設定します。

> [!NOTE]
> ディザスター リカバリーのためにフェールオーバーを実行する場合、最後の手順としてフェールオーバーをコミットします。 オンプレミスのマシンを移行する場合は、 **[コミット]** オプションは使用しません。 代わりに、 **[移行の完了]** オプションを選択します。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> Azure Migrate について[よく寄せられる質問を確認](../migrate/resources-faq.md)する。

  
