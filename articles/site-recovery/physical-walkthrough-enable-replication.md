---
title: "Azure Site Recovery を使用して Azure にレプリケートされる物理サーバーのレプリケーションを有効にする | Microsoft Docs"
description: "Azure Site Recovery サービスを使用した Azure への物理サーバーのレプリケーションを有効にするために必要な手順の概要を示します"
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
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>手順 10: Azure への物理サーバーのレプリケーションを有効にする


この記事では、Azure ポータルで [Azure Site Recovery](site-recovery-overview.md) サービスを使用した Azure へのオンプレミスの Windows/Linux 物理サーバーのレプリケーションを有効にする方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="before-you-start"></a>開始する前に

- サーバーに[モビリティ サービス コンポーネントがインストールされている](physical-walkthrough-install-mobility.md)必要があります。
- VM をプッシュ インストール用に準備した場合は、レプリケーションを有効にすると、プロセス サーバーでモビリティ サービスが自動的にインストールされます。
- レプリケーション用のマシンを有効にするときは、Azure Storage の使用と Azure VM の作成を確実に実行するために、Azure ユーザー アカウントに特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。
- サーバーの IP アドレスを追加または変更した場合、変更が有効になってポータルに表示されるまでに最大 15 分かかる可能性があります。


## <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。 [詳細情報](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>サーバーをレプリケートする

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。
2. **[ソース]** で、構成サーバーを選択します。
3. **[マシンの種類]** で、**[物理マシン]** を選択します。
4. プロセス サーバーを選択します。 追加のプロセス サーバーを作成していない場合、これは構成サーバーになります。 次に、 **[OK]**をクリックします
5. **[ターゲット]** で、サブスクリプションと、フェールオーバー対象の VM を作成するリソース グループを選択します。 Azure で、フェールオーバー対象の VM に使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。
6. データのレプリケーションに使用する Azure Storage アカウントを選択します。 既にセットアップしたアカウントを使用しない場合は、新しいアカウントを作成できます。
7. フェールオーバー後に Azure VM が接続する **Azure ネットワーク**と**サブネット**を選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 既存のネットワークを使用しない場合は、ネットワークを作成することができます。

    ![Enable replication](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. **[物理マシン]** で **[+ 物理マシン]** をクリックし、**名前**と **IP アドレス**を入力します。 レプリケートするマシンのオペレーティング システムを選択します。 マシンが検出されて一覧に表示されるまでには数分かかります。
9. **[プロパティ]** >  の **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。
10. 既定では、すべてのディスクがレプリケートされます。 **[すべてのディスク]** をクリックし、レプリケートしないディスクをオフにします。 次に、 **[OK]**をクリックします 後で追加の VM プロパティを設定できます。

    ![Enable replication](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 ポリシーを変更する場合、変更はレプリケートしているマシンと新しいマシンに適用されます。
12. マシンをレプリケーション グループにまとめる場合は、**[マルチ VM 整合性]** を有効にし、グループの名前を指定します。 次に、 **[OK]**をクリックします 以下の点に注意してください。

    * レプリケーション グループのマシンはまとめてレプリケートされ、フェールオーバー時にクラッシュ整合性復旧ポイントとアプリ整合性復旧ポイントを共有します。
    * 物理サーバーがワークロードをミラー化できるように、これらをまとめることをお勧めします。 マルチ VM 整合性を有効にすると、ワークロードのパフォーマンスに影響を及ぼす可能性があるので、複数のマシンが同じワークロードを実行しており、整合性が必要な場合にのみ使用します。

13. **[レプリケーションを有効にする]**をクリックします。 **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。

## <a name="next-steps"></a>次のステップ

「[手順 11: テスト フェールオーバーを実行する](physical-walkthrough-test-failover.md)」に進みます。
