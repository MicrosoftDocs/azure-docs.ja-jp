---
title: "Azure Site Recovery を使用してセカンダリ オンプレミス サイトへのディザスター リカバリー訓練を実行する | Microsoft Docs"
description: "Azure Site Recovery を使用した セカンダリ オンプレミス サイトへのディザスター リカバリー訓練の実行について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 445878e2-6682-49ba-914d-4c6824ab08a6
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew
ms.openlocfilehash: 8737c2a22bd729cfc15d5448e1ec0becef643fd5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-your-secondary-on-premises-site"></a>Hyper-V VM のセカンダリ オンプレミス サイトへのディザスター リカバリー訓練を実行する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのコンピューターと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、Hyper-V VM のセカンダリ オンプレミス サイトへのディザスター リカバリー訓練を実行する方法を示します。 Hyper-V VM は、System Center Virtual Machine Manager (VMM) プライベート クラウドで管理する必要があります。 訓練ではデータ損失やダウンタイムなしでレプリケーション戦略を検証します。これは運用環境には影響しません。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * テスト フェールオーバーの前提条件を確認する
> * 1 台のコンピューターのテスト フェールオーバーを実行する


## <a name="prerequisites"></a>前提条件

- テスト フェールオーバーは、セカンダリ サイトのネットワークに関するオプションを指定して実行できます。 フェールオーバーは、ネットワークなしで、または既存のネットワークを使用して実行できます。Site Recovery によってテスト ネットワークが自動的に作成されるようにすることもできます。 
**テスト フェールオーバーで運用環境の既存のネットワークを使用する場合:**:
    - テスト フェールオーバーの実行中は、プライマリ VM をシャット ダウンする必要があります。 そうしないと、同じ ID を持つ 2 つの仮想マシンが同じネットワークで同時に実行されることになります。 
    - テスト VM に加えた変更は、テスト フェールオーバーのクリーンアップを行うと失われます。 これらの変更は、プライマリ VM にはレプリケートされません。
    - 運用環境のネットワークでテストを行うと、運用環境のワークロードでダウンタイムが発生します。 ディザスター リカバリー訓練の実施中は、ユーザーにアプリを使用しないように依頼してください。 
- レプリカ テスト ネットワークは、テスト フェールオーバーで使用される VMM 論理ネットワークの種類と一致する必要はありません。 ただし、いくつかの組み合わせは機能しません。 たとえば、レプリカで DHCP と VLAN ベースの分離を使用している場合、テスト フェールオーバーのネットワークで Windows ネットワーク仮想化を使用することはできません。これは IP アドレス プールが必要であるためです。 
- ネットワーク マッピング用に選択したネットワークはテスト フェールオーバーでは使用しないことをお勧めします。


## <a name="run-a-test-failover-for-a-vm"></a>VM のテスト フェールオーバーの実行

1. **[レプリケートされたアイテム]** で、VM をクリックし、**[テスト フェールオーバー]** をクリックします。
2. **[テスト フェールオーバー]** で、テスト フェールオーバー後にテスト VM をネットワークに接続する方法を指定します。 このチュートリアルでは、Site Recovery によってテスト ネットワークが自動的に作成されるようにすることをお勧めします。 [詳細情報](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)
3. **[OK]** をクリックすると、フェールオーバーが開始されます。 ジョブの進捗状況を **[ジョブ]** タブで追跡します。
4. フェールオーバーが完了したら、テスト VM が正常に起動することを確認します。
5. 完了したら、**[テスト フェールオーバーのクリーンアップ]** をクリックします。 これにより、テスト VM とテスト フェールオーバー中に作成されたすべてのネットワークが削除されます。
6. **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 


## <a name="next-steps"></a>次のステップ

[運用環境のフェールオーバーを実行する](tutorial-vmm-to-vmm-failover-failback.md)






