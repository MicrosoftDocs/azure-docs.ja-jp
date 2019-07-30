---
title: Azure のインスタント リストア機能
description: Azure のインスタント リストア機能と、VM バックアップ スタック、Resource Manager デプロイ モデルに関する FAQ
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: sogup
ms.openlocfilehash: 8bbf24fdd05fa0d70bcadae4f21e599dc8bef3a5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465107"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Azure Backup のインスタント リストア機能を使用してバックアップと復元のパフォーマンスを改善する

> [!NOTE]
> ユーザーからのフィードバックに基づき、Azure Stack 機能との混同を避けるため、名前を**VM バックアップ スタック V2** から**インスタント リストア**に変更しています。<br/><br/> Azure のすべてのバックアップ ユーザーは、**インスタント リストア**にアップグレードされました。

インスタント リストアの新しいモデルでは、次の機能が強化されています。

* コンテナーへのデータ転送の終了を待たずに、復旧に利用できるバックアップ ジョブの一環として取得されるスナップショットを使用できます。 これにより、復元をトリガーする前にスナップショットをコンテナーにコピーする待機時間が短縮されます。
* スナップショットを既定で 2 日間ローカルに保持することで、バックアップと復元の時間が短縮されます。 この既定のスナップショット リテンション期間の値は、1 から 5 日の間の任意の値に構成できます。
* 最大 4 TB のディスク サイズがサポートされます。 Azure Backup では、ディスクのサイズ変更は推奨されません。
* Standard HDD ディスクおよび Premium SSD ディスクと共に Standard SSD ディスクがサポートされます。
*   復元時に、(ディスクごとの) アンマネージド VM の元のストレージ アカウントを使用できます。 この機能は、ストレージ アカウント間に分散しているディスクが VM にある場合でも使用できます。 さまざまな VM 構成で復元操作が速くなります。


## <a name="whats-new-in-this-feature"></a>この機能の更新点

現在、バックアップ ジョブは 2 つのフェーズで構成されます。

1.  VM スナップショットを作成します。
2.  Azure Recovery Services コンテナーに VM スナップショットを転送します。

復旧ポイントは、フェーズ 1 および 2 が完了した後でのみ、作成されたものと見なされます。 このアップグレードの一部として、スナップショットが完了するとすぐに復旧ポイントが作成されます。このスナップショットの種類の復旧ポイントを利用し、同じ復元フローを使って復元を実行することができます。 復旧ポイントの種類として "スナップショット" を使用することで、この復旧ポイントを Azure portal で識別することができます。コンテナーにスナップショットが転送された後、復旧ポイントの種類は "スナップショットとコンテナー" に変わります。

![VM バックアップ スタック Resource Manager デプロイメント モデルのバックアップ ジョブ: ストレージとコンテナー](./media/backup-azure-vms/instant-rp-flow.png)

既定で、スナップショットは 2 日間保持されます。 この機能では、復元時間を削減して、これらのスナップショットから復元操作を行うことができます。 それにより、コンテナーからデータを変換して元の場所にコピーするために必要な時間が削減されます。

## <a name="feature-considerations"></a>機能に関する考慮事項

* 復旧ポイントの作成を促進し、復元操作の速度を上げるため、スナップショットはディスクと共に格納されます。 結果として、ストレージのコストはこの期間に取得されたスナップショットに対応したものになります。
* 増分スナップショットは、ページ BLOB として格納されます。 アンマネージド ディスクを使用しているすべてのユーザーは、自分のローカル ストレージ アカウントに格納されているスナップショットに対して課金されます。 マネージド VM バックアップで使用される復元ポイント コレクションでは、基になるストレージ レベルの BLOB スナップショットが使用されるため、マネージド ディスクの場合は、BLOB スナップショットの価格に対応するコストが表示され、これらは増分となります。
* Premium ストレージ アカウントの場合、インスタント復旧ポイント用に作成されるスナップショットは、割り振られているスペースの 10 TB の制限に加算されます。
* 復元のニーズに基づいて、スナップショットの保持期間を構成できます。 以下で説明するように、要件に応じて、バックアップ ポリシー ブレードでスナップショットの保持期間を最短 1 日に設定できます。 頻繁に復元を実行しない場合は、これによりスナップショットの保持期間のコストを節約できます。
* 一方向のアップグレードであり、インスタント アップグレードにアップグレードされると、元に戻すことはできません。

