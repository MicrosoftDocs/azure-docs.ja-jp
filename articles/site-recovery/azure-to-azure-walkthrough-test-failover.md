---
title: "Azure Site Recovery を使用した Azure VM レプリケーションのテスト フェールオーバーを実行する | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して別の Azure リージョンにレプリケートしている Azure VM に対して、テスト フェールオーバーを実行するために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---

# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>手順 6: Azure VM レプリケーションのテスト フェールオーバーを実行する

Azure 仮想マシン (VM) のレプリケーションを有効にしたら、この記事の手順に従って、Azure Portal にある [Azure Site Recovery](site-recovery-overview.md) サービスを使って 1 つの Azure リージョンから別のリージョンへテスト フェールオーバーを実行します。

- 記事の手順を完了すると、少なくとも 1 つの Azure VM がセカンダリの Azure リージョンにフェールオーバーできることが、テスト フェールオーバーによって検証されたことになります。 
- この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。

>[!NOTE]
>
> Azure VM レプリケーションは、現在プレビューの段階です。


## <a name="before-you-start"></a>開始する前に

- テスト フェールオーバーを実行する前に、VM のプロパティを確認し、必要な変更を加えることをお勧めします。 **[レプリケートされたアイテム]** で VM のプロパティにアクセスできます。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。
- テスト フェールオーバーには、運用環境のフェールオーバーのために設定したネットワーク (既定またはカスタマイズ) ではなく、別個の Azure VM ネットワークを使用することをお勧めします。
- テスト フェールオーバーは、Azure VM (およびそのストレージ) をセカンダリの Azure リージョンにフェールオーバーします。 依存するアプリやリソースはレプリケートしません。 VM のフェールオーバーで実行しているアプリが Active Directory や DNS など他のリソースに依存しているときは、セカンダリ リージョンでまだ使用可能になっていない場合、これらのリソースもレプリケートする必要があります。 [詳細情報](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- オンプレミス サイトからのフェールオーバー後に、レプリケートされた VM にアクセスしたい場合、これらの VM に[接続する準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)を行う必要があります。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. **[設定]** > **[レプリケートされたアイテム]** で、VM をクリックして **[+ テスト フェールオーバー]** をクリックします。 

2. **[テスト フェールオーバー]** で、フェールオーバーに使用する復旧ポイントを選択します。

    - **[Latest processed]\(最新の処理\)**: Site Recovery サービスで処理された最新の復旧ポイントに VM をフェールオーバーします。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、低い RTO (Recovery Time Objective: 回復時刻の目標) を提示します。
    - **[Latest app-consistent]\(最新のアプリ整合性\)** : このオプションは、すべての VM を最新のアプリ整合性の復旧ポイントにフェールオーバーします。 タイム スタンプが表示されます。 
    - **[カスタム]** : 任意の復旧ポイントを選択します。
 
3. フェールオーバー後に、セカンダリ リージョンのAzure VM の接続先となるターゲットの Azure 仮想ネットワークを選択します。
4. フェールオーバーを開始するには、**[OK]** をクリックします。 進行状況を追跡するには、VM をクリックして、そのプロパティを開きます。 また、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で、**テスト フェールオーバー** ジョブをクリックすることもできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行されていることを確認してください。
6. テスト フェールオーバー中に作成された VM を削除するには、レプリケートされたアイテムまたは復旧計画で **[Cleanup test failover]\(テスト フェールオーバーのクリーンアップ\)** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 

テスト フェールオーバーの詳細については、[こちら](site-recovery-test-failover-to-azure.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

フェールオーバーのテストを終えたら、このチュートリアルは完了です。 次は、運用環境でのフェールオーバーの実行について確認します。

- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- [復旧計画の使用](site-recovery-create-recovery-plans.md)による複数の VM のフェールオーバーについて詳細を確認する。
- [復旧計画の使用](site-recovery-create-recovery-plans.md)について詳細を確認する。
- フェールオーバー後の [Azure VM の再保護](site-recovery-how-to-reprotect.md)の詳細を確認する。


