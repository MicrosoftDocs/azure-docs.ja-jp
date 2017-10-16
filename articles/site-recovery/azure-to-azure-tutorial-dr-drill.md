---
title: "Azure Site Recovery でセカンダリ Azure リージョンへの Azure VM のディザスター リカバリー訓練を実行する (プレビュー)"
description: "Azure Site Recovery サービスを使用してセカンダリ Azure リージョンへの Azure VM のディザスター リカバリー訓練を実行する方法について説明します。"
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: a3b453028b7fd32bd3ed22823a337f7a978d9aa7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>セカンダリ Azure リージョンへの Azure VM のディザスター リカバリー訓練を実行する (プレビュー)

[Azure Site Recovery](site-recovery-overview.md) サービスは、計画された停止や計画外の停止の際にビジネス アプリを実行し続け、使用できるようにすることで、ビジネス継続性とディザスター リカバリー (BCDR) 戦略に貢献します。 Site Recovery は、レプリケーション、フェールオーバー、復旧など、オンプレミスのマシンと Azure 仮想マシン (VM) のディザスター リカバリーを管理し、調整します。

このチュートリアルでは、テスト フェールオーバーを使用して、ある Azure リージョンから別のリージョンへの Azure VM のディザスター リカバリー訓練を実行する方法について説明します。 訓練ではデータ損失やダウンタイムなしでレプリケーション戦略を検証します。これは運用環境には影響しません。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 前提条件を確認する
> * 1 台の VM のテスト フェールオーバーを実行する

## <a name="prerequisites"></a>前提条件

- テスト フェールオーバーを実行する前に、すべてが想定どおりであることを確かめるために、VM のプロパティを確認することをお勧めします。  **[レプリケートされたアイテム]** で VM のプロパティにアクセスしてください。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。
- レプリケーションを有効にしたときに設定された既定のネットワークではなく、テスト フェールオーバー用の別個の Azure VM ネットワークを使用することをお勧めします。


## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. **[設定]** > **[レプリケートされたアイテム]** で、VM をクリックして **[+ テスト フェールオーバー]** をクリックします。

2. **[テスト フェールオーバー]** で、フェールオーバーに使用する復旧ポイントを選択します。

   - **[Latest processed]\(最新の処理\)**: Site Recovery サービスで処理された最新の復旧ポイントに VM をフェールオーバーします。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、低い RTO (Recovery Time Objective: 回復時刻の目標) を提示します。
   - **[Latest app-consistent]\(最新のアプリ整合性\)** : このオプションは、すべての VM を最新のアプリ整合性の復旧ポイントにフェールオーバーします。 タイム スタンプが表示されます。
   - **[カスタム]** : 任意の復旧ポイントを選択します。

3. フェールオーバー後に、セカンダリ リージョンのAzure VM の接続先となるターゲットの Azure 仮想ネットワークを選択します。

4. フェールオーバーを開始するには、**[OK]** をクリックします。 進行状況を追跡するには、VM をクリックして、そのプロパティを開きます。 また、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で、**テスト フェールオーバー** ジョブをクリックすることもできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が実行中で、適切なサイズであること、また、適切なネットワークに接続されていることを確認してください。
6. テスト フェールオーバー中に作成された VM を削除するには、レプリケートされたアイテムまたは復旧計画で **[Cleanup test failover]\(テスト フェールオーバーのクリーンアップ\)** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [運用環境のフェールオーバーを実行する](azure-to-azure-tutorial-failover-failback.md)
