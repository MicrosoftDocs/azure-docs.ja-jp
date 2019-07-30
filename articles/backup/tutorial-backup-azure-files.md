---
title: Azure Backup サービスを使用した Azure Files のファイル共有のバックアップ
description: このチュートリアルでは、Azure ファイル共有をバックアップする方法について説明します。
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: a06504438340f62d5b338165cb8b159459ae35dc
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467073"
---
# <a name="back-up-azure-file-shares"></a>Azure ファイル共有のバックアップ
この記事では、Azure Portal を使用して [Azure ファイル共有](../storage/files/storage-files-introduction.md)のバックアップと復元を行う方法について説明します。

このガイドでは、以下の方法について説明します。
> [!div class="checklist"]
> * Recovery Services コンテナーを構成して Azure Files をバックアップできるようにする
> * オンデマンド バックアップ ジョブを実行して復元ポイントを作成する


## <a name="prerequisites"></a>前提条件
Azure ファイル共有をバックアップする前に、[サポートされているストレージ アカウントの種類](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview)のいずれかにそれが存在することを確認しておいてください。 この確認が完了したら、ファイル共有を保護することができます。

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>プレビュー期間における Azure ファイル共有のバックアップの制限
Azure ファイル共有のバックアップはプレビュー段階です。 汎用 v1 ストレージ アカウントと汎用 v2 ストレージ アカウント、どちらの Azure ファイル共有もサポートされています。 次のバックアップ シナリオは、Azure ファイル共有ではサポートされていません。
- 仮想ネットワークまたはファイアウォールが有効になっているストレージ アカウントの Azure ファイル共有を保護することはできません。
- Azure Backup を使用して Azure Files を保護するために利用できる CLI はありません。
- スケジュール バックアップの数は、1 日につき 1 個が上限となります。
- オンデマンド バックアップの数は、1 日につき 4 個が上限となります。
- ストレージ アカウントに対する[リソース ロック](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest)を使用して、Recovery Services コンテナー内のバックアップを誤って削除しないようにします。
- Azure Backup によって作成されたスナップショットを削除しないでください。 スナップショットを削除すると、復旧ポイントが失われたり、復元が失敗したりする場合があります。
- Azure Backup で保護されているファイル共有は削除しないでください。 現在のソリューションでは、ファイル共有が削除されると Azure Backup によって取得されたスナップショットがすべて削除されます。そのため、すべての復元ポイントが失われます。

[ゾーン冗長ストレージ](../storage/common/storage-redundancy-zrs.md) (ZRS) レプリケーションを使用するストレージ アカウントでの Azure ファイル共有のバックアップは、現在、米国中部 (CUS)、米国東部 (EUS)、米国東部 2 (EUS2)、北ヨーロッパ (NE)、東南アジア (SEA)、西ヨーロッパ (WE)、米国西部 2 (WUS2) でのみ使用できます。

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure ファイル共有のバックアップの構成
このチュートリアルでは、Azure ファイル共有を既に確立していることを前提としています。 Azure ファイル共有をバックアップするには:

1. 使用しているファイル共有と同じリージョンに Recovery Services コンテナーを作成します。 コンテナーが既にある場合は、そのコンテナーの [概要] ページを開き、 **[バックアップ]** をクリックします。

    ![コンテナーの [概要] ページで [バックアップ] をクリックする](./media/backup-file-shares/overview-backup-page.png)

2. **[バックアップの目標]** メニューの **[What do you want to back up?]\(何をバックアップしますか?\)** で [Azure ファイル共有] を選択します。

    ![バックアップの目標として Azure ファイル共有を選択する](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. **[バックアップ]** をクリックして、Recovery Services コンテナーに対して Azure ファイル共有を構成します。

   ![[バックアップ] をクリックして Azure ファイル共有をコンテナーに関連付ける](./media/backup-file-shares/set-backup-goal.png)

    コンテナーが Azure ファイル共有に関連付けられると、[バックアップ] メニューが開き、ストレージ アカウントを選択するように求められます。 このメニューには、対象のコンテナーが存在するリージョン内にある、まだ Recovery Services コンテナーに関連付けられていない、サポートされているストレージ アカウントがすべて表示されます。

   ![使うストレージ アカウントを選ぶ](./media/backup-file-shares/list-of-storage-accounts.png)

4. ストレージ アカウントの一覧でアカウントを選択し、 **[OK]** をクリックします。 Azure は、バックアップ可能なファイル共有のストレージ アカウントを検索します。 最近ファイル共有を追加していても一覧に表示されない場合は、ファイル共有が表示されるまで少し時間を置いてください。

   ![ファイル共有の検出中](./media/backup-file-shares/discover-file-shares.png)

5. **[ファイル共有]** の一覧から、バックアップするファイル共有を 1 つ以上選択し、 **[OK]** をクリックします。

6. ファイル共有を選択すると、[バックアップ] メニューが **[バックアップ ポリシー]** に切り替わります。 このメニューから、既存のバックアップ ポリシーを選択するか新しいバックアップ ポリシーを作成し、 **[バックアップの有効化]** をクリックします。

   ![バックアップ ポリシーを選択する、または新しいバックアップ ポリシーを作成する](./media/backup-file-shares/apply-backup-policy.png)

    バックアップ ポリシーを確立すると、スケジュールされた時刻にファイル共有のスナップショットが作成され、復旧ポイントは選択した期間保持されます。

## <a name="create-an-on-demand-backup"></a>オンデマンド バックアップの作成
バックアップ ポリシーを構成したら、次回のスケジュール バックアップまでデータが確実に保護されるように、オンデマンド バックアップを作成することをお勧めします。


### <a name="to-create-an-on-demand-backup"></a>オンデマンド バックアップを作成するには

1. ファイル共有の復旧ポイントが含まれている Recovery Services コンテナーを開き、 **[Backup Items]\(バックアップ項目\)** をクリックします。 バックアップ項目の種類の一覧が表示されます。

   ![バックアップ項目の一覧](./media/backup-file-shares/list-of-backup-items.png)

2. 一覧から **[Azure Storage (Azure Files)]** を選択します。 Azure ファイル共有の一覧が表示されます。

   ![Azure ファイル共有の一覧](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Azure ファイル共有の一覧から、目的のファイル共有を選択します。 選択したファイル共有の [Backup Item]\(バックアップ項目\) メニューが開きます。

   ![選択したファイル共有の [Backup Item]\(バックアップ項目\) メニュー](./media/backup-file-shares/backup-item-menu.png)

4. [Backup Item]\(バックアップ項目\) メニューから **[今すぐバックアップ]** をクリックします。 これはオンデマンド バックアップ ジョブのため、復旧ポイントに関連付けられた保持ポリシーはありません。 **[今すぐバックアップ]** ダイアログが開きます。 復旧ポイントの保持期限を指定します。

   ![復旧ポイントの保持期間を選択する](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure portal を使用して以下を行いました。

> [!div class="checklist"]
> * Recovery Services コンテナーを構成して Azure Files をバックアップできるようにする
> * オンデマンド バックアップ ジョブを実行して復元ポイントを作成する

次の記事に進み、Azure ファイル共有のバックアップから復元します。

> [!div class="nextstepaction"]
> [Azure ファイル共有のバックアップから復元する](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
