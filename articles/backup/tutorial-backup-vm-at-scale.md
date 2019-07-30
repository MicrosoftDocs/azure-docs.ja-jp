---
title: Azure 仮想マシンの大規模なバックアップ
description: 同時に複数の仮想マシンを Azure にバックアップする
keywords: 仮想マシンのバックアップ; 仮想マシン バックアップ; VM のバックアップ; VM バックアップ; Azure VM のバックアップ; バックアップとディザスター リカバリー
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
ms.custom: mvc
ms.openlocfilehash: da2a9e634567dbe657410a61e5bfe0526197309d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467130"
---
# <a name="use-azure-portal-to-back-up-multiple-virtual-machines"></a>Azure Portal を使用して複数の仮想マシンをバックアップする

Azure でデータをバックアップする場合は、Recovery Services コンテナーと呼ばれる Azure リソースにそのデータを格納します。 Recovery Services コンテナーのリソースは、ほとんどの Azure サービスの [設定] メニューから利用できます。 ほとんどの Azure サービスの [設定] メニューに Recovery Services コンテナーを統合すると、データのバックアップがとても簡単になるというメリットがあります。 ただし、ビジネスでデータベースまたは仮想マシンごとに個別に操作するのは面倒です。 すべての仮想マシンのデータを 1 つの部門または 1 つの場所にバックアップする場合はどうでしょうか。 バックアップ ポリシーを作成し、目的の仮想マシンにそのポリシーを適用して、複数の仮想マシンを簡単にバックアップできます。 このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * Recovery Services コンテナーを作成する
> * バックアップ ポリシーを定義する
> * 複数の仮想マシンを保護するバックアップ ポリシーを適用する
> * 保護された仮想マシン用のオンデマンドのバックアップ ジョブをトリガーする

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーには、バックアップ データと、保護される仮想マシンに適用されるバックアップ ポリシーが含まれます。 仮想マシンのバックアップはローカルな処理です。 ある場所から別の場所にある Recovery Services コンテナーに仮想マシンをバックアップすることはできません。 そのため、バックアップする仮想マシンがある Azure の場所ごとに、少なくとも 1 つの Recovery Services コンテナーが存在する必要があります。

1. 左側のメニューで **[すべてのサービス]** を選択し、サービスの一覧に *「Recovery Services」* と入力します。 入力中、リソースにフィルターが適用されます。 Recovery Services コンテナーが一覧に表示される場合、それを選択すると [Recovery Services コンテナー] メニューが開きます。

    ![[Recovery Services コンテナー] メニューを開く](./media/tutorial-backup-vm-at-scale/full-browser-open-rs-vault.png) <br/>

2. **[Recovery Services コンテナー]** メニューで、 **[追加]** をクリックして [Recovery Services コンテナー] メニューを開きます。

    ![コンテナー メニューを開く](./media/tutorial-backup-vm-at-scale/provide-vault-detail-2.png)

3. [Recovery Services コンテナー] メニューで、

    - **[名前]** に「*myRecoveryServicesVault*」と入力します。
    - 現在のサブスクリプション ID が **[サブスクリプション]** に表示されます。 追加のサブスクリプションがある場合は、新しいコンテナーに別のサブスクリプションを選択できます。
    - **[リソース グループ]** で **[既存のものを使用]** を選択し、" *[myResourceGroup]* " を選択します。 " *[myResourceGroup]* " が存在しない場合は、 **[新規作成]** を選択し、" *「myResourceGroup」* " と入力します。
    - **[場所]** ドロップダウン メニューから " *[西ヨーロッパ]* " を選択します。
    - **[作成]** をクリックし、Recovery Services コンテナーを作成します。

Recovery Services コンテナーは、保護する仮想マシンと同じ場所にある必要があります。 複数のリージョンに仮想マシンがある場合は、各リージョンで Recovery Services コンテナーを作成します。 このチュートリアルでは、Recovery Services コンテナーを "*西ヨーロッパ*" に作成します。"*myVM*" (クイックスタートで作成された仮想マシン) は西ヨーロッパに作成されたためです。

Recovery Services コンテナーの作成には数分かかることがあります。 ポータルの右上の領域で、状態の通知を監視します。 コンテナーが作成されると、Recovery Services コンテナーの一覧に表示されます。

Recovery Services コンテナーを作成する場合、既定では、コンテナーには geo 冗長ストレージがあります。 データの回復性を提供するために、geo 冗長ストレージは 2 つの Azure リージョン間でデータを複数回レプリケートします。

