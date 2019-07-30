---
title: Azure ファイル共有のバックアップ
description: この記事では、Azure ファイル共有をバックアップおよび復元する方法について詳しく説明するほか、管理タスクについて説明します。
author: rayne-wiselman
ms.author: raynew
ms.date: 01/31/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 7233db1e685c3edcdbd6a97bc2ae23706ad6f767
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466544"
---
# <a name="back-up-azure-file-shares"></a>Azure ファイル共有のバックアップ
この記事では、Azure Portal を使用して [Azure ファイル共有](../storage/files/storage-files-introduction.md)のバックアップと復元を行う方法について説明します。

このガイドでは、以下の方法について説明します。
> [!div class="checklist"]
> * Recovery Services コンテナーを構成して Azure Files をバックアップできるようにする
> * オンデマンド バックアップ ジョブを実行して復元ポイントを作成する
> * 復元ポイントから 1 つまたは複数のファイルを復元する
> * バックアップ ジョブの管理
> * Azure Files の保護を停止する
> * バックアップ データを削除する

## <a name="prerequisites"></a>前提条件
Azure ファイル共有をバックアップする前に、[サポートされているストレージ アカウントの種類](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview)のいずれかにそれが存在することを確認しておいてください。 この確認が完了したら、ファイル共有を保護することができます。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>プレビュー期間における Azure ファイル共有のバックアップの制限
Azure ファイル共有のバックアップはプレビュー段階です。 汎用 v1 ストレージ アカウントと汎用 v2 ストレージ アカウント、どちらの Azure ファイル共有もサポートされています。 次のバックアップ シナリオは、Azure ファイル共有ではサポートされていません。
- 現在、[ゾーン冗長ストレージ](../storage/common/storage-redundancy-zrs.md) (ZRS) レプリケーションを使用したストレージ アカウントでの Azure ファイル共有のバックアップのサポートは、[これらのリージョン](backup-azure-files-faq.md#in-which-geos-can-i-back-up-azure-file-shares-)に限定されています。
- 仮想ネットワークまたはファイアウォールが有効になっているストレージ アカウントの Azure ファイル共有を保護することはできません。
- Azure Backup を使用して Azure Files を保護するために利用できる CLI はありません。
- スケジュール バックアップの数は、1 日につき 1 個が上限となります。
- オンデマンド バックアップの数は、1 日につき 4 個が上限となります。
- ストレージ アカウントに対する[リソース ロック](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)を使用して、Recovery Services コンテナー内のバックアップを誤って削除しないようにします。
- Azure Backup によって作成されたスナップショットを削除しないでください。 スナップショットを削除すると、復旧ポイントが失われたり、復元が失敗したりする場合があります。
- Azure Backup で保護されているファイル共有は削除しないでください。 現在のソリューションでは、ファイル共有が削除されると Azure Backup によって取得されたスナップショットがすべて削除されます。そのため、すべての復元ポイントが失われます。



## <a name="configuring-backup-for-an-azure-file-share"></a>Azure ファイル共有のバックアップの構成
このチュートリアルでは、Azure ファイル共有を既に確立していることを前提としています。 Azure ファイル共有をバックアップするには:

1. 使用しているファイル共有と同じリージョンに Recovery Services コンテナーを作成します。 コンテナーが既にある場合は、そのコンテナーの [概要] ページを開き、 **[バックアップ]** をクリックします。

    ![バックアップの目標として Azure ファイル共有を選択する](./media/backup-file-shares/overview-backup-page.png)

2. **[バックアップの目標]** メニューの **[何をバックアップしますか?]** で [Azure ファイル共有] を選択します。

    ![バックアップの目標として Azure ファイル共有を選択する](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. **[バックアップ]** をクリックして、Recovery Services コンテナーに対して Azure ファイル共有を構成します。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/set-backup-goal.png)

    コンテナーが Azure ファイル共有に関連付けられると、[バックアップ] メニューが開き、ストレージ アカウントを選択するように求められます。 このメニューには、コンテナーが存在するリージョン内にある、まだ Recovery Services コンテナーに関連付けられていない、サポートされているストレージ アカウントがすべて表示されます。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/list-of-storage-accounts.png)

4. ストレージ アカウントの一覧でアカウントを選択し、 **[OK]** をクリックします。 Azure は、バックアップ可能なファイル共有のストレージ アカウントを検索します。 最近ファイル共有を追加していても一覧に表示されない場合は、ファイル共有が表示されるまで少し時間を置いてください。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/discover-file-shares.png)

5. **[ファイル共有]** の一覧から、バックアップするファイル共有を 1 つ以上選択し、 **[OK]** をクリックします。

