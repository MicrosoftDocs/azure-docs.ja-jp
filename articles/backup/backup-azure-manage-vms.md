---
title: "Resource Manager によってデプロイされた仮想マシン バックアップの管理 | Microsoft Docs"
description: "Resource Manager によってデプロイされた仮想マシンのバックアップを管理したり監視したりする方法について説明します。"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: f3050283-d60f-472d-b464-cb844e70d67e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk;markgal
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: f8cb0030290d200ab79e340b9b0fe8f5765d49f0
ms.lasthandoff: 03/18/2017


---
# <a name="manage-azure-virtual-machine-backups"></a>Azure 仮想マシンのバックアップの管理
> [!div class="op_single_selector"]
> * [Azure VM バックアップの管理](backup-azure-manage-vms.md)
> * [クラシック VM バックアップの管理](backup-azure-manage-vms-classic.md)
>
>

この記事では、VM のバックアップ管理に関するガイダンスを提供するほか、ポータルのダッシュボードで使用できるバックアップ アラート情報について説明します。 この記事のガイダンスは、Recovery Services コンテナーと組み合わせて VM を使用するケースを想定しています。 この記事で仮想マシンの作成や仮想マシンの保護については取り上げていません。 Azure Resource Manager によって Azure にデプロイされた VM を Recovery Services コンテナーで保護する方法についての基本的な情報については、「 [最初に: ARM VM の Recovery Services コンテナーへのバックアップ](backup-azure-vms-first-look-arm.md)」を参照してください。

## <a name="manage-vaults-and-protected-virtual-machines"></a>コンテナーと保護された仮想マシンの管理
Azure ポータルから Recovery Services コンテナーのダッシュボードにアクセスすると、コンテナーに関して次のような情報を確認できます。

* 最新のバックアップ スナップショット (つまり最新の復元ポイント) <br\>
* バックアップ ポリシー <br\>
* すべてのバックアップ スナップショットの合計サイズ <br\>
* コンテナーで保護されている仮想マシンの数 <br\>

仮想マシンのバックアップに伴う管理タスクは多くの場合、ダッシュボードでコンテナーを開くことから始まります。 ただし Recovery Services コンテナーを使った保護は、複数の項目 (または複数の VM) が対象となる場合があるため、特定の VM の詳細を表示するには、そのコンテナー項目のダッシュボードを開きます。 以降の手順では、*コンテナーのダッシュボード*と*コンテナー項目のダッシュボード*の開き方について順に説明しています。 2 つの手順では、コンテナーとコンテナー項目を Azure ダッシュボードに追加する方法について、[ダッシュボードにピン留めする] コマンドを使用した "ヒント" を紹介しています。 "ダッシュボードにピン留めする" は、コンテナーまたはコンテナー項目へのショートカットの作成手段となります。 このショートカットから使用頻度の高いコマンドを実行することもできます。

> [!TIP]
> 複数のダッシュボードまたはブレードが開いている場合は、ウィンドウの一番下にある濃い青色のスライダーを使用して、Azure ダッシュボードを左右にスライドさせます。
>
>