## <a name="set-backup-policy-to-protect-vms"></a>VM を保護するバックアップ ポリシーを設定する

Recovery Services コンテナーを作成した後、次の手順は、データの種類に合わせてコンテナーを構成することと、バックアップ ポリシーを設定することです。 バックアップ ポリシーは、復旧ポイントを作成する頻度と時期のスケジュールです。 ポリシーには、復旧ポイントの保持期間も含まれます。 このチュートリアルでは、ホテル、スタジアム、レストランと売店を備えたスポーツ複合施設のビジネスで、仮想マシン上のデータを保護することを前提とします。 次の手順では、財務データのバックアップ ポリシーを作成します。

1. Recovery Services コンテナーの一覧から **[myRecoveryServicesVault]** を選択し、そのダッシュボードを開きます。

   ![[シナリオ] メニューを開く](./media/tutorial-backup-vm-at-scale/open-vault-from-list.png)

2. コンテナーのダッシュボード メニューの **[バックアップ]** をクリックして、[バックアップ] メニューを開きます。

3. [バックアップの目標] メニューで、 **[ワークロードはどこで実行されていますか?]** ドロップダウン メニューの " *[Azure]* " を選択します。 **[What do you want to backup]\(バックアップ対象\)** ドロップダウンから " *[仮想マシン]* " を選択し、 **[バックアップ]** をクリックします。

    これらのアクションは、仮想マシンとやり取りするように Recovery Services コンテナーを準備します。 Recovery Services コンテナーには、1 日に復元ポイントを 1 つ作成し、復元ポイントを 30 日間保持する既定のポリシーがあります。

    ![[シナリオ] メニューを開く](./media/tutorial-backup-vm-at-scale/backup-goal.png)

4. 新規ポリシーを作成するには、[バックアップ ポリシー] メニューで、 **[バックアップ ポリシーの選択]** ドロップダウン メニューから " *[新規作成]* " を選択します。

    ![Select workload](./media/tutorial-backup-vm-at-scale/create-new-policy.png)

5. **[バックアップ ポリシー]** メニューで、 **[ポリシー名]** に " *「Finance」* " と入力します。 バックアップ ポリシーの次の変更を入力します。
   - **[バックアップの頻度]** には、"*中部標準時*" のタイム ゾーンを設定します。 スポーツ複合施設はテキサス州にあるため、所有者は時間をローカルにすることを希望しています。 バックアップの頻度は、毎日午前 3 時 30 分に設定したままにします。
   - **[毎日のバックアップ ポイントの保持期間]** には、90 日の期間を設定します。
   - **[毎週のバックアップ ポイントの保持期間]** には、" *[月曜日]* " の復元ポイントを使用し、52 週間保持します。
   - **[毎月のバックアップ ポイントの保持期間]** には、月の最初の日曜日の復元ポイントを使用し、36 か月間保持します。
   - **[毎年のバックアップ ポイントの保持期間]** オプションの選択を解除します。 財務部門のリーダーは、36 か月間より長くデータを保持することを希望していません。
   - **[OK]** をクリックしてバックアップ ポリシーを作成します。

     ![Select workload](./media/tutorial-backup-vm-at-scale/set-new-policy.png)

     バックアップ ポリシーを作成した後に、ポリシーを仮想マシンに関連付けます。

6. **[仮想マシンの選択]** ダイアログで、" *[myVM]* " を選択し、 **[OK]** をクリックしてバックアップ ポリシーを仮想マシンにデプロイします。

    同じ場所にあり、まだバックアップ ポリシーに関連付けられていないすべての仮想マシンが表示されます。 "*myVMH1*" と "*myVMR1*" が選択されて "*財務*" ポリシーに関連付けられます。

    ![Select workload](./media/tutorial-backup-vm-at-scale/choose-vm-to-protect.png)

    デプロイが完了すると、デプロイが正常に完了したことを示す通知を受信します。

## <a name="initial-backup"></a>初回バックアップ

Recovery Services コンテナーのバックアップを有効にしましたが、初回バックアップは作成されていません。 データが保護されるように最初のバックアップをトリガーすることが、ディザスター リカバリーのベスト プラクティスです。

オンデマンド バックアップ ジョブを実行するには、次の手順に従います。

1. コンテナー ダッシュボードで、 **[バックアップ項目]** の下の **[3]** をクリックして、[バックアップ項目] メニューを開きます。

    ![Settings icon](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)

    **[バックアップ項目]** メニューが開きます。

