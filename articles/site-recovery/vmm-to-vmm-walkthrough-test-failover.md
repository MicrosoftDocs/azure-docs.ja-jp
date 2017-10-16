---
title: "Azure Site Recovery を使用したセカンダリ サイトへの Hyper-V VM レプリケーションのテスト フェールオーバーを実行する | Microsoft Docs"
description: "Azure Site Recovery を使用したセカンダリ System Center VMM サイトへの Hyper-V VM レプリケーションのテスト フェールオーバーを実行する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a3fd11ce-65a1-4308-8435-45cf954353ef
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 23d235d326273e7ec59feee6588a39f685401e52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-run-a-test-failover-for-hyper-v-replication-to-a-secondary-site"></a>手順 10: セカンダリ サイトへの Hyper-V レプリケーションのテスト フェールオーバーを実行する


[Azure Site Recovery](site-recovery-overview.md) を使用した Hyper-V 仮想マシン (VM) レプリケーションを有効にしたら、この記事の手順に従ってテスト フェールオーバーを実行します。 テスト フェールオーバーでは、運用環境に影響を与えずにレプリケーションが機能することを確認します。 


この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="before-you-start"></a>開始する前に

- テスト フェールオーバーをトリガーするときは、テスト用のレプリカ VM の接続先となるネットワークを指定する必要があります。 [ネットワーク オプションの詳細](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery)をご覧ください。
- ネットワーク マッピング中に選択したネットワークを選択しないことをお勧めします。
- この記事の手順では、1 つの VM をフェールオーバーする方法について説明します。 複数の VM をフェールオーバーする場合は、[復旧計画の作成](site-recovery-create-recovery-plans.md)に関する記事をお読みください。
- [復旧計画の制限](site-recovery-test-failover-vmm-to-vmm.md#things-to-note)に関する記事をお読みください。
- テスト フェールオーバー中に VM に指定された IP アドレスは、その VM が計画されたフェールオーバーや計画されていないフェールオーバー中に受信するのと同じ IP アドレスです (IP アドレスはテスト フェールオーバー ネットワークで利用可能であることが前提)。 テスト フェールオーバー ネットワークで IP アドレスが使用できない場合、VM は、テスト フェールオーバー ネットワークで利用できる別の IP アドレスを受信します。
- エンド ツー エンドのネットワーク接続マシンの妥当性を確認できるよう、運用環境のネットワークでテスト フェールオーバーを実行する場合は、以下の点に注意してください。
    - テスト フェールオーバーの実行中は、プライマリ VM をシャット ダウンする必要があります。 そうしないと、同じ ID を持つ 2 つの仮想マシンが同じネットワークで同時に実行されることになります。 
    - テスト VM に加えた変更は、テスト フェールオーバーのクリーンアップを行うと失われます。 これらの変更は、プライマリ VM にはレプリケートされません。
    - 運用環境のネットワークでテストを行うと、運用環境のワークロードでダウンタイムが発生します。 ディザスター リカバリー訓練の実施中は、ユーザーがアプリを使用しないようにしてください。  


## <a name="run-a-test-failover-for-a-vm"></a>VM のテスト フェールオーバーの実行

1. 1 つの VM をフェールオーバーする場合は、**[レプリケートされたアイテム]** で、VM をクリックして **[テスト フェールオーバー]** をクリックします。
2. **[テスト フェールオーバー]** で、テスト フェールオーバー後にテスト VM をネットワークに接続する方法を指定します。 
3. **[OK]** をクリックすると、フェールオーバーが開始されます。 ジョブの進捗状況を **[ジョブ]** タブで追跡します。
5. フェールオーバーが完了したら、テスト VM が正常に起動することを確認します。
6. 完了したら、復旧計画の **[テスト フェールオーバーのクリーンアップ]** をクリックします。
7. **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 この手順により、テスト フェールオーバー中に作成された仮想マシンとネットワークが削除されます。


## <a name="next-steps"></a>次のステップ

デプロイをテストしたら、他の種類の[フェールオーバー](site-recovery-failover.md)について学びます。