![Full view with slider](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Recovery Services コンテナーをダッシュボードから開く
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ハブ メニューで **[参照]** をクリックし、リソースの一覧で「**Recovery Services**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]**をクリックします。

    ![Create Recovery Services Vault step 1](./media/backup-azure-manage-vms/browse-to-rs-vaults.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。

    ![List of Recovery Services vaults ](./media/backup-azure-manage-vms/list-o-vaults.png) <br/>

   > [!TIP]
   > Azure ダッシュボードにコンテナーをピン留めすると、Azure ポータルを開いてすぐにコンテナーにアクセスすることができます。 ダッシュボードにコンテナーをピン留めするには、コンテナーの一覧で目的のコンテナーを右クリックし、 **[ダッシュボードにピン留めする]**を選択します。
   >
   >
3. コンテナーの一覧から、いずれかのコンテナーを選択して、そのダッシュボードを開きます。 コンテナーを選択すると、そのコンテナーのダッシュボードと **[設定]** ブレードが表示されます。 次の画像では、 **Contoso-vault** ダッシュボードが強調表示されています。

    ![Open vault dashboard and Settings blade](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>コンテナー項目のダッシュボードを開く
前の手順では、コンテナーのダッシュボードを開きました。 コンテナー項目のダッシュボードを開くには、次の手順に従います。

1. コンテナーのダッシュボードにある **[バックアップ項目]** タイルで、**[Azure Virtual Machines]** をクリックします。

    ![Open backup items tile](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    **[バックアップ項目]** ブレードに、項目ごとの最新のバックアップ ジョブが一覧表示されます。 この例では、このコンテナーによって保護されている仮想マシンが 1 つ存在します (demovm-markgal)。  

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > コンテナー項目は、アクセスしやすいように、Azure ダッシュボードにピン留めすることができます。 コンテナー項目をピン留めするには、コンテナー項目の一覧で目的の項目を右クリックし、 **[ダッシュボードにピン留めする]**を選択します。
   >
   >
2. **[バックアップ項目]** ブレードで目的の項目をクリックして、コンテナー項目のダッシュボードを開きます。

    ![Backup items tile](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    コンテナー項目のダッシュボードとその **[設定]** ブレードが表示されます。

    ![Backup items dashboard with Settings blade](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    コンテナー項目のダッシュボードから、多くの重要な管理作業を実行できます。その例を次に示します。

   * ポリシーの変更または新しいバックアップ ポリシーの作成<br\>
   * 復元ポイントとその整合状態の確認 <br\>
   * オンデマンドでの仮想マシンのバックアップ <br\>
   * 仮想マシンの保護の停止 <br\>
   * 仮想マシンの保護の再開 <br\>
   * バックアップ データ (または復旧ポイント) の削除 <br\>
   * [バックアップ ディスクの復元](backup-azure-arm-restore-vms.md#restore-backed-up-disks)  <br\>

次の手順は、コンテナー項目のダッシュボードを表示したところから始めます。

## <a name="manage-backup-policies"></a>バックアップ ポリシーを管理する
1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で、**[すべての設定]** をクリックして **[設定]** ブレードを開きます。

    ![Backup policy blade](./media/backup-azure-manage-vms/all-settings-button.png)
2. **[設定]** ブレードの **[バックアップ ポリシー]** をクリックして対応するブレードを開きます。

    表示されたブレードに、バックアップの頻度とリテンション期間が詳しく表示されます。

    ![Backup policy blade](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. **[バックアップ ポリシーの選択]** メニューから次の手順を実行します。

   * ポリシーを変更するには、別のポリシーを選択し、 **[保存]**をクリックします。 すぐに、新しいポリシーがコンテナーに適用されます。 <br\>
   * ポリシーを作成するには、 **[新規作成]**を選択します。

     ![仮想マシンのバックアップ](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     バックアップ ポリシーを作成する手順については、「 [バックアップ ポリシーの定義](backup-azure-manage-vms.md#defining-a-backup-policy)」を参照してください。

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

## <a name="on-demand-backup-of-a-virtual-machine"></a>オンデマンドでの仮想マシンのバックアップ
仮想マシンに保護が構成されていれば、その仮想マシンについてオンデマンド バックアップを作成できます。 初回バックアップがまだ実行されていない場合、オンデマンド バックアップを実行すると、仮想マシンの完全なコピーが Recovery Services コンテナーに作成されます。 初回バックアップが完了している場合、オンデマンド バックアップによって Recovery Services コンテナーに送信されるのは、前回のスナップショット以降の変更だけです。 つまり、以降のバックアップでは、増分のみが送信対象となります。

> [!NOTE]
> オンデマンド バックアップのリテンション期間は、ポリシーの中で日単位のバックアップ ポイントに対して指定されたリテンション期間の値となります。 日単位のバックアップ ポイントが選択されていない場合は、週単位のバックアップ ポイントが使用されます。
>
>

仮想マシンのオンデマンド バックアップをトリガーするには、次の手順に従います。

* [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[今すぐバックアップ]**をクリックします。

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-button.png)

    オンデマンド バックアップ ジョブを開始してよいかどうかを確認するメッセージが表示されます。 バックアップ ジョブを開始する場合は **[はい]** をクリックします。

    ![Backup now button](./media/backup-azure-manage-vms/backup-now-check.png)

    バックアップ ジョブによって、復旧ポイントが作成されます。 復旧ポイントのリテンション期間は、仮想マシンに関連付けられているポリシーで指定されるリテンション期間と同じです。 ジョブの進行状況を追跡するには、コンテナーのダッシュボードで **[バックアップ ジョブ]** タイルをクリックします。  

## <a name="stop-protecting-virtual-machines"></a>仮想マシンの保護を停止する
仮想マシンの保護を中止するように選択した場合、復旧ポイントを保持するかどうかを確認するメッセージが表示されます。 仮想マシンの保護を中止する方法は 2 つあります。

* 将来のバックアップ ジョブをすべて停止したうえで、すべての復旧ポイントを削除する方法と、
* 将来のバックアップ ジョブをすべて停止して、復旧ポイントを保持する方法です。 <br/>

復旧ポイントを記憶域に保持するためにはコストが発生します。 一方、復旧ポイントを保持する利点は、仮想マシンを必要に応じて後から復元できることです。 復旧ポイントを保持するためのコストについては、「[価格の詳細](https://azure.microsoft.com/pricing/details/backup/)」を参照してください。 すべての復旧ポイントを削除するように選択した場合、仮想マシンを復元することはできません。

仮想マシンに対する保護を停止するには、次の手順に従います。

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップの停止]**をクリックします。

    ![Stop backup button](./media/backup-azure-manage-vms/stop-backup-button.png)

    [バックアップの停止] ブレードが開きます。

    ![Stop backup blade](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. **[バックアップの停止]** ブレードで、バックアップ データを保持するか削除するかを選択します。 選択内容についての詳しい説明が、情報ボックスに表示されます。

    ![保護の停止](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. バックアップ データを保持するように選択した場合は、手順 4. に進みます。 バックアップ データを削除するように選択した場合は、バックアップ ジョブの停止と復旧ポイントの削除についての意思確認をします。対象となる項目の名前を入力してください。

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

    項目名がわからない場合は、感嘆符にマウス カーソルを合わせると名前が表示されます。 また、 **[バックアップの停止]** ブレードのタイトルの下にも項目の名前が表示されます。
4. 必要に応じて、**[理由]** または **[コメント]** に入力します。
5. 現在の項目のバックアップ ジョブを停止するには、 ![[バックアップの停止] ボタン](./media/backup-azure-manage-vms/stop-backup-button-blue.png) をクリックします。

    バックアップ ジョブが停止したことを示す通知メッセージが表示されます。

    ![保護の停止の確認](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>仮想マシンの保護の再開
仮想マシンの保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合、保護を再開することができます。 **[バックアップ データを削除]** オプションを選択した場合は、仮想マシンの保護を再開できません。

仮想マシンの保護を再開するには

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップの再開]**をクリックします。

    ![Resume protection](./media/backup-azure-manage-vms/resume-backup-button.png)

    [バックアップ ポリシー] ブレードが開きます。

   > [!NOTE]
   > 仮想マシンを再保護する場合は、最初に仮想マシンを保護するために使用いたポリシー以外のポリシーを選択できます。
   >
   >
2. [バックアップ ポリシーの管理](backup-azure-manage-vms.md#manage-backup-policies)に関するトピックの手順に従って、仮想マシンのポリシーを割り当てます。

    仮想マシンにバックアップ ポリシーが適用されると、次のメッセージが表示されます。

    ![Successfully protected VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>[バックアップ データを削除]
仮想マシンに関連付けられているバックアップ データは、 **バックアップの停止** ジョブの実行中やバックアップ ジョブが完了した後にいつでも削除することができます。 数日または数週間待ってから復旧ポイントを削除する方が便利な場合もあります。 復旧ポイントの復元とは異なり、バックアップ データを削除するときは、特定の復旧ポイントを削除対象として選択することができません。 バックアップ データを削除するように選択すると、その項目に関連付けられている復旧ポイントもすべて削除されます。

以降の手順は、仮想マシンのバックアップ ジョブが停止済みであるか、無効にされていることを前提としています。 バックアップ ジョブを無効にしたら、**[バックアップの再開]** オプションと **[バックアップ データの削除]** オプションが、コンテナー項目のダッシュボードに表示されます。

![Resume and delete buttons](./media/backup-azure-manage-vms/resume-delete-buttons.png)

" *バックアップを無効*" にした状態の仮想マシンでバックアップ データを削除するには:

1. [コンテナー項目のダッシュボード](backup-azure-manage-vms.md#open-a-vault-item-dashboard)で **[バックアップ データの削除]**をクリックします。

    ![VM の種類](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **[バックアップ データを削除]** ブレードが開きます。

    ![VM の種類](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. 項目の名前を入力して、復旧ポイントを削除してもよいことを確認します。

    ![Stop verification](./media/backup-azure-manage-vms/item-verification-box.png)

    項目名がわからない場合は、感嘆符にマウス カーソルを合わせると名前が表示されます。 また、 **[バックアップ データを削除]** ブレードのタイトルの下にも項目の名前が表示されます。
3. 必要に応じて、**[理由]** または **[コメント]** に入力します。
4. 現在の項目のバックアップ データを削除するには、 ![[バックアップの停止] ボタン](./media/backup-azure-manage-vms/delete-button.png) をクリックします。

    バックアップ データが削除されたことを示す通知メッセージが表示されます。

## <a name="next-steps"></a>次のステップ
復旧ポイントからの仮想マシンの再作成については、 [Azure VM の復元](backup-azure-restore-vms.md)に関するページをご覧ください。 仮想マシンの保護については、 [Recovery Services コンテナーへの VM のバックアップ](backup-azure-vms-first-look-arm.md)に関するページをご覧ください。 イベントの監視については、「 [Monitor alerts for Azure virtual machine backups (Azure 仮想マシンのバックアップ アラートの監視)](backup-azure-monitor-vms.md)」を参照してください。

