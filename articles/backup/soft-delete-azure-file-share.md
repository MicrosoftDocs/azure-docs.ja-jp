---
title: Azure ファイル共有を誤削除から保護する
description: 論理的な削除によって Azure ファイル共有を誤削除から保護する方法について説明します。
ms.topic: conceptual
ms.date: 02/02/2020
ms.custom: references_regions
ms.openlocfilehash: 52a116320e07c25d4ee7f00b8063ca15faeb8560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179914"
---
# <a name="accidental-delete-protection-for-azure-file-shares-using-azure-backup"></a>Azure Backup を使用して Azure ファイル共有を誤削除から保護する

ストレージ アカウントのすべてのファイル共有は、個々のストレージ アカウントでファイル共有のバックアップを構成する際に、サイバー攻撃や誤削除への保護対策として[論理的な削除](../storage/files/storage-files-prevent-file-share-deletion.md)が有効になります。 論理的な削除が有効になっていれば、悪意のあるアクターがファイル共有を削除しても、その後少なくとも 14 日間は、ファイル共有の内容と復旧ポイント (スナップショット) が維持されるため、データの損失を招くことなく、ファイル共有を復旧することができます。  論理的な削除は Standard および Premium ストレージ アカウントでサポートされており、その設定は Azure Backup によって、バックアップされたファイル共有をホストしているすべてのストレージ アカウントで有効にされます。

次のフロー チャートは、ストレージ アカウントのファイル共有に対して論理的な削除が有効になっている場合のさまざまな手順とバックアップ項目の状態を示しています。

 ![論理的な削除のフロー チャート](./media/soft-delete-afs/soft-delete-flow-chart.png)

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="when-will-soft-delete-be-enabled-for-file-shares-in-my-storage-account"></a>ストレージ アカウントのファイル共有に対して論理的な削除はいつ有効になるですか?

ストレージ アカウントのファイル共有に対して初めてバックアップを構成する際、Azure Backup サービスによって、個々のストレージ アカウントのすべてのファイル共有に対して論理的な削除が有効化されます。

### <a name="can-i-configure-the-number-of-days-for-which-my-snapshots-and-restore-points-will-be-retained-in-soft-deleted-state-after-i-delete-the-file-share"></a>ファイル共有を削除した後、論理的に削除された状態でスナップショットおよび復元ポイントが保持される日数は構成できますか?

はい。保持期間は、実際の要件に応じて設定することができます。 保持期間を構成する手順の説明は、[こちらのドキュメント](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal)でご覧いただけます。 ファイル共有のバックアップが使用されたストレージ アカウントでは、最短の保持期間が 14 日に設定されます。

### <a name="does-azure-backup-reset-my-retention-setting-because-i-configured-it-to-less-than-14-days"></a>Azure Backup によって保持期間の設定がリセットされたのは、私がそれを 14 日未満に構成したためでしょうか?

ファイル共有のバックアップが使用されたストレージ アカウントでは、セキュリティの観点から、最小保持期間を 14 日とすることが推奨されます。 そのため、バックアップ ジョブが実行されるたびに、14 日未満になっている設定は Azure Backup によって検出され、14 日間にリセットされます。

### <a name="what-is-the-cost-incurred-during-the-retention-period"></a>保持期間中に発生するコストはどういったものですか?

論理的に削除された状態の間も、保護されたインスタンスのコストとスナップショット ストレージのコストは変わりません。  また、使用済み容量に関して、Standard ファイル共有では通常の料金が、Premium ファイル共有ではスナップショット ストレージ料金が課金されます。

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-deleted-state"></a>データが論理的な削除状態であるときに復元操作を実行できますか?

復元操作を実行するには、論理的に削除されたファイル共有の削除を先に取り消しておく必要があります。 削除の取り消し操作によって、ファイル共有がバックアップ状態になり、任意の時点への復元を実行できるようになります。 ファイル共有の削除を取り消す方法については、[こちらのリンク](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)をクリックするか、または[ファイル共有の削除を取り消すためのスクリプト](./scripts/backup-powershell-script-undelete-file-share.md)を参照してください。

### <a name="how-can-i-purge-the-data-of-a-file-share-in-a-storage-account-that-has-at-least-one-protected-file-share"></a>保護されたファイル共有が少なくとも 1 つ存在するストレージ アカウントからファイル共有のデータを消去するにはどうすればよいのでしょうか?

保護されたファイル共有がストレージ アカウントに 1 つでも存在する場合、そのアカウントに含まれるすべてのファイル共有で論理的な削除が有効化され、削除操作の実行後 14 日間データは保持されます。 ただし、データを保持せずに、すぐに消去したい場合は、次の手順に従ってください。

1. 論理的な削除が有効な状態でファイル共有を削除した場合は、まず、[Files ポータル](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)から、または[ファイル共有の削除を取り消すためのスクリプト](./scripts/backup-powershell-script-undelete-file-share.md)を使用して、ファイル共有の削除を取り消します。
2. [こちらのドキュメント](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#disable-soft-delete)に記載された手順に従って、ストレージ アカウントのファイル共有に対する論理的な削除を無効にします。
3. すぐに消去したい内容を含んでいるファイル共有を削除します。

>[!NOTE]
>手順 2. は、ストレージ アカウント内の保護されたファイル共有に対して、次回にスケジュールされているバックアップ ジョブが実行される前に行う必要があります。 バックアップ ジョブが実行されるたびに、ストレージ アカウント内のすべてのファイル共有に対して再度、論理的な削除が有効になります。

>[!WARNING]
>手順 2. で論理的な削除を無効にした後にファイル共有に対して実行する削除操作はすべて、完全な削除操作になります。 そのため、論理的な削除を無効にした後、バックアップ済みのファイル共有を誤って削除した場合、すべてのスナップショットが失われ、データを復旧することはできません。

### <a name="in-the-context-of-a-file-shares-soft-delete-setting-what-changes-does-azure-backup-do-when-i-unregister-a-storage-account"></a>ストレージ アカウントの登録が解除されたとき、Azure Backup は、ファイル共有の論理削除設定との関連で、どのような変更を行うのでしょうか?

登録解除の時点で、Azure Backup は、ファイル共有の保持期間の設定をチェックし、14 日を超えているか、または 14 日未満であった場合、保持設定をそのまま残します。 一方、保持期間が 14 日であった場合、それは Azure Backup によって有効にされたものであると見なされ、登録解除のプロセス中に論理的な削除は無効化されます。 保持設定を残したままストレージ アカウントの登録を解除したい場合は、登録解除の完了後、[ストレージ アカウント] ペインから再度設定を有効にしてください。 構成手順については、[こちらのリンク](../storage/files/storage-files-enable-soft-delete.md?tabs=azure-portal#restore-soft-deleted-file-share)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure portal から Azure ファイル共有をバックアップする](backup-afs.md)方法を学習します