>[!NOTE]
>このインスタント リストアのアップグレードにより、(**新規と既存両方の**) すべての顧客のスナップショット保持期間は、既定で 2 日に設定されるようになります。 ただし、必要に応じて、1 ～ 5 日の任意の値に期間を設定できます。

## <a name="cost-impact"></a>コストの影響

増分スナップショットは VM のストレージ アカウントに格納され、インスタント復旧のために使用されます。 増分スナップショットは、スナップショットによって占有される領域が、スナップショットの作成後に記述されるページによって占有される領域と等しいことを意味します。 課金は、スナップショットによって占有される GB あたりの使用済み領域に対して引き続き行われ、GB あたりの料金は、[価格ページ](https://azure.microsoft.com/pricing/details/managed-disks/)に示されているものと同じになります。

>[!NOTE]
> スナップショットのリテンション期間は、週間ポリシーでは 5 日間に固定されています。

## <a name="configure-snapshot-retention"></a>スナップショットのリテンション期間の構成

### <a name="using-azure-portal"></a>Azure Portal の使用

Azure portal で、**VM バックアップ ポリシー** ブレード ( **[インスタント リストア]** セクションの下) にフィールドが追加されているのを確認できます。 特定のバックアップ ポリシーに関連付けられているすべての VM の **[VM Backup Policy]\(VM バックアップ ポリシー\)** ブレードで、スナップショットの保持期間を変更することができます。

![インスタント リストア機能](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell の使用

>[!NOTE]
> Az PowerShell バージョン 1.6.0 以降では、PowerShell を使用して、ポリシーのインスタント リストアのスナップショット保持期間を更新できます

```powershell
PS C:\> $bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```
各ポリシーの既定のスナップショット リテンション期間は、2 日に設定されます。 ユーザーは、最小で 1 日、最大で 5 日に値を変更できます。 スナップショット リテンション期間は、週間ポリシーでは 5 日に固定されています。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="what-are-the-cost-implications-of-instant-restore"></a>インスタント リストアはコストにどのように影響しますか。
復旧ポイントの作成と復元操作の速度を上げるため、スナップショットはディスクと共に格納されます。 その結果、VM バックアップ ポリシーの一部として選択されるスナップショットの保有期間に対応するストレージ コストが表示されます。

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium ストレージ アカウントで、インスタント復旧ポイント用に作成されたスナップショットは 10 TB のスナップショット制限を占有しますか。
はい。Premium ストレージ アカウントでは、インスタント復元ポイント用に作成されたスナップショットは、割り当てられた 10 TB のスナップショット スペースを占有します。

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>5 日間の期間中、スナップショットの保有期間はどのようになりますか。
毎日新しいスナップショットが取得されるため、個別の増分スナップショットが 5 個になります。 スナップショットのサイズはデータの変化によって異なります (ほとんどの場合、約 2% から 7%)。

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>インスタント リストア スナップショットは増分スナップショットですか、それとも完全スナップショットですか。
インスタント リストア機能の一部として取得されるスナップショットは、増分スナップショットです。

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>インスタント リストア機能による、概算コストの増加はどのように計算すればよいですか。
VM の変化によって異なります。 安定状態では、コストの増加は、スナップショットの保持期間 VM あたりの日次変化 GB あたりのストレージ コストになると考えられます。

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>復元ポイントの回復の種類が "スナップショットとコンテナー" であり、復元操作を実行する場合は、どの回復の種類が使用されますか。
回復の種類が "スナップショットとコンテナー" の場合は、ローカルのスナップショットから自動的に復元が行われ、コンテナーからの復元に比べ、はるかに高速になります。

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>スナップショット (階層 1) の保有期間よりも短い、復元ポイント (階層 2) の保有期間を選択するとどうなりますか。
新しいモデルでは、スナップショット (階層 1) を削除しない限り、復元ポイント (階層 2) を削除することはできません。 復元ポイント (階層 2) の保持期間がスナップショットの保持期間より長くなるようスケジュール設定することをお勧めします。

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>バックアップ ポリシーに設定されている保有期間が過ぎても、スナップショットが存在するのはなぜですか。
回復ポイントにスナップショットがあり、それが利用可能な最新の RP である場合、次のバックアップが成功するまで保持されます。 これは現在、設計されている GC ポリシーに従ったものです。このポリシーでは、VM での問題によって、今後のすべてのバックアップが失敗した場合に、1 つ以上の最新の RP が常に存在することが要求されます。 通常のシナリオでは、有効期限後、最大 24 時間で RP がクリーンアップされます。
