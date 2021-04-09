---
title: エージェントと拡張機能に関する問題のトラブルシューティング
description: エージェント、拡張機能、ディスクに関する Azure Backup のエラーの症状、原因、解決策。
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519607"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Azure Backup の失敗のトラブルシューティング:エージェント/拡張機能に関する問題

この記事では、VM エージェントと拡張機能との通信に関連する Azure Backup エラーの解決に役立つ可能性のあるトラブルシューティング手順について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>バックアップ エラーのトラブルシューティングを行うためのステップバイステップ ガイド

よくあるバックアップ エラーの多くは、次に示すトラブルシューティングの手順に従って自己解決できます。

### <a name="step-1-check-azure-vm-health"></a>手順 1:Azure VM の正常性を確認する

- **Azure VM のプロビジョニングの状態が '実行中' であることを確認します**:[VM のプロビジョニングの状態](../virtual-machines/states-billing.md)が **停止/割り当て解除済み/更新中** の状態にある場合、バックアップ操作が妨げられます。 *[Azure portal] > [VM] > [概要] >* の順に開き、VM の状態をチェックして **実行中** であることを確認してから、バックアップ操作を再試行してください。
- **保留中の OS の更新または再起動を確認します**:VM で保留中の OS 更新プログラムがないか、または再起動が保留されていないかを確認してください。

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>手順 2:Azure VM ゲスト エージェント サービスの正常性を確認する

