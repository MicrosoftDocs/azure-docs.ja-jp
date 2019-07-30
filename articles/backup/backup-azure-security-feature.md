---
title: Azure Backup を使用したハイブリッド バックアップを保護するためのセキュリティ機能
description: Azure Backup のセキュリティ機能を使用してバックアップのセキュリティを強化する方法について説明します
author: utraghuv
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/08/2017
ms.author: utraghuv
ms.openlocfilehash: 1e643e74f35b381dfb329453d81615bc69785ed7
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465416"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Azure Backup を使用したハイブリッド バックアップを保護するためのセキュリティ機能
マルウェア、ランサムウェア、侵入などのセキュリティ問題への懸念が高まっています。 これらのセキュリティ問題は、金銭とデータの両方の観点からコストがかかる可能性があります。 このような攻撃を防ぐために、Azure Backup にはハイブリッド バックアップを保護するセキュリティ機能が用意されています。 この記事では、Azure Recovery Services エージェントと Azure Backup Server を使用して、これらの機能を有効にして使用する方法について説明します。 次のような機能が該当します。

- **防止**。 パスフレーズの変更など、重要な操作を実行するたびに、認証レイヤーが追加されます。 この検証により、有効な Azure 資格情報を持つユーザーのみがそのような操作を実行できるようになります。
- **アラート**。 バックアップ データの削除など、重要な操作を実行するたびに、サブスクリプションの管理者に電子メール通知が送信されます。 この電子メールにより、ユーザーはそのような操作に関する通知をすばやく受信できます。
- **復旧**。 削除日から 14 日間は、削除されたバックアップ データが保持されます。 これにより、特定の期間内のデータの復旧性が確保され、攻撃が行われてもデータが失われることはありません。 また、保持される最小復旧ポイントの数が非常に増えたため、データの破損を防ぐことができます。

> [!NOTE]
> サービスとしてのインフラストラクチャ (IaaS) VM バックアップを使用している場合は、セキュリティ機能を有効にしないでください。 現時点ではこれらの機能を IaaS VM バックアップで使用することはできません。そのため、これらの機能を有効にしても効果はありません。 セキュリティ機能は、次に示す機能を使用している場合にのみ有効にする必要があります。 <br/>
>  * **Azure Backup エージェント**。 エージェントの最小バージョンは 2.0.9052 です。 これらの機能を有効にすると、重要な操作を実行するには、このエージェント バージョンにアップグレードする必要があります。 <br/>
>  * **Azure Backup Server**。 Azure Backup エージェントの最小バージョンは 2.0.9052 (Azure Backup Server Update 1) です。 <br/>
>  * **System Center Data Protection Manager**。 Azure Backup エージェントの最小バージョンは 2.0.9052 (Data Protection Manager 2012 R2 UR12 または Data Protection Manager 2016 UR2) です。 <br/>


> [!NOTE]
> これらの機能は、Recovery Services コンテナーでのみ使用できます。 新しく作成されるすべての Recovery Services コンテナーでは、これらの機能が既定で有効になります。 既存の Recovery Services コンテナーでは、次のセクションで説明する手順に従ってこれらの機能を有効にできます。 機能が有効になったら、そのコンテナーに登録されているすべての Recovery Services エージェント コンピューター、Azure Backup Server インスタンス、および Data Protection Manager サーバーに機能が適用されます。 この設定の有効化は 1 回限りの操作であり、これらの機能を有効にした後で無効にすることはできません。
>

## <a name="enable-security-features"></a>セキュリティ機能の有効化
Recovery Services コンテナーを作成している場合、すべてのセキュリティ機能を使用できます。 既存のコンテナーで作業している場合、次の手順に従ってセキュリティ機能を有効にします。

