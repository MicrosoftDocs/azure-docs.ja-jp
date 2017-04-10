---
title: "Azure Backup を使用してハイブリッド バックアップを保護するためのセキュリティ機能 | Microsoft Docs"
description: "Azure Backup のセキュリティ機能を使用してバックアップのセキュリティを強化する方法について説明します"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 47bc8423-0a08-4191-826d-3f52de0b4cb8
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 5c49de40401235d38142efd60d22b0591752fe75
ms.openlocfilehash: f17802bf455b82f0b5239356c35024ecde7f1f35
ms.lasthandoff: 02/17/2017


---
# <a name="security-features-for-protecting-hybrid-backups-using-azure-backup"></a>Azure Backup を使用してハイブリッド バックアップを保護するためのセキュリティ機能
多くのお客様がマルウェア、ランサムウェア、侵入などのセキュリティの問題に悩まされています。これらのセキュリティの問題によってデータが失われ、1 件のセキュリティ違反で生じるコストは増え続けています。 このような攻撃を防ぐために、Azure Backup にはハイブリッド バックアップを保護するセキュリティ機能が用意されています。 この記事では、Microsoft Azure Recovery Services Agent と Microsoft Azure Backup Server を使用してこれらの機能を有効にして活用する方法について説明します。 これらの機能は、次に示すセキュリティの&3; 本の柱に基づいています。

1. **防止** - パスフレーズの変更などの重要な操作を実行するたびに、認証レイヤーが追加されます。 この検証により、有効な Azure 資格情報を持つユーザーだけがそのような操作を実行できるようになります。
2. **警告** - バックアップ データの削除などの重要な操作を実行するたびに、サブスクリプションの管理者に電子メール通知が送信されます。 この電子メールにより、ユーザーがそのような操作に関する通知を適切なタイミングで受信できます。
3. **復旧** - 削除日から 14 日間は、削除されたバックアップ データが保持されます。 これにより、特定の期間内のデータの復旧性が確保され、攻撃が行われてもデータが失われることはありません。 また、保持される最小復旧ポイントの数が増えたため、データの破損を防ぐことができます。

> [!NOTE]
> セキュリティ機能は、次に示す機能を使用している場合にのみ有効にする必要があります。 <br/>
> * **MAB エージェント** - エージェントの最小バージョンは 2.0.9052 です。 これらの機能を有効にした場合、パスフレーズの変更、バックアップの停止 (データの削除を含む) などの重要な操作を実行するには、このエージェント バージョンにアップグレードしてください。 <br/>
> * **Azure Backup Server** - MAB エージェントの最小バージョンは 2.0.9052 (Azure Backup Server Update 1) です。 <br/>
> * **DPM** - MAB エージェントの最小バージョンは 2.0.9052 (DPM 2012 R2 UR12 または DPM 2016 UR2) です。 <br/>
> * **IaaS VM バックアップ** - IaaS VM バックアップではこれらの機能を有効にしないでください。 現時点ではこれらの機能を IaaS VM バックアップで使用することはできません。そのため、これらの機能を有効にしても IaaS VM バックアップに対する効果はありません。
> * これらの機能は、Recovery Services コンテナーでのみ使用できます。
> * 新しく作成されるすべての Recovery Services コンテナーでは、これらの機能が既定で有効になります。 既存の Recovery Services コンテナーでは、以下のセクションで説明する手順を使って、ユーザーがこれらの機能を有効にする必要があります。
> * これらの機能を有効にすると、コンテナーに登録されたすべての Azure Recovery Services Agent (MARS) コンピューター、Azure Backup Server、DPM サーバーでセキュリティ機能を使用できます。 <br/>
> * この設定の有効化は&1; 回限りの操作であり、これらの機能を有効にした後で無効にすることはできません。 <br/>
>
>

## <a name="enabling-security-features"></a>セキュリティ機能を有効にする
Recovery Services コンテナーを作成するユーザーは、すべてのセキュリティ機能を使用できます。 既存の Recovery Services コンテナーでこれらの機能を有効にするには、次の手順に従ってください。

