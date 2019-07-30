---
title: Azure Backup の失敗のトラブルシューティング:ゲスト エージェントの状態を確認できない
description: エージェント、拡張機能、ディスクに関する Azure Backup のエラーの症状、原因、解決策。
author: saurabhsensharma
manager: saurabhsensharma
keywords: Azure Backup; VM エージェント; ネットワーク接続;
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: e4337c9c89ca239bb664cbb7fb953ab9eedd3ac5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465331"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup の失敗のトラブルシューティング:エージェント/拡張機能に関する問題

この記事では、VM エージェントと拡張機能との通信に関連する Azure Backup エラーの解決に役立つ可能性のあるトラブルシューティング手順について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]



## <a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM agent unable to communicate with Azure Backup (VM エージェントが Azure Backup と通信できません)

**エラー コード**:UserErrorGuestAgentStatusUnavailable <br>
**エラー メッセージ**:VM エージェントが Azure Backup と通信できない<br>

Backup サービスに VM を登録してスケジュール設定すると、Backup サービスは、VM エージェントと通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされずにバックアップが失敗する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。<br>
**原因 1:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**     
**原因 4:[バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  
**原因 5:[VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Could not communicate with the VM agent for snapshot status (スナップショットの状態について VM エージェントと通信できませんでした)

**エラー コード**:GuestAgentSnapshotTaskStatusError<br>
**エラー メッセージ**:スナップショットの状態について VM エージェントと通信できませんでした <br>

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - The Restore Point collection max limit has reached (復元ポイント コレクションの上限に達しました)

**エラー コード**:UserErrorRpCollectionLimitReached <br>
**エラー メッセージ**:復元ポイント コレクションの上限に達しました。 <br>
* この問題は、復旧ポイントの自動クリーンアップを妨げる、復旧ポイント リソース グループに対するロックが存在する場合に発生することがあります。
* この問題は、1 日に複数のバックアップがトリガーされる場合にも発生することがあります。 現時点では、インスタント復元ポイントは設定されているスナップショット保有期間基づき 1 日から 5 日間保持され、任意の時点で VM に関連付けることができるインスタント RP は 18 個だけであるため、1 日に 1 つだけのバックアップをお勧めします。 <br>

推奨される操作:<br>
この問題を解決するには、VM のリソース グループに対するロックを解除して、クリーンアップをトリガーする操作を再試行します。
> [!NOTE]
> Backup サービスでは、復元ポイント コレクションを格納する VM のリソース グループとは別のリソース グループが作成されます。 Backup サービスに使用するために作成されたリソース グループはロックしないことをお勧めします。 Backup サービスによって作成されるリソース グループの名前付け形式は次のとおりです。AzureBackupRG_`<Geo>`_`<number>` 例:AzureBackupRG_northeurope_1

**手順 1:[復元ポイントのリソース グループのロックを解除する](#remove_lock_from_the_recovery_point_resource_group)** <br>
**手順 2:[復元ポイント コレクションをクリーンアップする](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Backup のキー コンテナーに対するアクセス許可は、暗号化された VM をバックアップするには十分ではありません

**エラー コード**:UserErrorKeyvaultPermissionsNotConfigured <br>
**エラー メッセージ**:Backup のキー コンテナーに対するアクセス許可は、暗号化された VM をバックアップするのに十分ではありません。 <br>

暗号化された VM のバックアップ操作を正常に完了するには、キー コンテナーへのアクセス許可が必要です。 これを行うには、[Azure portal](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption) または [PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#enable-protection) を使用します。

## <a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Snapshot operation failed due to no network connectivity on the virtual machine (仮想マシンがネットワークに接続していないためにスナップショット操作が失敗しました)

**エラー コード**:ExtensionSnapshotFailedNoNetwork<br>
**エラー メッセージ**:仮想マシンがネットワークに接続していないためにスナップショット操作が失敗した<br>

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。    
**原因 1:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2:[バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  
**原因 3:[VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**

## <a name="ExtentionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtentionOperationFailedForManagedDisks - VMSnapshot の拡張操作に失敗しました。

**エラー コード**:ExtentionOperationFailedForManagedDisks <br>
**エラー メッセージ**:VMSnapshot 拡張機能の操作に失敗した<br>

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2:[バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  
**原因 3:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 4:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 - Backup fails, with an internal error (内部エラーでバックアップに失敗しました)

**エラー コード**:BackUpOperationFailed / BackUpOperationFailedV2 <br>
**エラー メッセージ**:バックアップ操作は内部エラーのため失敗しました - 数分以内に操作をやり直してください <br>

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[ エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 4:[バックアップ拡張機能の更新または読み込みに失敗した](#the-backup-extension-fails-to-update-or-load)**  
**原因 5:リソース グループのロックが原因で、バックアップ サービスに古い復元ポイントを削除するためのアクセス許可がない** <br>
**原因 6:[VM がインターネットにアクセスできない](#the-vm-has-no-internet-access)**

## <a name="usererrorunsupporteddisksize---currently-azure-backup-does-not-support-disk-sizes-greater-than-4095gb"></a>UserErrorUnsupportedDiskSize - 現在、Azure Backup では 4095 GB を超えるディスク サイズはサポートされていません

**エラー コード**:UserErrorUnsupportedDiskSize <br>
**エラー メッセージ**:現在、Azure Backup では 4095 GB を超えるディスク サイズはサポートされていません <br>

VM をバックアップするときディスク サイズが 4095 GB よりも大きい場合、バックアップ操作が失敗することがあります。 大容量ディスクのサポートは近日対応予定です。  

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 別のバックアップ操作が進行中であるためバックアップを開始できません

**エラー コード**:UserErrorBackupOperationInProgress <br>
**エラー メッセージ**:別のバックアップ操作が進行中であるためバックアップを開始できません<br>

進行中のバックアップ ジョブがあるため、最新のバックアップ ジョブが失敗しました。 新しいバックアップ ジョブは、現在のジョブが完了するまで開始できません。 他のバックアップ操作をトリガーまたはスケジュールする前に、進行中のバックアップ操作が完了していることを確認します。 バックアップ ジョブの状態を確認するには、以下の手順を実行します。

1. Azure portal にサインインし、 **[すべてのサービス]** をクリックします。 「Recovery Services」と入力し、 **[Recovery Services コンテナー]** をクリックします。 Recovery Services コンテナーの一覧が表示されます。
2. Recovery Services コンテナーの一覧から、バックアップの構成先のコンテナーを選択します。
3. コンテナーのダッシュボード メニューの **[バックアップ ジョブ]** をクリックすると、すべてのバックアップ ジョブが表示されます。

    * バックアップ ジョブが進行中の場合は、そのジョブが完了するまで待機する、そのバックアップ ジョブを取り消します。
        * バックアップ ジョブを取り消すには、そのバックアップ ジョブを右クリックして **[キャンセル]** をクリックするか、[PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0) を使用します。
    * 別のコンテナーでバックアップを再構成した場合は、その後、古いコンテナーで実行されているバックアップ ジョブがないことを確認します。 存在する場合は、バックアップ ジョブを取り消します。
        * バックアップ ジョブを取り消すには、そのバックアップ ジョブを右クリックして **[キャンセル]** をクリックするか、[PowerShell](https://docs.microsoft.com/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob?view=azps-1.4.0) を使用します
4. バックアップ操作を再試行してください。

スケジュールしたバックアップ操作に長い時間がかかり、次のバックアップの構成と競合している場合は、[ベスト プラクティス](backup-azure-vms-introduction.md#best-practices)、[バックアップ パフォーマンス](backup-azure-vms-introduction.md#backup-performance)、[復元に関する考慮事項](backup-azure-vms-introduction.md#backup-and-restore-considerations)について確認してください。


## <a name="causes-and-solutions"></a>原因とソリューション

### <a name="the-vm-has-no-internet-access"></a>VM がインターネットにアクセスできない
デプロイ要件に準拠したインターネット アクセスが VM にありません。 または、Azure インフラストラクチャへのアクセスを禁止する制限が設けられている可能性があります。

バックアップ拡張機能が正常に機能するには、Azure のパブリック IP アドレスへの接続が必要です。 この拡張機能が、VM のスナップショットを管理するコマンドを、Azure Storage エンドポイント (HTTP URL) に送信するためです。 拡張機能がパブリック インターネットにアクセスできない場合は、最終的にバックアップが失敗します。

####  <a name="solution"></a>解決策
ネットワークの問題を解決するには、「[ネットワーク接続を確立する](backup-azure-arm-vms-prepare.md#establish-network-connectivity)」を参照してください。

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>エージェントが VM にインストールされているが応答しない (Windows VM の場合)

#### <a name="solution"></a>解決策
VM エージェントが破損しているまたはサービスが停止している可能性があります。 VM エージェントを再インストールすることで最新バージョンを入手できます。 その際に、サービスとの通信も再開されます。

1. VM サービス (services.msc) で Windows Azure ゲスト エージェント サービスが実行されているかどうかを確認します。 Windows Azure ゲスト エージェント サービスの再起動とバックアップの開始を試みます。    
2. コントロール パネルの [サービス] に Windows Azure ゲスト エージェント サービスが表示されない場合は、 **[プログラムと機能]** に移動し、Windows Azure ゲスト エージェント サービスがインストールされているかどうかを確認してください。
4. Windows Azure ゲスト エージェント サービスが **[プログラムと機能]** に表示される場合は、Windows Azure ゲスト エージェントをアンインストールします。
5. [最新バージョンのエージェント MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。
6. [サービス] に Windows Azure ゲスト エージェント サービスが表示されることを確認します。
7. オンデマンド バックアップを実行します。
    * ポータルの **[今すぐバックアップ]** を選択します。

さらに、VM に [Microsoft .NET 4.5 がインストールされていること](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)を確認します。 VM エージェントがサービスと通信するためには .NET 4.5 が必要です。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM にインストールされているエージェントが古くなっている (Linux VM の場合)

#### <a name="solution"></a>解決策
Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 この問題のトラブルシューティングを行うには、次の一般的なガイドラインに従います。

1. [Linux VM エージェントを更新](../virtual-machines/linux/update-agent.md)する手順に従います。

   > [!NOTE]
   > ディストリビューション リポジトリを通してのみエージェントを更新することを "*強くお勧め*" します。 エージェント コードを直接 GitHub からダウンロードして、更新することはお勧めしません。 最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 最新のエージェントを確認するには、GitHub リポジトリの [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)のページをご覧ください。

2. `ps -e` コマンドを実行して、Azure エージェントが VM で実行されていることを確認します。

   このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。

   * Ubuntu の場合: `service walinuxagent start`
   * その他のディストリビューションの場合: `service waagent start`

3. [エージェントの自動再起動を構成します](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 新しいテスト バックアップを実行します。 エラーが解決しない場合は、VM から次のログを収集してください。

   * /var/lib/waagent/*.xml
   * /var/log/waagent.log
   * /var/log/azure/*

waagent の詳細ログが必要な場合は、次の手順に従います。

1. /etc/waagent.conf ファイルで、次の行を見つけます:**Enable verbose logging (y|n)**
2. **Logs.Verbose** の値を *n* から *y* に変更します。
3. 変更を保存した後、このセクションで前述した手順を実行して waagent を再起動します。

###  <a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>スナップショットの状態を取得できないか、スナップショットを作成できない
VM のバックアップは、基礎となるストレージ アカウントへのスナップショット コマンドの発行に依存します。 ストレージ アカウントにアクセスできないか、スナップショット タスクの実行が遅れているために、バックアップが失敗することがあります。

#### <a name="solution"></a>解決策
次の場合にスナップショットのタスクが失敗することがあります。

| 原因 | 解決策 |
| --- | --- |
| VM が リモート デスクトップ プロトコル (RDP) でシャットダウンされているため、VM の状態が正しく報告されない。 | RDP で VM をシャットダウンした場合は、ポータルでその VM の状態が正しいかどうかを確認します。 正しくない場合は、VM のダッシュボードの **[シャットダウン]** オプションを使用して、ポータルで VM をシャットダウンします。 |
| VM が DHCP からホスト/ファブリック アドレスを取得できない。 | IaaS VM バックアップが正しく機能するには、ゲスト内で DHCP が有効になっている必要があります。 VM が DHCP 応答 245 からホスト/ファブリック アドレスを取得できない場合は、拡張機能をダウンロードしたり実行したりできません。 静的プライベート IP が必要な場合は、**Azure portal** または **PowerShell** を通じて静的プライベート IP を構成し、VM 内の DHCP オプションが有効になっていることを確認します。 PowerShell を使用した静的 IP アドレスの設定については、[こちら](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)をご覧ください。

### <a name="the-backup-extension-fails-to-update-or-load"></a>バックアップ拡張機能の更新または読み込みに失敗した
拡張機能を読み込むことができないと、スナップショットを作成できないため、バックアップに失敗します。

#### <a name="solution"></a>解決策

VMSnapshot 拡張機能に再読み込みを強制する 拡張機能をアンインストールします。 次回のバックアップ時に、拡張機能が再度読み込まれます。

拡張機能をアンインストールするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で、バックアップ エラーが発生している VM に移動します。
2. **[設定]** を選択します。
3. **[拡張機能]** を選択します。
4. **[Vmsnapshot 拡張機能]** を選択します。
5. **[アンインストール]** を選択します。

Linux VM で、VMSnapshot 拡張機能が Azure Portal に表示されない場合は、[Azure Linux エージェントを更新](../virtual-machines/linux/update-agent.md)してから、バックアップを実行してください。

この手順を済ませておくと、次回のバックアップ時に拡張機能が再インストールされます。

### <a name="remove_lock_from_the_recovery_point_resource_group"></a>復旧ポイントのリソース グループに対するロックを解除する
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[すべてのリソース] オプション**に移動して、AzureBackupRG_`<Geo>`_`<number>` という形式の復元ポイント コレクションのリソース グループを選択します。
3. **[設定]** セクションで **[ロック]** を選択して、ロックを表示します。
4. ロックを解除するには、省略記号を選択し、 **[削除]** をクリックします。

    ![ロックを解除する](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean_up_restore_point_collection"></a> 復元ポイント コレクションをクリーンアップする
ロックを解除した後で、復元ポイントをクリーンアップする必要があります。 復元ポイントをクリーンアップするには、次のいずれかの手順に従います。<br>
* [アドホック バックアップを実行して復元ポイント コレクションをクリーンアップする](#clean-up-restore-point-collection-by-running-ad-hoc-backup)<br>
* [Azure portal から復元ポイント コレクションをクリーンアップする](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-ad-hoc-backup"></a>アドホック バックアップを実行して復元ポイント コレクションをクリーンアップする
ロックを解除した後、アドホック/手動のバックアップをトリガーします。 これにより、復元ポイントが自動的にクリーンアップされます。 このアドホック/手動操作は、初回は失敗することを予期しておいてください。ただし、復元ポイントの手動削除の代わりに、自動クリーンアップが確実に行われます。 クリーンアップ後、次にスケジュールされているバックアップは成功するはずです。

> [!NOTE]
> 自動クリーンアップは、アドホック/手動バックアップをトリガーした数時間後に行われます。 スケジュールされたバックアップが引き続き失敗する場合は、[こちら](#clean-up-restore-point-collection-from-azure-portal)に記載されている手順を使用して、復元ポイント コレクションを手動で削除してみてください。

#### <a name="clean-up-restore-point-collection-from-azure-portal"></a>Azure portal から復元ポイント コレクションをクリーンアップする <br>

リソース グループのロックのために消去されない復元ポイント コレクションを手動で消去するには、次の手順を試行してください。
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[ハブ]** メニューの **[すべてのリソース]** をクリックし、VM が配置されている、AzureBackupRG_`<Geo>`_`<number>` という形式のリソース グループを選択します。

    ![ロックを解除する](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. リソース グループをクリックすると、 **[概要]** ブレードが表示されます。
4. **[非表示の型の表示]** オプションを選択して、非表示のすべてのリソースを表示します。 AzureBackupRG_`<VMName>`_`<number>` という形式の復元ポイント コレクションを選択します。

    ![ロックを解除する](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. **[削除]** をクリックして、復元ポイント コレクションを消去します。
6. バックアップ操作を再試行します。

> [!NOTE]
 >リソース (RP コレクション) に多数の復元ポイントがある場合、同じものをポータルから削除するとタイムアウトして失敗する可能性があります。 これは既知の CRP 問題であり、規定の時間内にすべての復元ポイントが削除されず、操作がタイムアウトします。ただし、削除操作は通常、2 ～ 3 回の再試行後に成功します。
