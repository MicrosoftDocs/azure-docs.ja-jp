---
title: Azure Site Recovery を使用して Azure VM のディザスター リカバリー訓練を実行するチュートリアル
description: このチュートリアルでは、Site Recovery を使用して、別のリージョンへの Azure VM のディザスター リカバリー訓練を実行します。
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395652"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>チュートリアル:Azure VM のディザスター リカバリー訓練を実行する

[Azure Site Recovery](site-recovery-overview.md) で Azure VM を別の Azure リージョンにレプリケートするディザスター リカバリー訓練を実行する方法について説明します。 この記事では、次の内容について説明します。

> [!div class="checklist"]
> * 前提条件を確認する
> * 訓練の前に VM の設定を確認する
> * テスト フェールオーバーの実行
> * 訓練後にクリーンアップする


> [!NOTE]
> このチュートリアルでは、ディザスター リカバリー訓練を実行するための最小限の手順を紹介しています。 すべてのインフラストラクチャ テストを含んだ訓練を実行したい場合は、Azure VM の[ネットワーク](azure-to-azure-about-networking.md)、[自動化](azure-to-azure-powershell.md)、[トラブルシューティング](azure-to-azure-troubleshoot-errors.md)についての理解を深めてください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、少なくとも 1 つの Azure VM のディザスター リカバリーを有効にする必要があります。 そのためには、このシリーズの[最初のチュートリアルを完了](azure-to-azure-tutorial-enable-replication.md)してください。

## <a name="verify-vm-settings"></a>VM 設定を確認する

1. コンテナー > **[レプリケートされたアイテム]** で、VM を選択します。

    ![VM のプロパティで [ディザスター リカバリー] ページを開くためのオプション](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. VM が保護されていること、また正常な状態であることを **[概要]** ページで確認します。
3. テスト フェールオーバーを実行するときに、ターゲット リージョンの Azure 仮想ネットワークを選択します。 フェールオーバー後に作成される Azure VM は、このネットワークに配置されます。 

    - このチュートリアルでは、テスト フェールオーバーを実行するときに既存のネットワークを選択します。
    - 訓練には非運用ネットワークを選択することをお勧めします。そうすれば、運用ネットワークの IP アドレスとネットワーク コンポーネントは引き続き利用できます。
   - テスト フェールオーバーに使用するネットワーク設定を事前に構成することもできます。 NIC ごとに割り当てることができる細かな設定の例としては、サブネット、プライベート IP アドレス、パブリック IP アドレス、ロード バランサー、ネットワーク セキュリティ グループがあります。 ここではその方法を使用しませんが、詳しく知りたい方は、[こちらの記事を参照](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations)してください。


## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行


1. **[概要]** ページで、 **[テスト フェールオーバー]** を選択します。

    
    ![レプリケートされたアイテムの [テスト フェールオーバー] ボタン](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. **[テスト フェールオーバー]** で、復旧ポイントを選択します。 その復旧ポイントのデータを使用して、ターゲット リージョンに Azure VM が作成されます。
  
   - **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントが使用されます。 タイム スタンプが表示されます。 データの処理に時間がかからないため、目標復旧時間 (RTO) は短くなります。
   -  **Latest**:Site Recovery に送信されたすべてのデータを処理して、各 VM の復旧ポイントを作成してから、それにフェールオーバーします。 フェールオーバーがトリガーされた時点で、Site Recovery にすべてのデータがレプリケートされるため、回復ポイントの目標 (RPO) は最も短くなります。
   - **最新のアプリ整合性**:このオプションでは、最新のアプリ整合性の復旧ポイントに VM がフェールオーバーされます。 タイム スタンプが表示されます。
   - **Custom**:特定の復旧ポイントにフェールオーバーします。 カスタムは、フェールオーバーする VM が 1 つだけであって、復旧計画を使用しない場合にのみ使用できます。

3. フェールオーバー後に作成される Azure VM の配置先となるターゲット ネットワークを **[Azure 仮想ネットワーク]** で選択します。 可能であれば、レプリケーションを有効にするときに作成したネットワークではなく、非運用ネットワークを選択してください。

    ![テスト フェールオーバーの設定ページ](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. フェールオーバーを開始するには、**[OK]** を選択します。
5. [通知] でテスト フェールオーバーを監視します。

    ![進行状況の通知](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![成功の通知](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. フェールオーバーの完了後、ターゲット リージョンに作成された Azure VM が Azure portal の **[仮想マシン]** に表示されます。 VM が実行中で、適切なサイズであること、また、選択したネットワークに接続されていることを確認してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

1. **[Essentials]\(要点\)** ページで、 **[テスト フェールオーバーのクリーンアップ]** を選択します。

    ![クリーンアップ プロセスを開始するためのボタン](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. **[テスト フェールオーバーのクリーンアップ]**  >  **[メモ]** に、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 
3. テスト フェールオーバー中に作成された VM を削除するには、 **[テストが完了しました]** を選択します。

    ![クリーンアップ オプションが表示されるページ](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. [通知] で、クリーンアップの進行状況を監視します。

    ![クリーンアップの進行状況の通知](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![クリーンアップの成功の通知](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ディザスター リカバリー訓練を実行して、フェールオーバーが正しく機能していることを確認しました。 この後は、完全なフェールオーバーを実行することができます。

> [!div class="nextstepaction"]
> [運用環境のフェールオーバーを実行する](azure-to-azure-tutorial-failover-failback.md)