1. Azure 資格情報を使用して Azure Portal にログインします。
2. [Hub (ハブ)] メニューで「Recovery Services」と入力して、Recovery Services の一覧に移動します。

    ![Create Recovery Services Vault step 1](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。 この一覧でコンテナーを選択します。

    選択したコンテナーのダッシュボードが開きます。
3. コンテナーの下に表示されている項目の一覧で、**[設定]** の下にある **[プロパティ]** をクリックします。

    ![コンテナーのプロパティを開く](./media/backup-azure-security-feature/vault-list-properties.png)
4. **[セキュリティの設定]** の下にある **[更新]** をクリックします。

    ![セキュリティの設定を開く](./media/backup-azure-security-feature/security-settings-update.png)

    [更新] リンクから [セキュリティの設定] ブレードが開き、これらの機能を有効にしたり、機能の概要を確認したりできます。
5. **[Have you configured Azure Multi-Factor Authentication? (Azure Multi-Factor Authentication を構成しましたか?)]** ドロップダウンで値を選択して、[Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) を有効にしたかどうかを確認します。 有効にした場合は、Azure Portal へのログイン時に別のデバイス (携帯電話など) から認証を実行するよう求められます。

   セキュリティ機能の一部として、Azure Backup で重要な操作を実行する場合は、Azure Portal で使用可能なセキュリティ PIN を入力する必要があります。 Azure Multi-Factor Authentication を有効にすると、セキュリティ レイヤーが追加されます。これにより、有効な Azure 資格情報を持ち、2 つ目のデバイスから認証された許可済みのユーザーだけが Azure Portal にアクセスして、そのような重要な操作を実行できるようになります。
6. 次の図に示すように、トグル ボタンを使用してセキュリティ機能を**有効**にし、上にある **[保存]** ボタンをクリックしてセキュリティの設定を保存します。 **[Enable (有効)]** を選択できるのは、[Have you configured Azure Multi-Factor Authentication? (Azure Multi-Factor Authentication を構成しましたか?)] ドロップダウンで値を選択した後だけです。

    ![セキュリティの設定の有効化](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recovering-deleted-backup-data"></a>削除されたバックアップ データを復旧する
セキュリティ対策として、Azure Backup は削除されたバックアップ データを 14 日間保持し、バックアップの停止 (バックアップ データの削除操作を含む) を実行した場合でもすぐにはデータを削除しません。 14 日以内にデータを復元するには、次の手順に従ってください。

**Microsoft Recovery Services Agent (MARS)** ユーザーの場合:

1. バックアップを作成したコンピューターをまだ使用できる場合は、MARS の [[Recover data to the same machine (同じコンピューターにデータを復旧する)]](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) を選択し、すべての古い復旧ポイントから復旧します。
2. 前述のコンピューターを使用できない場合は、[[Recover to an alternate machine (別のコンピューターに復旧する)]](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) を選択し、別の MARS コンピューターを使用してデータを取得します。

**Azure Backup Server** ユーザーの場合:

1. バックアップを作成したサーバーをまだ使用できる場合は、削除されたデータ ソースを再度保護し、データの復旧機能を使用して、すべての古い復旧ポイントから復旧します。
2. 前述のコンピューターを使用できない場合は、[[Recover data from another Azure Backup Server (別の Azure Backup Server からデータを復旧する)]](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server) を選択し、別の Azure Backup Server を使用してデータを取得します。

**Data Protection Manager (DPM)** のユーザーの場合:

1. バックアップを作成したサーバーをまだ使用できる場合は、削除されたデータ ソースを再度保護し、データの復旧機能を使用して、すべての古い復旧ポイントから復旧します。
2. 前述のコンピューターを使うことができない場合は、「[外部 DPM の追加](backup-azure-alternate-dpm-server.md#recover-data-from-another-azure-backup-server)」に従い、別の DPM サーバーを使ってこのデータを取得します。

## <a name="preventing-attacks"></a>攻撃を防ぐ
この機能の一部として、有効なユーザーだけがさまざまな操作を実行できるようにするためのチェックが追加されました。

### <a name="authentication-to-perform-critical-operations"></a>重要な操作を実行するための認証
重要な操作の認証レイヤー追加の一部として、保護の停止 (データの削除を含む) 操作やパスフレーズの変更操作の実行時にセキュリティ PIN の入力を求められます。

セキュリティ PIN を入手するには、次の手順に従ってください。

1. Azure Portal にログインします。
2. [Recovery Services コンテナー] > [設定] > [プロパティ] に移動します。
3. [セキュリティ PIN] の下にある **[生成]** をクリックします。 [生成] リンクからブレードが開きます。このブレードには、Azure Recovery Services Agent UI で入力するセキュリティ PIN が表示されます。
    PIN が有効なのは 5 分間だけで、その期間が過ぎると自動的に生成されます。

### <a name="maintaining-minimum-retention-range"></a>リテンション期間の最小範囲を維持する
有効な数の復旧ポイントを常に使用可能な状態にしておくために、次のチェック機能が追加されました。

1. 日単位のリテンションの場合、実行されるリテンションの最小値は **7** 日間です。
2. 週単位のリテンションの場合、実行されるリテンションの最小値は **4** 週間です。
3. 月単位のリテンションの場合、実行されるリテンションの最小値は **3** か月です。
4. 年単位のリテンションの場合、実行されるリテンションの最小値は **1** 年です。

## <a name="notifications-for-critical-operations"></a>重要な操作の通知
一部の重要な操作を実行するたびに、操作の詳細を含む電子メール通知がサブスクリプションの管理者に送信されます。 電子メール通知を受信する追加の電子メール ID を構成する場合は、Azure Portal を使用して構成できます。

この記事で説明するセキュリティ機能には、対象を絞った攻撃を防ぎ、攻撃者がバックアップにアクセスできないようにするための防御機構が用意されています。 さらに重要なのは、攻撃が行われてもデータを復旧できる点です。

## <a name="next-steps"></a>次のステップ
* [Azure Recovery Services コンテナーの使用を開始](backup-azure-vms-first-look-arm.md)して、これらの機能を有効にする
* [最新の Azure Recovery Services Agent をダウンロード](http://aka.ms/azurebackup_agent)して、Windows コンピューターを保護し、バックアップ データに対する攻撃を防ぐ
* [最新の Azure Backup Server をダウンロード](https://aka.ms/latest_azurebackupserver)して、ワークロードを保護し、バックアップ データに対する攻撃を防ぐ
* [System Center 2012 R2 Data Protection Manager の UR12 をダウンロード](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)するか、[System Center 2016 Data Protection Manager の UR2 をダウンロード](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager)して、ワークロードを保護し、バックアップ データに対する攻撃を防ぐ