2. **[バックアップ項目]** メニューで、 **[Azure 仮想マシン]** をクリックして、コンテナーに関連付けられている仮想マシンの一覧を開きます。

    ![Settings icon](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    **[バックアップ項目]** の一覧が開きます。

    ![Backup job triggered](./media/tutorial-backup-vm-at-scale/initial-backup-context-menu.png)

3. **[バックアップ項目]** の一覧で、省略記号 **[...]** をクリックしてコンテキスト メニューを開きます。

4. コンテキスト メニューで、 **[今すぐバックアップ]** を選択します。

    ![コンテキスト メニュー](./media/tutorial-backup-vm-at-scale/context-menu.png)

    [今すぐバックアップ] メニューが開きます。

5. [今すぐバックアップ] メニューで、復旧ポイントを保持する最後の日付を入力し、 **[バックアップ]** をクリックします。

    ![今すぐバックアップ回復ポイントを保持する最後の日を設定する](./media/tutorial-backup-vm-at-scale/backup-now-short.png)

    デプロイ通知で、バックアップ ジョブがトリガーされたこと、および [バックアップ ジョブ] ページでジョブの進行状況を監視できることが示されます。 仮想マシンのサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

    初回バックアップ ジョブが完了したら、[バックアップ ジョブ] メニューでその状態を確認できます。 オンデマンド バックアップ ジョブで、"*myVM*" の最初の復元ポイントが作成されました。 他の仮想マシンをバックアップする場合は、各仮想マシンでこの手順を繰り返します。

    ![Backup Jobs tile](./media/tutorial-backup-vm-at-scale/initial-backup-complete.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

引き続きチュートリアルの作業を行う場合は、このチュートリアルで作成したリソースをクリーンアップしないでください。 作業を続行しない場合は、次の手順に従って、このチュートリアルで作成したすべてのリソースを Azure Portal で削除します。

1. **[myRecoveryServicesVault]** ダッシュボードで、 **[バックアップ項目]** の下の **[3]** をクリックして、[バックアップ項目] メニューを開きます。

    ![Settings icon](./media/tutorial-backup-vm-at-scale/tutorial-vm-back-up-now.png)


2. **[バックアップ項目]** メニューで、 **[Azure 仮想マシン]** をクリックして、コンテナーに関連付けられている仮想マシンの一覧を開きます。

    ![Settings icon](./media/tutorial-backup-vm-at-scale/three-virtual-machines.png)

    **[バックアップ項目]** の一覧が開きます。

3. **[バックアップ項目]** メニューで、省略記号をクリックしてコンテキスト メニューを開きます。

    ![Settings icon](./media/tutorial-backup-vm-at-scale/context-menu-to-delete-vm.png)

4. コンテキスト メニューで、 **[バックアップの停止]** を選択して [バックアップの停止] メニューを開きます。

    ![Settings icon](./media/tutorial-backup-vm-at-scale/context-menu-for-delete.png)

5. **[バックアップの停止]** メニューで、上のドロップダウン メニューを選択し、 **[バックアップ データの削除]** を選択します。

6. **[Type the name of the Backup item]\(バックアップ項目の名前を入力してください\)** ダイアログで、" *「myVM」* " と入力します。

7. バックアップ項目が確認されると (チェックマークが表示されます)、 **[バックアップの停止]** ボタンが有効になります。 **[バックアップの停止]** をクリックしてポリシーを停止し、復元ポイントを削除します。

    ![[バックアップの停止] をクリックしてコンテナーを削除する](./media/tutorial-backup-vm-at-scale/provide-reason-for-delete.png)

8. **[myRecoveryServicesVault]** メニューで、 **[削除]** をクリックします。

    ![[バックアップの停止] をクリックしてコンテナーを削除する](./media/tutorial-backup-vm-at-scale/deleting-the-vault.png)

    コンテナーが削除されると、Recovery Services コンテナーの一覧に戻ります。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Portal で次の作業を行いました。

> [!div class="checklist"]
> * Recovery Services コンテナーを作成する
> * 仮想マシンを保護するようにコンテナーを設定する
> * カスタムのバックアップ ポリシーと保持ポリシーを作成する
> * 複数の仮想マシンを保護するポリシーを割り当てる
> * 仮想マシンのオンデマンド バックアップをトリガーする

ディスクから Azure 仮想マシンを復元するには、次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [CLI を使用して VM を復元する](./tutorial-restore-disk.md)
