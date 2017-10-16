---
title: "Azure Site Recovery を使用した Azure への VMware VM のレプリケーションを有効にする | Microsoft Docs"
description: "Azure Site Recovery サービスを使用した Azure への VMware VM のレプリケーションを有効にするために必要な手順の概要を示します"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 470b9ddd8df4a4e74ec7174f79020c252323e502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-enable-replication-for-vmware-virtual-machines-to-azure"></a>手順 11: Azure への VMware 仮想マシンのレプリケーションを有効にする


この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure へのオンプレミスの VMware 仮想マシンのレプリケーションを有効にする方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="before-you-start"></a>開始する前に

- VMware VM には[モビリティ サービス コンポーネントがインストールされている](vmware-walkthrough-install-mobility.md)必要があります。 - VM をプッシュ インストール用に準備した場合は、レプリケーションを有効にすると、プロセス サーバーでモビリティ サービスが自動的にインストールされます。
- Azure ユーザー アカウントには、VM を Azure にレプリケートできるようにするための特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。
- VM を追加または変更するときに、変更が有効になってポータルに表示されるまでに 15 分以上かかることがあります。
- VM の最終検出時刻は、**[構成サーバー]** > **[前回のアクセス]** で確認できます。
- 定期検出を待たずに VM を追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** をクリックします。



## <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。 [詳細情報](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>VM をレプリケートする

始める前に、簡単なビデオ概要を見ます。

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。
2. **[ソース]** で、構成サーバーを選択します。
3. **[マシンの種類]** で、**[仮想マシン]** を選択します。
4. **[vCenter/vSphere Hypervisor] \(vCenter/vSphere ハイパーバイザー)** で、vSphere ホストを管理する vCenter サーバーを選択するか、ホストを選択します。
5. プロセス サーバーを選択します。 追加のプロセス サーバーを作成していない場合、これは構成サーバーになります。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/vmware-walkthrough-enable-replication/enable-replication2.png)

6. **[ターゲット]** で、サブスクリプションと、フェールオーバー対象の VM を作成するリソース グループを選択します。 Azure で、フェールオーバー対象の VM に使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。


7. データのレプリケーションに使用する Azure Storage アカウントを選択します。 既にセットアップしたアカウントを使用しない場合は、新しいアカウントを作成できます。

8. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 既存のネットワークを使用しない場合は、ネットワークを作成することができます。

    ![Enable replication](./media/vmware-walkthrough-enable-replication/enable-rep3.png)
9. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/vmware-walkthrough-enable-replication/enable-replication5.png)
10. **[プロパティ]** >  の **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。
11. 既定では、すべてのディスクがレプリケートされます。 **[すべてのディスク]** をクリックし、レプリケートしないディスクをオフにします。 次に、 **[OK]**をクリックします 後で追加の VM プロパティを設定できます。

    ![Enable replication](./media/vmware-walkthrough-enable-replication/enable-replication6.png)
11. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 ポリシーを変更する場合、変更はレプリケートしているマシンと新しいマシンに適用されます。
12. マシンをレプリケーション グループにまとめる場合は、**[マルチ VM 整合性]** を有効にし、グループの名前を指定します。 次に、 **[OK]**をクリックします 以下の点に注意してください。

    * レプリケーション グループのマシンはまとめてレプリケートされ、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを共有します。
    * VM と物理サーバーがワークロードをミラー化できるように、これらをまとめることをお勧めします。 マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響を及ぼす可能性があるので、複数のマシンが同じワークロードを実行しており、整合性が必要な場合にのみ使用します。

    ![Enable replication](./media/vmware-walkthrough-enable-replication/enable-replication7.png)
13. **[レプリケーションを有効にする]**をクリックします。 **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。

## <a name="next-steps"></a>次のステップ

[手順 12: テスト フェールオーバーを実行する](vmware-walkthrough-test-failover.md)方法に関するページに進む