- **Azure VM ゲスト エージェント サービスが開始されており、最新の状態であることを確認します**:
  - Windows VM の場合:
    - **services.msc** に移動し、**Windows Azure VM ゲスト エージェント サービス** が動作していることを確認します。 また、[最新バージョン](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)がインストールされていることを確認してください。 詳細については、[Windows VM ゲスト エージェントの問題](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)に関するページを参照してください。
    - Azure VM エージェントは、既定でポータル、PowerShell、コマンド ライン インターフェイス、または Azure Resource Manager テンプレートから Azure Marketplace イメージによってデプロイされた、すべての Windows VM にインストールされます。 Azure にデプロイするカスタム VM イメージを作成する場合は、[エージェントの手動インストール](../virtual-machines/extensions/agent-windows.md#manual-installation)が必要となる場合があります。
    - サポート マトリックスを確認して、VM が[サポートされている Windows オペレーティング システム](backup-support-matrix-iaas.md#operating-system-support-windows)で動作するかどうかを確認します。
  - Linux VM の場合は、
    - コマンド `ps-e` を実行して、Azure VM ゲスト エージェント サービスが実行されていることを確認します。 また、[最新バージョン](../virtual-machines/extensions/update-linux-agent.md)がインストールされていることを確認してください。 詳細については、[Linux VM ゲスト エージェントの問題](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)に関するページを参照してください。
    - [Linux VM エージェントのシステム パッケージの依存関係](../virtual-machines/extensions/agent-linux.md#requirements)が、サポートされている構成であることを確認します。 次に例を示します。サポートされている Python のバージョンは 2.6 以降です。
    - サポート マトリックスを確認し、VM が[サポートされている Linux オペレーティング システム](backup-support-matrix-iaas.md#operating-system-support-linux)で動作するかどうかを確認します。

### <a name="step-3-check-azure-vm-extension-health"></a>手順 3:Azure VM 拡張機能の正常性を確認する

- **すべての Azure VM 拡張機能が "プロビジョニング成功" の状態であることを確認します**:エラー状態の拡張機能がある場合、バックアップが影響を受ける可能性があります。
- *Azure portal を開いて [VM] > [設定] > [拡張機能] > [拡張機能の状態]* に移動し、すべての拡張機能が **[プロビジョニング成功]** の状態になっていることを確認します。
- すべての[拡張機能の問題](../virtual-machines/extensions/overview.md#troubleshoot-extensions)が解決されていることを確認して、バックアップ操作をやり直してください。
- **COM+ System Application** が動作していることを確認します。 また、**分散トランザクション コーディネーター サービス** も **ネットワーク サービス アカウント** として実行されている必要があります。 [COM+ と MSDTC の問題のトラブルシューティング](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)に関する記事の手順に従います。

### <a name="step-4-check-azure-backup-vm-extension-health"></a>手順 4:Azure Backup VM 拡張機能の正常性を確認する

Azure Backup では、VM スナップショット拡張機能を使用して Azure 仮想マシンのアプリケーション整合性バックアップを作成します。 Azure Backup によって、バックアップの有効化の後にトリガーされる最初のスケジュールされたバックアップの一部として、拡張機能がインストールされます。

- **VMSnapshot 拡張機能がエラー状態でないことを確認します**:この [セクション](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state)に記載されている手順に従って、Azure Backup 拡張機能が正常であることを検証して確認します。

- **ウイルス対策によって拡張機能がブロックされているかどうかを確認します**:特定のウイルス対策ソフトウェアによって、拡張機能の実行が妨げられる場合があります。
  
  バックアップ エラーが発生した際は、"**イベント ビューアーのアプリケーション ログ**" に、"_障害が発生しているアプリケーション名: IaaSBcdrExtension.exe_" と記載されたログ エントリがあるかどうかを確認します。 エントリが見つかった場合、VM で構成されているウイルス対策によってバックアップ拡張機能の実行が制限されている可能性があります。 ウイルス対策構成で次のディレクトリを除外してテストを行い、バックアップ操作を再試行してください。
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **ネットワーク アクセスが必要かどうかを確認します**:拡張機能パッケージは、Azure Storage 拡張機能リポジトリからダウンロードされ、拡張機能ステータスのアップロードが Azure Storage に転記されます。 [詳細については、こちらを参照してください](../virtual-machines/extensions/features-windows.md#network-access)。
  - サポートされていないバージョンのエージェントを使用する場合は、そのリージョン内の Azure Storage への VM からの送信アクセスを許可する必要があります。
  - ゲスト ファイアウォールまたはプロキシを使用して `168.63.129.16` へのアクセスをブロックしている場合は、上記とは関係なく拡張機能はエラーになります。 ポート 80、443、および 32526 が必要です。[詳細はこちらを参照してください](../virtual-machines/extensions/features-windows.md#network-access)。

- **ゲスト VM 内で DHCP が有効になっていることを確認します**:これは、IaaS VM のバックアップを機能させるために、DHCP からホストまたはファブリック アドレスを取得するのに必要です。 静的プライベート IP が必要な場合は、Azure portal または PowerShell を通じて静的プライベート IP を構成し、VM 内の DHCP オプションが有効になっていることを確認します。[詳細はこちらを参照してください](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)。

- **VSS ライター サービスが実行されていることを確認します**:VSS ライターの問題のトラブルシューティング[の手順に従います。
- **バックアップのベスト プラクティス ガイドラインに従います**:[Azure VM のバックアップを有効にするベスト プラクティス](backup-azure-vms-introduction.md#best-practices)を確認します。
- **暗号化されたディスクのガイドラインを確認します**:暗号化されたディスクを使用している VM のバックアップを有効にする場合は、必要なアクセス許可をすべて付与していることを確認してください。 詳細については、「[暗号化された Azure VM をバックアップおよび復元する](backup-azure-vms-encryption.md)」を参照してください。

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM agent unable to communicate with Azure Backup (VM エージェントが Azure Backup と通信できません)

**エラー コード**:UserErrorGuestAgentStatusUnavailable <br>
**エラー メッセージ**:VM エージェントが Azure Backup と通信できない<br>

Azure VM エージェントが停止しているか、古くなっているか、一貫性のない状態になっているか、またはインストールされていない可能性があります。 これらの状態によって、Azure Backup サービスではスナップショットをトリガーできなくなっています。

- **Azure portal から [VM] > [設定] > [プロパティ] ペインを開いて**、VM の **[状態]** が **[実行中]** であること、また、 **[エージェントの状態]** が **[準備完了]** になっていることを確認します。 VM エージェントが停止しているか、不整合な状態になっている場合は、エージェントを再起動する<br>
  - Windows VM の場合は、次の[手順](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)を実行して、ゲスト エージェントを再起動します。<br>
  - Linux VM の場合は、次の[手順](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)を実行して、ゲスト エージェントを再起動します。
- **Azure portal を開いて [VM] > [設定] > [拡張機能]** に移動し、すべての拡張機能が **[プロビジョニング成功]** 状態になっていることを確認します。 そうでない場合は、こちらの[手順](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state)に従って問題を解決してください。

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Could not communicate with the VM agent for snapshot status (スナップショットの状態について VM エージェントと通信できませんでした)

**エラー コード**:GuestAgentSnapshotTaskStatusError<br>
**エラー メッセージ**:スナップショットの状態について VM エージェントと通信できませんでした <br>

Azure Backup サービスに VM を登録してスケジュール設定すると、Backup では、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  

**原因 1:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**原因 3:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**

**原因 4:[VM エージェント構成オプションが設定されていない (Linux VM の場合)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**原因 5:[アプリケーション制御ソリューションが IaaSBcdrExtension.exe をブロックしている](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed は、プロビジョニングに失敗した状態

**エラー コード**:UserErrorVmProvisioningStateFailed<br>
**エラー メッセージ**:VM がプロビジョニングに失敗した状態<br>

このエラーは、拡張機能の１つが失敗して、VM がプロビジョニング失敗状態になる場合に発生します。<br>**Azure portal を開いて [VM] > [設定] > [拡張機能] > [拡張機能の状態]** に移動し、すべての拡張機能が **[プロビジョニング成功]** の状態になっていることを確認します。 詳細については、「[プロビジョニング状態](../virtual-machines/states-billing.md)」を参照してください。

- エラー状態の拡張機能がある場合、バックアップが影響を受ける可能性があります。 これらの拡張機能の問題が解決されていることを確認して、バックアップ操作をやり直してください。
- VM のプロビジョニング状態が更新中の状態になっている場合は、バックアップが妨げられる可能性があります。 正常であることを確認してから、バックアップ操作を再試行してください。

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - The Restore Point collection max limit has reached (復元ポイント コレクションの上限に達しました)

**エラー コード**:UserErrorRpCollectionLimitReached <br>
**エラー メッセージ**:復元ポイント コレクションの上限に達しました。 <br>

- この問題は、復旧ポイントの自動クリーンアップを妨げる、復旧ポイント リソース グループに対するロックが存在する場合に、発生することがあります。
- この問題は、1 日に複数のバックアップがトリガーされる場合にも発生することがあります。 現時点では、インスタント復元ポイントは設定されているスナップショット保有期間に基づき 1 日から 5 日間保持され、任意の時点で VM に関連付けることができるインスタント RP は 18 個だけであるため、1 日に 1 回だけのバックアップをお勧めします。 <br>
- VM の復元ポイント コレクションとリソース グループ全体の復元ポイントの数は、18 個以下にする必要があります。 新しい復元ポイントを作成するには、既存の復元ポイントを削除します。

推奨される操作:<br>
この問題を解決するには、VM のリソース グループに対するロックを解除して、クリーンアップをトリガーする操作を再試行します。
> [!NOTE]
> Backup サービスでは、復元ポイント コレクションを格納する VM のリソース グループとは別のリソース グループが作成されます。 Backup サービスに使用するために作成されたリソース グループは、ロックしないことをお勧めします。 Backup サービスによって作成されるリソース グループの名前付け形式は次のとおりです。AzureBackupRG_`<Geo>`_`<number>`。 次に例を示します。*AzureBackupRG_northeurope_1*

**ステップ 1:[復元ポイントのリソース グループのロックを解除する](#remove_lock_from_the_recovery_point_resource_group)** <br>
**手順 2:[復元ポイント コレクションをクリーンアップする](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured - Backup のキー コンテナーに対するアクセス許可は、暗号化された VM をバックアップするには十分ではありません

**エラー コード**:UserErrorKeyvaultPermissionsNotConfigured <br>
**エラー メッセージ**:Backup のキー コンテナーに対するアクセス許可は、暗号化された VM をバックアップするのに十分ではありません。 <br>

暗号化された VM のバックアップ操作を正常に完了するには、キー コンテナーへのアクセス許可が必要です。 アクセス許可は、[Azure portal](./backup-azure-vms-encryption.md) または [PowerShell](./backup-azure-vms-automation.md#enable-protection) を使用して設定できます。

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Snapshot operation failed due to no network connectivity on the virtual machine (仮想マシンがネットワークに接続していないためにスナップショット操作が失敗しました)

**エラー コード**:ExtensionSnapshotFailedNoNetwork<br>
**エラー メッセージ**:仮想マシンがネットワークに接続していないためにスナップショット操作が失敗した<br>

Azure Backup サービスに VM を登録してスケジュール設定すると、Backup では、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を完了してから、操作を再試行してください。

**[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot の拡張操作に失敗しました

**エラー コード**:ExtensionOperationFailedForManagedDisks <br>
**エラー メッセージ**:VMSnapshot 拡張機能の操作に失敗した<br>

Azure Backup サービスに VM を登録してスケジュール設定すると、Backup では、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 2:[エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 3:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 - Backup fails, with an internal error (内部エラーでバックアップに失敗しました)

**エラー コード**:BackUpOperationFailed / BackUpOperationFailedV2 <br>
**エラー メッセージ**:バックアップ操作は内部エラーのため失敗しました - 数分以内に操作をやり直してください <br>

Azure Backup サービスに VM を登録して、スケジュール設定すると、Backup サービスは、VM のバックアップ拡張機能と通信してジョブを開始し、ポイントインタイム スナップショットを作成します。 以下のいずれかの状況によって、スナップショットをトリガーできない場合があります。 スナップショットがトリガーされなかった場合、バックアップ エラーが発生する可能性があります。 次のトラブルシューティング手順を上から順に実行した後で、必要な操作を再試行してください。  
**原因 1:[ エージェントが VM にインストールされているが応答しない (Windows VM の場合)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**原因 2:[VM にインストールされているエージェントが古くなっている (Linux VM の場合)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**原因 3:[スナップショットの状態を取得できないか、スナップショットを作成できない](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  
**原因 4:[リソース グループのロックが原因で、バックアップ サービスに古い復元ポイントを削除するためのアクセス許可がない](#remove_lock_from_the_recovery_point_resource_group)**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize - The configured disk size(s) is currently not supported by Azure Backup (構成されたディスク サイズは、Azure Backup では現在サポートされていません)

**エラー コード**:UserErrorUnsupportedDiskSize <br>
**エラー メッセージ**:The configured disk size(s) is currently not supported by Azure Backup. (構成されたディスク サイズは、現在、Azure Backup ではサポートされていません。) <br>

VM をバックアップするときにディスク サイズが 32 TB よりも大きいと、バックアップ操作が失敗することがあります。 また、4 TB を超える暗号化されたディスクのバックアップは現在サポートされていません。 ディスクを分割して、ディスクのサイズが、サポートされている制限以下になるようにしてください。

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>UserErrorBackupOperationInProgress - 別のバックアップ操作が進行中であるためバックアップを開始できません

**エラー コード**:UserErrorBackupOperationInProgress <br>
**エラー メッセージ**:別のバックアップ操作が進行中であるためバックアップを開始できません<br>

進行中の既存のバックアップ ジョブがあるため、最新のバックアップ ジョブが失敗しました。 新しいバックアップ ジョブは、現在のジョブが完了するまで開始できません。 他のバックアップ操作をトリガーまたはスケジュールする前に、進行中のバックアップ操作が完了していることを確認します。 バックアップ ジョブの状態を確認するには、次の手順を実行します。

1. Azure portal にサインインして、 **[すべてのサービス]** を選択します。 「Recovery Services」と入力し、 **[Recovery Services コンテナー]** を選択します。 Recovery Services コンテナーの一覧が表示されます。
2. Recovery Services コンテナーの一覧から、バックアップの構成先のコンテナーを選択します。
3. コンテナーのダッシュボード メニューの **[バックアップ ジョブ]** を選択すると、すべてのバックアップ ジョブが表示されます。
   - バックアップ ジョブが進行中の場合は、そのジョブが完了するまで待機する、そのバックアップ ジョブを取り消します。
     - バックアップ ジョブを取り消すには、そのバックアップ ジョブを右クリックして **[キャンセル]** を選択するか、[PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) を使用します。
   - 別のコンテナー内でバックアップを再構成した場合は、古いコンテナー内で実行されているバックアップ ジョブがないことを確認します。 存在する場合は、バックアップ ジョブを取り消します。
     - バックアップ ジョブを取り消すには、そのバックアップ ジョブを右クリックして **[キャンセル]** を選択するか、[PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) を使用します。
4. バックアップ操作を再試行してください。

スケジュールしたバックアップ操作に長い時間がかかり、次のバックアップの構成と競合している場合は、[ベスト プラクティス](backup-azure-vms-introduction.md#best-practices)、[バックアップ パフォーマンス](backup-azure-vms-introduction.md#backup-performance)、および[復元に関する考慮事項](backup-azure-vms-introduction.md#backup-and-restore-considerations)について確認してください。

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - エラーが発生したため、バックアップに失敗しました。 詳細については、ジョブ エラー メッセージの詳細をご覧ください。

**エラー コード**:UserErrorCrpReportedUserError <br>
**エラー メッセージ**:エラーが発生したため、バックアップに失敗しました。 詳細については、ジョブ エラー メッセージの詳細をご覧ください。

このエラーは、IaaS VM から報告されます。 問題の根本原因を特定するには、Recovery Services コンテナーの設定にアクセスします。 **[監視]** セクション下で、 **[バックアップ ジョブ]** を選択して、状態をフィルター処理して表示します。 **[失敗]** を選択して、基になるエラー メッセージの詳細を確認します。 エラーの詳細ページの推奨事項に従って、さらにアクションを実行します。

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>UserErrorBcmDatasourceNotPresent - バックアップ失敗: この仮想マシンは、Azure Backup によって (アクティブに) 保護されていません

**エラー コード**:UserErrorBcmDatasourceNotPresent <br>
**エラー メッセージ**:バックアップ失敗: この仮想マシンは、Azure Backup によって (アクティブに) 保護されていません。

指定された仮想マシンが Azure Backup によってアクティブに保護されている (一時停止状態でない) ことを確認します。 この問題を解決するには、仮想マシンがアクティブであることを確実にしてから、操作を再試行します。

## <a name="causes-and-solutions"></a>原因とソリューション

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>エージェントが VM にインストールされているが応答しない (Windows VM の場合)

#### <a name="solution-for-this-error"></a>このエラーの解決策

VM エージェントが破損しているまたはサービスが停止している可能性があります。 VM エージェントを再インストールすることで最新バージョンを入手できます。 その際に、サービスとの通信も再開されます。

1. VM サービス (services.msc) で Windows Azure ゲスト エージェント サービスが実行されているかどうかを確認します。 Windows Azure ゲスト エージェント サービスの再起動とバックアップの開始を試みます。
2. コントロール パネルの [サービス] に Windows Azure ゲスト エージェント サービスが表示されない場合は、 **[プログラムと機能]** に移動し、Windows Azure ゲスト エージェント サービスがインストールされているかどうかを確認してください。
3. Windows Azure ゲスト エージェント サービスが **[プログラムと機能]** に表示される場合は、Windows Azure ゲスト エージェントをアンインストールします。
4. [最新バージョンのエージェント MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) をダウンロードしてインストールします。 インストールを実行するには、管理者権限が必要です。
5. [サービス] に Windows Azure ゲスト エージェント サービスが表示されることを確認します。
6. オンデマンド バックアップを実行します。
   - ポータルの **[今すぐバックアップ]** を選択します。

さらに、VM に [Microsoft .NET 4.5 がインストールされていること](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)を確認します。 VM エージェントがサービスと通信するためには .NET 4.5 が必要です。

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM にインストールされているエージェントが古くなっている (Linux VM の場合)

#### <a name="solution"></a>解決策

Linux VM の場合、エージェントに関連するエラーまたは拡張機能に関連するエラーのほとんどは、古い VM エージェントに影響する問題が原因で発生します。 この問題のトラブルシューティングを行うには、次の一般的なガイドラインに従います。

1. [Linux VM エージェントを更新](../virtual-machines/extensions/update-linux-agent.md)する手順に従います。

   > [!NOTE]
   > ディストリビューション リポジトリを通してのみエージェントを更新することを "*強くお勧め*" します。 エージェント コードを GitHub から直接ダウンロードし、それを更新することは、推奨されません。 最新のエージェントをディストリビューションで使用できない場合は、そのエージェントをインストールする方法をディストリビューション サポートにお問い合わせください。 最新のエージェントを確認するには、GitHub リポジトリの [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)のページをご覧ください。

2. `ps -e` コマンドを実行して、Azure エージェントが VM で実行されていることを確認します。

   このプロセスが実行されていない場合は、次のコマンドを使用してプロセスを再起動します。

   - Ubuntu の場合: `service walinuxagent start`
   - その他のディストリビューションの場合: `service waagent start`

3. [エージェントの自動再起動を構成します](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash)。
4. 新しいテスト バックアップを実行します。 エラーが解決しない場合は、VM から次のログを収集してください。

   - /var/lib/waagent/*.xml
   - /var/log/waagent.log
   - /var/log/azure/*

waagent の詳細ログが必要な場合は、次の手順に従います。

1. /etc/waagent.conf ファイルで、次の行を見つけます:**Enable verbose logging (y|n)**
2. **Logs.Verbose** の値を *n* から *y* に変更します。
3. 変更を保存した後、このセクションで前述した手順を実行して waagent を再起動します。

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM エージェント構成オプションが設定されていない (Linux VM の場合)

構成ファイル (/etc/waagent.conf) を使用して waagent の動作を制御します。 Backup が動作するためには、構成ファイルのオプション **Extensions.Enable** を **y** に設定し、**Provisioning.Agent** を **auto** に設定する必要があります。
VM エージェント構成ファイルのオプションの完全な一覧については、<https://github.com/Azure/WALinuxAgent#configuration-file-options> を参照してください

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>アプリケーション制御ソリューションが IaaSBcdrExtension.exe をブロックしている

[AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (または別のアプリケーション制御ソリューション) を実行しており、規則が発行元またはパス ベースの場合、**IaaSBcdrExtension.exe** 実行可能ファイルの実行がブロックされる可能性があります。

#### <a name="solution-to-this-issue"></a>この問題の解決策

AppLocker (またはその他のアプリケーション制御ソフトウェア) から、`/var/lib` パスまたは **IaaSBcdrExtension.exe** 実行可能ファイルを除外します。

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>スナップショットの状態を取得できないか、スナップショットを作成できない

VM のバックアップは、基礎となるストレージ アカウントへのスナップショット コマンドの発行に依存します。 ストレージ アカウントにアクセスできないか、スナップショット タスクの実行が遅れているために、バックアップが失敗することがあります。

#### <a name="solution-for-this-issue"></a>この問題の解決策

次の場合にスナップショットのタスクが失敗することがあります。

| 原因 | 解決策 |
| --- | --- |
| VM が リモート デスクトップ プロトコル (RDP) でシャットダウンされているため、VM の状態が正しく報告されない。 | RDP で VM をシャットダウンした場合は、ポータルでその VM の状態が正しいかどうかを確認します。 正しくない場合は、VM のダッシュボードにある **[シャットダウン]** オプションを使用して、ポータル上で VM をシャットダウンします。 |
| VM が DHCP からホスト/ファブリック アドレスを取得できない。 | IaaS VM バックアップが正しく機能するには、ゲスト内で DHCP が有効になっている必要があります。 VM が DHCP 応答 245 からホスト/ファブリック アドレスを取得できない場合は、拡張機能をダウンロードしたり実行したりできません。 静的プライベート IP が必要な場合は、**Azure portal** または **PowerShell** を通じて静的プライベート IP を構成し、VM 内の DHCP オプションが有効になっていることを確認します。 PowerShell を使用した静的 IP アドレスの設定については、[こちら](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)をご覧ください。

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>復旧ポイントのリソース グループに対するロックを解除する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのリソース] オプション** に移動して、AzureBackupRG_`<Geo>`_`<number>` という形式の復元ポイント コレクションのリソース グループを選択します。
3. **[設定]** セクションで **[ロック]** を選択して、ロックを表示します。
4. ロックを解除するには、省略記号を選択し、 **[削除]** を選択します。

    ![ロックを解除する](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> 復元ポイント コレクションをクリーンアップする

ロックを解除した後で、復元ポイントをクリーンアップする必要があります。

VM のリソース グループまたは VM 自体を削除した場合、マネージド ディスクのインスタント復元スナップショットはアクティブなままで、リテンション期間の設定に従って有効期限が切れます。 復元ポイント コレクションに格納されているインスタント復元スナップショットを削除するには (もう不要になった場合)、以下の手順に従って復元ポイント コレクションをクリーンアップします。

復元ポイントをクリーンアップするには、次のいずれかの手順に従います。<br>

- [オンデマンド バックアップを実行して復元ポイント コレクションをクリーンアップする](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Azure portal から復元ポイント コレクションをクリーンアップする](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>オンデマンド バックアップを実行して復元ポイント コレクションをクリーンアップする

ロックを解除した後、オンデマンド バックアップをトリガーします。 このアクションによって、復元ポイントが自動的にクリーンアップされます。 このオンデマンド操作は、初回は失敗することを予期しておいてください。 ただし、復元ポイントの手動削除の代わりに、自動クリーンアップが確実に行われます。 クリーンアップ後、次にスケジュールされているバックアップは成功するはずです。

> [!NOTE]
> 自動クリーンアップは、オンデマンド バックアップをトリガーした数時間後に行われます。 スケジュールされたバックアップが引き続き失敗する場合は、[こちら](#clean-up-restore-point-collection-from-azure-portal)に記載されている手順を使用して、復元ポイント コレクションを手動で削除してみてください。

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Azure portal から復元ポイント コレクションをクリーンアップする <br>

リソース グループのロックが原因で消去されない復元ポイント コレクションを手動で消去するには、次の手順を試行します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[ハブ]** メニューの **[すべてのリソース]** を選択し、VM が配置されている、AzureBackupRG_`<Geo>`_`<number>` という形式のリソース グループを選択します。

    ![リソース グループの選択](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. リソース グループを選択すると、 **[概要]** ウィンドウが表示されます。
4. **[非表示の型の表示]** オプションを選択して、非表示のすべてのリソースを表示します。 AzureBackupRG_`<VMName>`_`<number>` という形式の復元ポイント コレクションを選択します。

    ![復元ポイント コレクションの選択](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. **[削除]** を選択して、復元ポイント コレクションを消去します。
6. バックアップ操作を再試行します。

> [!NOTE]
 >リソース (RP コレクション) に多数の復元ポイントがある場合、ポータルからこれらを削除しようとするとタイムアウトして失敗する可能性があります。 これは既知の CRP 問題であり、規定の時間内にすべての復元ポイントが削除されず、操作がタイムアウトします。ただし、削除操作は通常、再試行を 2 回から 3 回行った後に成功します。
