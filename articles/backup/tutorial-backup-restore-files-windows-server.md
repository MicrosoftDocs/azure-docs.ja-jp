---
title: Azure から Windows Server にファイルを回復する
description: このチュートリアルでは、Azure から Windows Server への項目の回復について詳しく説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 575ebfb62b3eed70f783bc68b7f06559e5632490
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273975"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Azure から Windows Server にファイルを回復する

Azure Backup では、Windows Server のバックアップから個々の項目を回復できます。 個々のファイルの回復は、誤って削除されたファイルをすばやく復元する必要がある場合に便利です。 このチュートリアルでは、Microsoft Azure Recovery Services (MARS) エージェントを使用して、Azure で既に実行したバックアップから項目を回復する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 個々の項目の回復を開始する 
> * [回復ポイントの選択] 
> * 復旧ポイントから項目を復元する

このチュートリアルでは、[Windows Server を Azure にバックアップする](backup-configure-vault.md)手順を既に実行し、Azure に Windows Server ファイルのバックアップが少なくとも 1 つあることを前提としています。

## <a name="initiate-recovery-of-individual-items"></a>個々の項目の回復を開始する

Microsoft Azure Backup という名前の便利なユーザー インターフェイス ウィザードが、Microsoft Azure Recovery Services (MARS) エージェントと共にインストールされます。 Microsoft Azure Backup ウィザードは、Microsoft Azure Recovery Services (MARS) エージェントと連携して、Azure に格納された復旧ポイントからバックアップ データを取得します。 Microsoft Azure Backup ウィザードを使用して、Windows Server に復元するファイルやフォルダーを識別します。 

1. **Microsoft Azure Backup** スナップインを開きます。 エージェントは、コンピューターで **Microsoft Azure Backup**を検索すると見つかります。

    ![Backup pending](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. ウィザードで、エージェント コンソールの **[操作] ウィンドウ**で **[データの回復]** をクリックして、**データの回復**ウィザードを起動します。

    ![Backup pending](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. **[作業の開始]** ページで、 **[このサーバー (サーバー名)]** をクリックし、 **[次へ]** をクリックします。

4. **[回復モードの選択]** ページで **[個々のファイルおよびフォルダー]** を選択し、 **[次へ]** をクリックして復旧ポイントの選択のプロセスを開始します。
 
5. **[ボリュームと日付の選択]** ページで、復元するファイルやフォルダーが格納されているボリュームを選択し、 **[マウント]** をクリックします。 日付を選択し、復旧ポイントに対応するドロップダウン メニューから時刻を選択します。 **ボールド**になっている日付では、その日の復旧ポイントを少なくとも 1 つ利用できます。

    ![Backup pending](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    **[マウント]** をクリックすると、Azure Backup は、復旧ポイントをディスクとして使用できるようにします。 ディスクからファイルを参照し、復旧します。

## <a name="restore-items-from-a-recovery-point"></a>復旧ポイントから項目を復元する

1. 回復ボリュームがマウントされたら、 **[参照]** をクリックして Windows エクスプローラーを開き、復旧するファイルとフォルダーを検索します。 

    ![Backup pending](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    回復ボリュームから直接ファイルを開き、ファイルを確認できます。

2. Windows エクスプローラーで、復元するファイルやフォルダーをコピーし、サーバー上の目的の場所に貼り付けます。

    ![Backup pending](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. ファイルやフォルダーの復元が完了したら、**データの回復**ウィザードの **[ファイルの参照と回復]** ページで、 **[マウント解除]** をクリックします。 

    ![Backup pending](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  ボリュームのマウントを解除するかどうかを確認するメッセージが表示されたら **[はい]** をクリックします。

    スナップショットのマウントが解除されると、エージェント コンソールの **[ジョブ]** ウィンドウに **"ジョブが完了しました"** と表示されます。

## <a name="next-steps"></a>次の手順

これで、Windows Server のデータをバックアップして Azure に復元するチュートリアルは完了です。 Azure Backup の詳細については、暗号化された仮想マシンのバックアップに関する PowerShell サンプルをご覧ください。

> [!div class="nextstepaction"]
> [暗号化された VM のバックアップ](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
