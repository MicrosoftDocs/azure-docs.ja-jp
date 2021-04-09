---
title: チュートリアル:Windows Server に項目を回復させる
description: このチュートリアルでは、Microsoft Azure Recovery Services Agent (MARS) エージェントを使用して、Azure から Windows Server に項目を回復する方法について説明します。
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88263114"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Azure から Windows Server にファイルを回復する

Azure Backup では、Windows Server のバックアップから個々の項目を回復できます。 個々のファイルの回復は、誤って削除されたファイルをすばやく復元する必要がある場合に便利です。 このチュートリアルでは、Microsoft Azure Recovery Services (MARS) エージェントを使用して、Azure で既に実行したバックアップから項目を回復する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 個々の項目の回復を開始する
> * [回復ポイントの選択]
> * 復旧ポイントから項目を復元する

このチュートリアルでは、[Windows Server を Azure にバックアップする](backup-windows-with-mars-agent.md)手順を既に実行し、Azure に Windows Server ファイルのバックアップが少なくとも 1 つあることを前提としています。

## <a name="initiate-recovery-of-individual-items"></a>個々の項目の回復を開始する

Microsoft Azure Backup という名前の便利なユーザー インターフェイス ウィザードが、Microsoft Azure Recovery Services (MARS) エージェントと共にインストールされます。 Microsoft Azure Backup ウィザードは、Microsoft Azure Recovery Services (MARS) エージェントと連携して、Azure に格納された復旧ポイントからバックアップ データを取得します。 Microsoft Azure Backup ウィザードを使用して、Windows Server に復元するファイルやフォルダーを識別します。

1. **Microsoft Azure Backup** スナップインを開きます。 エージェントは、コンピューターで **Microsoft Azure Backup** を検索すると見つかります。

    ![Microsoft Azure Backup スナップイン](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. ウィザードで、エージェント コンソールの **[操作]** ペインで **[データの回復]** を選択して、**データの回復** ウィザードを起動します。

    ![[データの回復] を選択する](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. **[作業の開始]** ページで、 **[このサーバー (サーバー名)]** を選択し、 **[次へ]** を選択します。

4. **[回復モードの選択]** ページで **[個々のファイルおよびフォルダー]** を選択し、 **[次へ]** を選択して復旧ポイントの選択のプロセスを開始します。

5. **[ボリュームと日付の選択]** ページで、復元するファイルやフォルダーが格納されているボリュームを選択し、 **[マウント]** を選択します。 日付を選択し、復旧ポイントに対応するドロップダウン メニューから時刻を選択します。 **ボールド** になっている日付では、その日の復旧ポイントを少なくとも 1 つ利用できます。

    ![ボリュームと日付を選択する](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    **[マウント]** を選択すると、Azure Backup によって、復旧ポイントがディスクとして使用できるようになります。 ディスクからファイルを参照し、復旧します。

## <a name="restore-items-from-a-recovery-point"></a>復旧ポイントから項目を復元する

1. 回復ボリュームがマウントされたら、 **[参照]** を選択してエクスプローラーを開き、復旧するファイルとフォルダーを検索します。

    ![[参照] を選択する](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    回復ボリュームから直接ファイルを開き、ファイルを確認できます。

2. エクスプローラーで、復元するファイルやフォルダーをコピーし、サーバー上の任意の場所に貼り付けます。

    ![ファイルとフォルダーをコピーする](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. ファイルやフォルダーの復元が完了したら、**データの回復** ウィザードの **[ファイルの参照と回復]** ページで、 **[マウント解除]** を選択します。

    ![[マウント解除] を選択する](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. **[はい]** を選択して、ボリュームのマウントを解除することを確認します。

    スナップショットのマウントが解除されると、エージェント コンソールの **[ジョブ]** ウィンドウに **"ジョブが完了しました"** と表示されます。

## <a name="next-steps"></a>次のステップ

これで、Windows Server のデータをバックアップして Azure に復元するチュートリアルは完了です。 Azure Backup の詳細については、暗号化された仮想マシンのバックアップに関する PowerShell サンプルをご覧ください。

> [!div class="nextstepaction"]
> [暗号化された VM のバックアップ](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