6. ファイル共有を選択すると、[バックアップ] メニューが **[バックアップ ポリシー]** に切り替わります。 このメニューから、既存のバックアップ ポリシーを選択するか新しいバックアップ ポリシーを作成し、 **[バックアップの有効化]** をクリックします。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/apply-backup-policy.png)

    バックアップ ポリシーを確立すると、スケジュールされた時刻にファイル共有のスナップショットが作成され、復旧ポイントは選択した期間保持されます。

## <a name="create-an-on-demand-backup"></a>オンデマンド バックアップの作成
場合によっては、バックアップ ポリシーでスケジュールされた時間外に、バックアップ スナップショット、つまり復旧ポイントを生成することができます。 一般的に、オンデマンド バックアップを生成するのは、バックアップ ポリシーを構成した直後です。 バックアップ ポリシーのスケジュールに基づいて、スナップショットが作成されるまで数時間または数日かかることがあります。 バックアップ ポリシーが適用されるまでデータを保護するために、オンデマンド バックアップを開始します。 多くの場合は、ファイル共有への計画的な変更を行う前にオンデマンド バックアップの作成が必要です。

### <a name="to-create-an-on-demand-backup"></a>オンデマンド バックアップを作成するには

1. ファイル共有の復旧ポイントが含まれている Recovery Services コンテナーを開き、 **[Backup Items]\(バックアップ項目\)** をクリックします。 バックアップ項目の種類の一覧が表示されます。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/list-of-backup-items.png)

2. 一覧から **[Azure Storage (Azure Files)]** を選択します。 Azure ファイル共有の一覧が表示されます。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Azure ファイル共有の一覧から、目的のファイル共有を選択します。 選択したファイル共有の [Backup Item]\(バックアップ項目\) メニューが開きます。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/backup-item-menu.png)

4. [Backup Item]\(バックアップ項目\) メニューから **[今すぐバックアップ]** をクリックします。 これはオンデマンド バックアップ ジョブのため、復旧ポイントに関連付けられた保持ポリシーはありません。 **[今すぐバックアップ]** ダイアログが開きます。 復旧ポイントの保持期限を指定します。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Azure ファイル共有のバックアップから復元する
復元ポイントからファイル共有全体または個々のファイルやフォルダーを復元する必要がある場合は、前のセクションで詳しく説明したように [Backup Item]\(バックアップ項目\) に移動します。 **[共有の復元]** を選択して、目的の特定の時点からファイル共有全体を復元します。 表示される復元ポイントの一覧から、1 つを選択して、現在のファイル共有を上書きするか同じリージョン内の別のファイル共有に復元することができます。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Azure ファイル共有のバックアップから個々のファイルまたはフォルダーを復元する
Azure Backup を使用すると、Azure Portal 内で復元ポイントを参照できます。 任意のファイルまたはフォルダーを復元するには、[Backup Item]\(バックアップ項目\) ページの [ファイルの回復] をクリックし、復元ポイントの一覧から選択します。 [回復先] を選択し、 **[ファイルの選択]** をクリックして復元ポイントを参照します。 任意のファイルまたはフォルダーを選択し、 **[復元]** を選択します。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Azure ファイル共有のバックアップを管理する