1. Azure 資格情報を使用して、Azure Portal にサインインします。
2. **[参照]** を選択し、「**Recovery Services**」と入力します。

    ![Azure Portal の [参照] オプションのスクリーンショット](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Recovery Services コンテナーの一覧が表示されます。 この一覧でコンテナーを選択します。 選択したコンテナーのダッシュボードが開きます。
3. コンテナーの下に表示されている項目一覧で、 **[設定]** の下にある **[プロパティ]** をクリックします。

    ![Recovery Services コンテナーのオプションのスクリーンショット](./media/backup-azure-security-feature/vault-list-properties.png)
4. **[セキュリティの設定]** の下にある **[更新]** をクリックします。

    ![Recovery Services コンテナーのプロパティのスクリーンショット](./media/backup-azure-security-feature/security-settings-update.png)

    [更新] リンクから **[セキュリティの設定]** ブレードが開きます。ここで機能の概要を確認したり、機能を有効にしたりすることができます。
5. **[Have you configured Azure Multi-Factor Authentication? (Azure Multi-Factor Authentication を構成しましたか?)]** ドロップダウン リストから値を選択し、[Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) を有効にしたかどうかを確認する値を選択します。 有効にした場合は、Azure Portal へのサインイン時に別のデバイス (携帯電話など) から認証を実行するように求められます。

   Backup で重要な操作を実行する場合、Azure Portal で使用可能なセキュリティ PIN を入力する必要があります。 Azure Multi-Factor Authentication を有効にすると、セキュリティ レイヤーが追加されます。 有効な Azure 資格情報を持ち、2 番目のデバイスから認証された承認済みのユーザーのみが Azure Portal にアクセスできます。
6. セキュリティ設定を保存するには、 **[有効]** を選択して、 **[保存]** をクリックします。 **[有効]** を選択できるのは、前の手順で **[Have you configured Azure Multi-Factor Authentication? (Azure Multi-Factor Authentication を構成しましたか?)]** リストから値を選択した場合のみです。

    ![セキュリティ設定のスクリーンショット](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>削除されたバックアップ データの復旧
Azure Backup では削除されたバックアップ データが 14 日間保持され、**バックアップの停止 (バックアップ データの削除操作を含む)** を実行した場合でも、すぐにはデータが削除されません。 14 日以内にこのデータを復元するには、使用しているものに応じて以下の手順を実行します。

**Azure Recovery Services エージェント** ユーザーの場合:

1. バックアップを実行したコンピューターをまだ使用できる場合、削除されたデータ ソースを再保護し、Azure Recovery Services の [[Recover data to the same machine]\(同じコンピューターにデータを復旧する\)](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) を使用して、すべての古い復旧ポイントから復旧します。
2. このコンピューターを使用できない場合、[[Recover to an alternate machine (別のコンピューターに復旧する)]](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) を選択し、別の Azure Recovery Services コンピューターを使用してデータを取得します。

**Azure Backup Server** ユーザーの場合:

1. バックアップを作成したサーバーをまだ使用できる場合は、削除されたデータ ソースを再度保護し、**データの復旧**機能を使用して、すべての古い復旧ポイントから復旧します。
2. このサーバーを使用できない場合は、[[Recover data from another Azure Backup Server (別の Azure Backup Server からデータを復旧する)]](backup-azure-alternate-dpm-server.md) を選択し、別の Azure Backup Server インスタンスを使用してデータを取得します。

**Data Protection Manager** ユーザーの場合:

1. バックアップを作成したサーバーをまだ使用できる場合は、削除されたデータ ソースを再度保護し、**データの復旧**機能を使用して、すべての古い復旧ポイントから復旧します。
2. このサーバーを使用できない場合、[[外部 DPM の追加]](backup-azure-alternate-dpm-server.md) をクリックして、別の Data Protection Manager サーバーを使用してこのデータを取得します。

## <a name="prevent-attacks"></a>攻撃の防止
有効なユーザーのみが各種操作を実行できるようにするためのチェックが追加されました。 たとえば、認証レイヤーの追加や、回復を行うための最小リテンション期間の維持などが含まれています。

### <a name="authentication-to-perform-critical-operations"></a>重要な操作を実行するための認証
重要な操作の認証レイヤー追加の一部として、**保護の停止 (データの削除を含む)** 操作や**パスフレーズの変更**操作の実行時にセキュリティ PIN の入力を求められます。

> [!NOTE]
> 
> 現時点では、セキュリティ PIN は DPM および MABS の**保護の停止 (データの削除を含む)** ではサポートされていません。

この PIN を受け取るには次のようにします。

1. Azure ポータルにサインインします。
2. **[Recovery Services コンテナー]**  >  **[設定]**  >  **[プロパティ]** の順に参照します。
3. **[セキュリティ PIN]** の下にある **[生成]** をクリックします。 これにより、Azure Recovery Services エージェントのユーザー インターフェイスに入力する PIN が含まれたブレードが開きます。
    この PIN が有効なのは 5 分間のみであり、その期間が過ぎると別のものが自動生成されます。

### <a name="maintain-a-minimum-retention-range"></a>リテンション期間の最小範囲の維持
有効な数の復旧ポイントを常に使用可能な状態にしておくために、次のチェック機能が追加されました。

- 日単位のリテンション期間の場合、実行されるリテンション期間の最小値は **7** 日間です。
- 週単位のリテンション期間の場合、実行されるリテンション期間の最小値は **4** 週間です。
- 月単位のリテンション期間の場合、実行されるリテンション期間の最小値は **3** か月です。
- 年単位のリテンション期間の場合、実行されるリテンション期間の最小値は **1** 年です。

## <a name="notifications-for-critical-operations"></a>重要な操作の通知
通常、重要な操作が実行されると、操作の詳細が含まれた電子メール通知がサブスクリプションの管理者に送信されます。 Azure Portal を使用して、これらの通知の電子メール受信者を追加構成できます。

この記事で説明するセキュリティ機能には、対象を絞った攻撃を防ぐための防御機構が用意されています。 さらに重要なことは、攻撃が行われても、これらの機能を使用してデータを回復できるということです。

## <a name="troubleshooting-errors"></a>エラーのトラブルシューティング
| Operation | エラーの詳細 | 解決策 |
| --- | --- | --- |
| ポリシーの変更 |バックアップ ポリシーを変更できませんでした。 エラー:サービスの内部エラー [0x29834] が発生したため、現在の操作を実行できませんでした。 しばらくしてから、操作をやり直してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |**原因:**<br/>このエラーは、セキュリティ設定が有効になっており、リテンション範囲を上記の最小値を下回るように減らそうとしたものの、サポートされていないバージョンを使用していると発生します (サポート対象のバージョンはこの記事の最初のメモに記載されています)。 <br/>**推奨される操作:**<br/> このケースでは、指定した最小リテンション期間 (日次の場合は 7 日間、週次の場合は 4 週間、月次の場合は 3 か月、年次の場合は 1 年) を上回るようにリテンション期間を設定し、ポリシー関連の更新を進めます。 他にもお勧めの方法として、バックアップ エージェント、Azure Backup Server、または DPM UR を更新して、すべてのセキュリティ更新プログラムを適用する方法があります。 |
| パスフレーズの変更 |入力されたセキュリティ PIN が正しくありません。 (ID: 100130) この操作を完了するには、正しいセキュリティ PIN を指定してください。 |**原因:**<br/> このエラーは、重大な操作 (パスフレーズの変更など) を実行している間に、無効または有効期限の切れたセキュリティ PIN を入力すると発生します。 <br/>**推奨される操作:**<br/> 操作を完了するには、有効なセキュリティ PIN を入力する必要があります。 PIN を取得するには、Azure Portal にログインし、Recovery Services コンテナーで [設定]、[プロパティ]、[セキュリティ PIN の生成] の順に移動します。 パスフレーズの変更にはこの PIN を使用します。 |
| パスフレーズの変更 |操作に失敗しました。 ID: 120002 |**原因:**<br/>このエラーは、セキュリティ設定が有効になっており、パスフレーズを変更しようとしたものの、サポートされていないバージョンを使用していると発生します (サポート対象のバージョンはこの記事の最初のメモに記載されています)。<br/>**推奨される操作:**<br/> パスフレーズを変更するには、まずバックアップ エージェントを 2.0.9052 以上、Azure Backup Server を update 1 以上、DPM を 2012 R2 UR12 または DPM 2016 UR2 (以下のリンクからダウンロード) に更新してから有効なセキュリティ PIN を入力する必要があります。 PIN を取得するには、Azure Portal にログインし、Recovery Services 資格情報コンテナーで [設定]、[プロパティ]、[セキュリティ PIN の生成] の順に移動します。 パスフレーズの変更にはこの PIN を使用します。 |

## <a name="next-steps"></a>次の手順
* [Azure Recovery Services コンテナーの使用を開始](backup-azure-vms-first-look-arm.md)して、これらの機能を有効にします。
* [最新の Azure Recovery Services Agent をダウンロード](https://aka.ms/azurebackup_agent)して、Windows コンピューターを保護し、バックアップ データに対する攻撃を防ぎます。
* [最新の Azure Backup Server をダウンロード](https://aka.ms/latest_azurebackupserver)して、ワークロードを保護し、攻撃からバックアップ データを保護します。
* [System Center 2012 R2 Data Protection Manager の UR12 をダウンロード](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager)するか、[System Center 2016 Data Protection Manager の UR2 をダウンロード](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager)して、ワークロードを保護し、バックアップ データに対する攻撃を防ぎます。
