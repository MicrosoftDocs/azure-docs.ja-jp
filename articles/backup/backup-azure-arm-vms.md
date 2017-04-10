---
title: "Azure VM をバックアップする | Microsoft Docs"
description: "Azure 仮想マシンを検出し、Recovery Services コンテナーに登録およびバックアップします。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "仮想マシンのバックアップ; バックアップ、仮想マシン; バックアップと障害復旧; ARM VM のバックアップ"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/15/2017
ms.author: trinadhk;jimpark;markgal;
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: 72f1ed52c9e0f39009efc54967d7cc68517182a3
ms.lasthandoff: 03/02/2017


---
# <a name="back-up-azure-virtual-machines-to-a-recovery-services-vault"></a>Recovery Services コンテナーへの Azure 仮想マシンのバックアップ
> [!div class="op_single_selector"]
> * [Recovery Services コンテナーへの VM のバックアップ](backup-azure-arm-vms.md)
> * [バックアップ コンテナーへの VM のバックアップ](backup-azure-vms.md)
>
>

この記事では、Azure VM (Resource Manager デプロイとクラシック デプロイの両方) を Recovery Services コンテナーにバックアップする方法について説明します。 VM をバックアップするための作業のほとんどがその準備です。 VM をバックアップまたは保護する前に、 [前提条件](backup-azure-arm-vms-prepare.md) の作業を行って、VM を保護するために環境を準備する必要があります。 前提条件の作業が完了したら、バックアップ操作を開始して VM のスナップショットを作成できます。


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

詳細については、「[Azure における VM バックアップ インフラストラクチャの計画を立てる](backup-azure-vms-introduction.md)」と [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) に関するページを参照してください。

## <a name="triggering-the-backup-job"></a>バックアップ ジョブのトリガー
Recovery Services コンテナーに関連付けられているバックアップ ポリシーでは、バックアップ操作を実行する頻度とタイミングを定義します。 既定では、スケジュールされた最初のバックアップが初回バックアップとなります。 初回バックアップが実行されるまで、**[バックアップ ジョブ]** ブレードの [前回のバックアップの状態] には、**[警告 (初回のバックアップが保留中)]** と表示されます。

![Backup pending](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

初回バックアップがすぐに開始される予定でない場合は、 **[今すぐバックアップ]**を実行することをお勧めします。 以下の手順は、コンテナー ダッシュボードから開始します。 この手順では、すべての前提条件を完了した後で最初のバックアップ ジョブを実行します。 最初のバックアップ ジョブが既に実行されている場合、この手順は使用できません。 関連付けられているバックアップ ポリシーで、次のバックアップ ジョブが決定されます。  

最初のバックアップ ジョブを実行するには:

1. コンテナー ダッシュボードで、**[バックアップ項目]** の下の番号をクリックするか、**[バックアップ項目]** タイルをクリックします。 <br/>
  ![[設定] アイコン](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  **[バックアップ項目]** ブレードが開きます。

  ![項目をバックアップする](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. **[バックアップ項目]** ブレードで、項目を選択します。

  ![Settings icon](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  **[バックアップ項目]** の一覧が開きます。 <br/>

  ![Backup job triggered](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. **[バックアップ項目]** の一覧で、省略記号 **[...]** をクリックしてコンテキスト メニューを開きます。

  ![コンテキスト メニュー](./media/backup-azure-vms-first-look-arm/context-menu.png)

  コンテキスト メニューが表示されます。

  ![コンテキスト メニュー](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. コンテキスト メニューで、**[今すぐバックアップ]** をクリックします。

  ![コンテキスト メニュー](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  [今すぐバックアップ] ブレードが開きます。

  ![[今すぐバックアップ] ブレードが開く](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. [今すぐバックアップ] ブレードでカレンダー アイコンをクリックし、カレンダー コントロールを使ってこの回復ポイントを保持する最終日を選び、**[バックアップ]** をクリックします。

  ![今すぐバックアップ回復ポイントを保持する最後の日を設定する](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  デプロイ通知で、バックアップ ジョブがトリガーされたこと、および [バックアップ ジョブ] ページでジョブの進行状況を監視できることが示されます。 VM のサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

6. 初期バックアップの状態を表示または追跡するには、コンテナー ダッシュボードの **[バックアップ ジョブ]** タイルで **[進行中]** をクリックします。

  ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  [バックアップ ジョブ] ブレードが開きます。

  ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  **[バックアップ ジョブ]** ブレードでは、すべてのジョブの状態を確認できます。 VM のバックアップ ジョブがまだ進行中か、または終了したかを確認します。 バックアップ ジョブが完了すると、状態は *[完了]* になります。

  > [!NOTE]
  > Azure Backup サービスは、バックアップ操作の一部として、各 VM のバックアップ拡張機能に対して、すべての書き込みをフラッシュし、整合性のあるスナップショットを作成するためのコマンドを発行します。
  >
  >

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
仮想マシンのバックアップ中に問題が発生した場合は、[仮想マシンのトラブルシューティングに関する記事](backup-azure-vms-troubleshoot.md)を参照してください。

## <a name="next-steps"></a>次のステップ
VM を保護したので、VM の管理タスクおよび VM の復元方法の詳細については、次の記事を参照してください。

* [仮想マシンの管理と監視](backup-azure-manage-vms.md)
* [仮想マシンの復元](backup-azure-arm-restore-vms.md)

