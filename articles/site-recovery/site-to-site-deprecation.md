---
title: Azure Site Recovery を使用したカスタマー マネージド サイト間での (VMM による) ディザスター リカバリーの廃止 | Microsoft Docs
description: Hyper-V を使用しているお客様所有のサイト間の DR と SCVMM によって Azure で管理されているサイト間の DR の廃止予定と代替オプションの詳細
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421843"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Azure Site Recovery を使用したカスタマー マネージド サイト間での (VMM による) ディザスター リカバリーの廃止

この記事では、予定されている廃止計画、付随する影響、および次のシナリオに対してお客様が利用できる代替オプションについて説明します。

Site Recovery を使用する System Center Virtual Machine Manager (SCVMM) によって管理されているお客様所有のサイト間の DR

> [!IMPORTANT]
> お客様は、環境の中断を回避するために、できるだけ早く修復手順を実行することをお勧めします。 

## <a name="what-changes-should-you-expect"></a>予期される変更

- 将来廃止となる Hyper-V VM のサイト間レプリケーションについては、2020 年 3 月以降、Azure portal の通知および電子メールを通じてお知らせする予定です。 廃止は、2023 年 3 月を予定しています。

- 既存の構成がある場合でも、その設定への影響はありません。

- シナリオが非推奨となると、お客様が代替アプローチに従っていない限り、既存のレプリケーションが中断される可能性があります。 お客様は、Azure portal で Azure Site Recovery エクスペリエンスを使用して、DR 関連の操作を表示、管理、または実行することができなくなります。
 
## <a name="alternatives"></a>代替 

シナリオが非推奨になったときに DR 戦略に影響が出ないようにするためにお客様が選択可能な代替手段を次に示します。 

- オプション 1 (推奨): [DR ターゲットとして Azure の使用を開始する](hyper-v-vmm-azure-tutorial.md)ことを選択します。


- オプション 2: サイト間レプリケーションを、基本的な [Hyper-Hyper-V レプリカ ソリューション](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)を使用して続行することを選択します。ただし、Azure portal で Azure Site Recovery を使用して DR 構成を管理することはできません。 


## <a name="remediation-steps"></a>修復ステップ

オプション 1 を選択する場合は、次のステップを実行してください。

1. [VMMs に関連付けられているすべての仮想マシンの保護を無効にします](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 **[レプリケーションの無効化]** を使用してオプションを削除するか、前述のスクリプトを実行して、レプリケーション設定のオンプレミスがクリーン アップされるようにします。 

2. サイト間レプリケーションの構成から[すべての VMM サーバーを登録解除](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)します。

3. VM のレプリケーションを有効にするために、[Azure リソースを準備](tutorial-prepare-azure-for-hyperv.md)します。
4. [オンプレミスの Hyper-V を準備する](hyper-v-prepare-on-premises-tutorial.md)
5. [VMM クラウド内の VM のレプリケーションを設定する](hyper-v-vmm-azure-tutorial.md)
6. 省略可能だが推奨: [DR ドリルを実行する](tutorial-dr-drill-azure.md)

Hyper-V レプリカを使用するオプション 2 を選択する場合は、次の手順を実行してください。

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
2. **[レプリケーションの無効化]** で、**[削除]** を選択します。

    これを使用すると、レプリケートされたアイテムが Azure Site Recovery から削除されます (請求は停止されます)。 オンプレミスの仮想マシン上のレプリケーション構成はクリーンアップ **されません**。 

## <a name="next-steps"></a>次のステップ
廃止に備えた計画を立て、ご自身のインフラストラクチャとビジネスに最適な代替オプションを選択します。 これに関してご質問がある場合は、Microsoft サポートまでお問い合わせください