**[バックアップ ジョブ]** ページで、次のような、ファイル共有のバックアップに関するいくつかの管理タスクを実行できます。
- [ジョブの監視](backup-azure-files.md#monitor-jobs)
- [新しいポリシーの作成](backup-azure-files.md#create-a-new-policy)
- [ファイル共有の保護の停止](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [ファイル共有の保護の再開](backup-azure-files.md#resume-protection-for-azure-file-share)
- [バックアップ データの削除](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>ジョブの監視

**[バックアップ ジョブ]** ページで、すべてのジョブの進行状況を監視できます。

**[バックアップ ジョブ]** ページを開くには:

- 監視する Recovery Services コンテナーを開き、Recovery Services コンテナーのメニューで **[ジョブ]** をクリックし、 **[バックアップ ジョブ]** をクリックします。

   ![監視するジョブを選択する](./media/backup-file-shares/open-backup-jobs.png)

    バックアップ ジョブとそれらのジョブのステータスの一覧が表示されます。

    ![監視するジョブを選択する](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>新しいポリシーの作成

Recovery Services コンテナーの **[バックアップ ポリシー]** から、Azure ファイル共有をバックアップするための新しいポリシーを作成できます。 ファイル共有のバックアップを構成する際に作成されたすべてのポリシーは、Azure ファイル共有というポリシーの種類で表示されます。

既存のバックアップ ポリシーを表示するには:

- 目的の Recovery Services コンテナーを開き、Recovery Services コンテナーのメニューで **[バックアップ ポリシー]** をクリックします。 すべてのバックアップ ポリシーが一覧表示されます。

   ![監視するジョブを選択する](./media/backup-file-shares/list-of-backup-policies.png)

新しいバックアップ ポリシーを作成するには:

1. [Recovery Services コンテナー] メニューで **[バックアップ ポリシー]** をクリックします。
2. バックアップ ポリシーの一覧で、 **[追加]** をクリックします。

   ![監視するジョブを選択する](./media/backup-file-shares/new-backup-policy.png)

3. **[追加]** メニューで **[Azure ファイル共有]** を選択します。 Azure ファイル共有の [バックアップ ポリシー] メニューが開きます。 復旧ポイントのポリシー名、バックアップ頻度、保持期間を指定します。 ポリシーの定義が完了したら、[OK] をクリックします。

   ![監視するジョブを選択する](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Azure ファイル共有の保護の停止

Azure ファイル共有の保護を停止するように選択した場合は、復旧ポイントを保持するかどうかを確認するメッセージが表示されます。 Azure ファイル共有の保護を停止するには、次の 2 つの方法があります。

- 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する
- 今後のバックアップ ジョブすべてを停止するが、復旧ポイントはそのままにする

Azure Backup によって作成された基になるスナップショットが保持されるため、ストレージに復旧ポイントをそのまま残す場合にはコストが伴う可能性があります。 一方、復旧ポイントを残す利点は、必要に応じて後でファイル共有を復元できることです。 復旧ポイントを残した場合のコストについては、価格の詳細を参照してください。 すべての復旧ポイントを削除するように選択した場合、ファイル共有を復元することはできません。

Azure ファイル共有の保護を停止するには:

1. ファイル共有の復旧ポイントが含まれている Recovery Services コンテナーを開き、 **[Backup Items]\(バックアップ項目\)** をクリックします。 バックアップ項目の種類の一覧が表示されます。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/list-of-backup-items.png)

2. **[バックアップの管理の種類]** の一覧で、 **[Azure Storage (Azure Files)]** を選択します。 (Azure Storage (Azure Files)) のバックアップ項目の一覧が表示されます。

   ![項目をクリックして追加メニューを開く](./media/backup-file-shares/azure-file-share-backup-items.png)

3. [Backup Items (Azure Storage (Azure Files))]\(バックアップ項目 (Azure Storage (Azure Files))\) の一覧で、停止するバックアップ項目を選択します。

4. Azure ファイル共有の項目で **[More]\(その他\)** メニューをクリックし、 **[バックアップの停止]** を選択します。

   ![項目をクリックして追加メニューを開く](./media/backup-file-shares/stop-backup.png)

5. [バックアップの停止] メニューから、 **[バックアップ データの保持]** または **[バックアップ データの削除]** を選択し、 **[バックアップの停止]** をクリックします。

   ![項目をクリックして追加メニューを開く](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Azure ファイル共有の保護の再開

ファイル共有の保護を停止するときに [バックアップ データの保持] オプションを選択した場合は、保護を再開することができます。 **[バックアップ データの削除]** オプションを選択した場合は、ファイル共有の保護を再開できません。

ファイル共有の保護を再開するには、[Backup Items]\(バックアップ項目\) に移動し、[バックアップの再開] をクリックします。 [バックアップ ポリシー] が開き、任意のポリシーを選択してバックアップを再開できます。

   ![監視するジョブを選択する](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>バックアップ データの削除

バックアップ ジョブの停止中、または保護を停止した後はいつでも、ファイル共有のバックアップを削除できます。 数日または数週間待ってから復旧ポイントを削除する方が便利な場合もあります。 復旧ポイントの復元とは異なり、バックアップ データを削除するときは、特定の復旧ポイントを削除対象として選択することができません。 バックアップ データを削除するように選択すると、その項目に関連付けられている復旧ポイントもすべて削除されます。

次の手順では、仮想マシンのバックアップ ジョブが停止されていることを前提としています。 バックアップ ジョブが停止されたら、[Backup Items]\(バックアップ項目\) ダッシュボードで [バックアップの再開] オプションと [バックアップ データの削除] オプションが使用できるようになります。 [バックアップ データの削除] をクリックし、ファイル共有の名前を入力して削除を確定します。 必要に応じて、削除の理由またはコメントを入力します。

## <a name="see-also"></a>関連項目
Azure ファイル共有の詳細については、以下を参照してください
- [Azure ファイル共有のバックアップに関する FAQ](backup-azure-files-faq.md)
- [Azure ファイル共有のバックアップのトラブルシューティング](troubleshoot-azure-files.md)
