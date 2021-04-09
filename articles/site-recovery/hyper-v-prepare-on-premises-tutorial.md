---
title: Azure Site Recovery を使用して Hyper-V VM から Azure へのディザスター リカバリーを準備する
description: Azure Site Recovery を使用して、オンプレミス Hyper-V VM の Azure へのディザスター リカバリーを準備する方法について説明します。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f24a259d2d71aa6599f6dd417d5e9fc99734e99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135662"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Azure へのディザスター リカバリーのためにオンプレミス Hyper-V サーバーを準備する

この記事では、Hyper-VM の Azure へのディザスター リカバリーを設定するときに、[Azure Site Recovery](site-recovery-overview.md) を使用してオンプレミスの Hyper-V インフラストラクチャを準備する方法を説明します。


これは、オンプレミスの Hyper-V VM のディザスター リカバリーを Azure に設定する方法について説明するシリーズの 2 番目のチュートリアルです。 1 番目のチュートリアルでは、Hyper-V のディザスター リカバリーに必要な [Azure コンポーネントを設定](tutorial-prepare-azure.md)しました。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Hyper-V 要件と、Hyper-V ホストが System Center VMM によって管理されている場合は、VMM 要件を確認する。
> * 該当する場合は VMM を準備する。
> * Azure の場所へのインターネット アクセスを確認する。
> * Azure にフェールオーバーした後に VM にアクセスできるように VM を準備する。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 可能であれば既定のオプションを使い、すべての可能な設定とパスを示してはいません。 詳細な手順については、Site Recovery の目次のハウツー セクションにある記事を参照してください。

## <a name="before-you-start"></a>開始する前に

Azure が[このシリーズの最初のチュートリアル](tutorial-prepare-azure.md)で説明されているように準備されていることを確認します。

## <a name="review-requirements-and-prerequisites"></a>要件と前提条件を確認する

Hyper-V ホストと VM が要件に準拠していることを確認します。

1. オンプレミスのサーバーの要件を[確認](hyper-v-azure-support-matrix.md#on-premises-servers)します。
2. Azure にレプリケートする Hyper-V VM の[要件を確認](hyper-v-azure-support-matrix.md#replicated-vms)します。
3. Hyper-V ホストの[ネットワーク](hyper-v-azure-support-matrix.md#hyper-v-network-configuration)と、ホストおよびゲスト [ストレージ](hyper-v-azure-support-matrix.md#hyper-v-host-storage)がオンプレミスの Hyper-V ホストをサポートしていることを確認します。
4. [Azure のネットワーク](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover)、[ストレージ](hyper-v-azure-support-matrix.md#azure-storage)、[コンピューティング](hyper-v-azure-support-matrix.md#azure-compute-features)に関して、フェールオーバー後のサポートをチェックします。
5. Azure にレプリケートするオンプレミスの VM は、「[Azure VM の要件](hyper-v-azure-support-matrix.md#azure-vm-requirements)」に準拠している必要があります。


## <a name="prepare-vmm-optional"></a>VMM を準備する (省略可能)

Hyper-V ホストが VMM で管理される場合、オンプレミス VMM サーバーを用意する必要があります。 

- VMM サーバーに少なくとも 1 つのクラウドと、1 つまたは複数のホスト グループがあることを確認します。 VM を実行する Hyper-V ホストはクラウドに置いてください。
- ネットワーク マッピング用に VMM サーバーを準備します。

### <a name="prepare-vmm-for-network-mapping"></a>ネットワーク マッピング用に VMM を準備する

VMM を使用している場合、[ネットワーク マッピング](./hyper-v-vmm-network-mapping.md)は、オンプレミス VMM VM ネットワークと Azure 仮想ネットワークの間でマッピングを行います。 マッピングによって、フェールバック後に作成されるとき、Azure VM は正しいネットワークに接続されます。

次の手順に従って、ネットワーク マッピング用に VMM を準備します。

1. Hyper-V ホストが置かれているクラウドに関連付けられている [VMM 論理ネットワーク](/system-center/vmm/network-logical)があることを確認します。
2. 論理ネットワークにリンクされている [VM ネットワーク](/system-center/vmm/network-virtual)があることを確認します。
3. VMM で、VM を VM ネットワークに接続します。

## <a name="verify-internet-access"></a>インターネット アクセスを確認する

1. このチュートリアルの目的として、最も簡単な構成は、Hyper-V ホストと VMM サーバーがプロキシを使用せずにインターネットに直接アクセスする方法です。 
2. Hyper-V ホストと VMM サーバー (関連する場合) が下の必須 URL にアクセスできることを確認します。   
3. IP アドレスでアクセスをコントロールしている場合は、次のことを確認してください。
    - IP アドレスベースのファイアウォール ルールが [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)および HTTPS (443) ポートに接続できること。
    - お使いのサブスクリプションの Azure リージョンの IP アドレス範囲を許可します。
    
### <a name="required-urls"></a>必須 URL


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

フェールオーバーのシナリオでは、レプリケートされたオンプレミス ネットワークに接続することがあります。

フェールオーバー後に RDP を使用して Windows VM に接続するには、次のアクセスを許可します。

1. インターネット経由でアクセスするには、フェールオーバーの前に、オンプレミスの VM 上の RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、 **[Windows ファイアウォール]**  >  **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
2. サイト間 VPN 経由でアクセスするには、オンプレミスのコンピューターで RDP を有効にします。 RDP は、 **[Windows ファイアウォール]**  ->  **[許可されたアプリおよび機能]** から、**ドメインとプライベート** ネットワークでの使用を許可する必要があります。
   オペレーティング システムの SAN ポリシーが **[OnlineAll]** に設定されていることを確認します。 [詳細については、こちらを参照してください](https://support.microsoft.com/kb/3031135)。 フェールオーバーをトリガーするときに、VM に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、仮想マシンにサインインすることはできません。
3. フェールオーバー後の Microsoft Azure VM で **[ブート診断]** をオンにして、VM のスクリーンショットを確認します。 接続できない場合は、VM が実行中であることを確認したうえで、[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認してください。

フェールオーバー後、Azure VM には、レプリケートされたオンプレミス VM と同じ IP アドレスか、別の IP アドレスを使用してアクセスできます。 フェールオーバー用の IP アドレスの設定については、[こちら](concepts-on-premises-to-azure-networking.md)を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure にオンプレミス Hyper-V VM のディザスター リカバリーを設定する](./hyper-v-azure-tutorial.md)
> [VMM クラウドに Hyper-V VM の Azure へのディザスター リカバリーを設定する](./hyper-v-vmm-azure-tutorial.md)
