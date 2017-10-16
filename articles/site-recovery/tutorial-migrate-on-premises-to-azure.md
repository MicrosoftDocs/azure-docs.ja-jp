---
title: "Azure Site Recovery を使用してオンプレミスのマシンを Azure に移行する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して、オンプレミスのマシンを Azure に移行する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.openlocfilehash: bbee77e669f49bdebb57121df8672a9253945b3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>オンプレミスのマシンを Azure に移行する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのマシンおよび Azure の仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックを管理し、調整します。

このチュートリアルでは、Site Recovery を使用して、オンプレミスの VM と物理サーバーを Azure に移行する方法を説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * デプロイの前提条件を設定する
> * Site Recovery の Recovery Services コンテナーを作成する
> * オンプレミスの管理サーバーをデプロイする
> * レプリケーション ポリシーを設定して、レプリケーションを有効にする
> * ディザスター リカバリーのテストを実行して、すべてが正常に機能することを確認する
> * Azure へのワンタイム フェールオーバーを実行する

## <a name="overview"></a>概要

マシンを移行するには、そのレプリケーションを有効にして、Azure にフェールオーバーします。


## <a name="prerequisites"></a>前提条件

このチュートリアルでは、次のことを実行する必要があります。

- Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントなどの Azure リソースを[準備します](tutorial-prepare-azure.md)。
- VMware オンプレミス VMware サーバーと VM を[準備します](tutorial-prepare-on-premises-vmware.md)。
- 準仮想化ドライバーによってエクスポートされたデバイスは、サポートされないことに注意してください。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。
1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。
3. **[保護の目標]** で、次のように選択します。
    - **VMware**: **[To Azure]\(Azure へ\)** > **[Yes, with VMware vSphere Hypervisor]\(はい、VMware vSphere ハイパーバイザーを使用する\)** の順に選択します。
    - **物理マシン**: **[To Azure]\(Azure へ\)** > **[非仮想化/その他]** の順に選択します。
    - **Hyper-V**: **[Azure へ]** > **[Yes, with Hyper-V]\(はい、Hyper-V を使用する\)** を選択します。


## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

- VMware VM のソース環境を[設定します](tutorial-vmware-to-azure.md#set-up-the-source-environment)。
- 物理サーバーのソース環境を[設定します](tutorial-physical-to-azure.md#set-up-the-source-environment)。
- Hyper-V VM のソース環境を[設定します](tutorial-hyper-v-to-azure.md#set-up-the-source-environment)。

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲットのデプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

## <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

- VMware VM の[レプリケーション ポリシーを設定します](tutorial-vmware-to-azure.md#create-a-replication-policy)。


## <a name="enable-replication"></a>Enable replication

- VMware VM の [レプリケーションを有効にします](tutorial-vmware-to-azure.md#enable-replication)。


## <a name="run-a-test-migration"></a>テスト移行を実行する

Azure への[テスト フェールオーバー](tutorial-dr-drill-azure.md)を実行して、すべて想定どおりに動作していることを確認します。


## <a name="migrate-to-azure"></a>Azure への移行

移行するマシンのフェールオーバーを実行します。

1. **[設定]** > **[レプリケートされたアイテム]** で、[マシン] > **[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で、フェールオーバーする**復旧ポイント**を選択します。 最新の復旧ポイントを選択します。
3. 暗号化キー設定は、このシナリオには関係しません。
4. Site Recovery でフェールオーバーを開始する前にそのソース仮想マシンをシャットダウンする場合は、**[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。
5. 想定どおりに Azure VM が Azure に表示されることを確認します。
6. **[レプリケートされたアイテム]** で [VM] > **[移行の完了]** を右クリックします。 これによって、移行プロセスが終了し、VM のレプリケーションが停止して、その VM での Site Recovery の課金が停止します。

    ![移行の完了](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **進行中のフェールオーバーを取り消さないでください**。フェールオーバーが開始する前に VM のレプリケーションが停止します。 進行中のフェールオーバーを取り消すと、フェールオーバーは停止しますが、VM は二度とレプリケートされません。

一部のシナリオでは、フェールオーバーが完了するまでに、さらに約 8 ～ 10 分の処理が必要です。 物理サーバー、VMware Linux マシン、DHCP サービスが有効でない VMware VM、ブート ドライバー storvsc、vmbus、storftt、intelide、atapi を持たない VMware VM については、テスト フェールオーバーの時間がさらに長くなる場合もあります。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure への移行後に Azure VM を他のリージョンにレプリケートする](site-recovery-azure-to-azure-after-migration.md)
