---
title: Azure VM のバックアップ エラーのトラブルシューティング
description: この記事では、Azure 仮想マシンのバックアップと復元で発生したエラーをトラブルシューティングする方法について説明します。
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 2cda13ea089ac08dff7c1ba5ca93ba56ab3c23cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831552"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Azure 仮想マシンでのバックアップ エラーのトラブルシューティング

次の情報を使って、Azure Backup の使用中に発生したエラーのトラブルシューティングを行うことができます。

## <a name="backup"></a>バックアップ

このセクションでは、Azure 仮想マシンのバックアップ操作のエラーについて説明します。

### <a name="basic-troubleshooting"></a>基本的なトラブルシューティング

* VM エージェント (WA エージェント) が[最新バージョン](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)であることを確認します。
* Windows または Linux の VM OS バージョンがサポートされていることを確認します。「[Azure VM バックアップのサポート マトリックス](./backup-support-matrix-iaas.md)」を参照してください。
* 別のバックアップ サービスが実行されていないことを確認します。
  * スナップショット拡張機能の問題が確実に起こらないようにするには、[拡張機能をアンインストールして強制的に再読み込みしてから、バックアップを再試行してください](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)。
* VM がインターネットに接続されていることを確認します。
  * 別のバックアップ サービスが実行されていないことを確認します。
* `Services.msc` から、**Windows Azure ゲスト エージェント** サービスが **実行中** であることを確認します。 **Windows Azure ゲスト エージェント** サービスが見つからない場合は、[Recovery Services コンテナーの Azure VM をバックアップする](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)方法の記事を参照してインストールします。
* **[イベント ログ]** には、他のバックアップ製品 (Windows Server バックアップなど) からのものであり、Azure Backup が原因ではないバックアップの失敗が表示される場合があります。 次の手順を使用して、Azure Backup に関する問題かどうかを確認します。
  * イベント ソースまたはメッセージで **[バックアップ]** エントリのエラーが発生した場合は、Azure IaaS VM のバックアップによるバックアップが成功したかどうか、および復元ポイントが目的のスナップショットの種類で作成されたかどうかを確認します。
  * Azure Backup が機能している場合は、別のバックアップ ソリューションの問題である可能性があります。
  * Azure Backup は正常に動作したが、"Windows Server バックアップ" が失敗したイベント ビューアー エラー 517 の例を次に示します。![Windows Server バックアップの失敗](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Azure Backup が失敗した場合は、この記事の「一般的な VM バックアップのエラー」セクションの対応するエラー コードを確認してください。

## <a name="common-issues"></a>一般的な問題

Azure 仮想マシンでのバックアップ エラーに関する一般的な問題を次に示します。

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError - VM で構成されているウイルス対策により、バックアップ拡張機能の実行が制限されています

エラー コード:VMRestorePointInternalError

バックアップ時に、**イベント ビューアーのアプリケーション ログ** に、メッセージ "**障害が発生しているアプリケーション名: IaaSBcdrExtension.exe**" が表示される場合は、VM で構成されているウイルス対策によってバックアップ拡張機能の実行が制限されていることが確認されています。
この問題を解決するには、ウイルス対策の構成で次のディレクトリを除外し、バックアップ操作を再試行します。

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - コンテナーからのバックアップされたデータのコピーがタイムアウトしました

エラー コード:CopyingVHDsFromBackUpVaultTakingLongTime <br/>
エラー メッセージ:コンテナーからのバックアップされたデータのコピーがタイムアウトしました

これは、バックアップ サービスがタイムアウト期間内にデータをコンテナーに転送するときの一時的なストレージ エラーまたはストレージ アカウント IOPS の不足のために発生することがあります。 これらの[ベスト プラクティス](backup-azure-vms-introduction.md#best-practices)を使用して VM バックアップを構成し、バックアップ操作を再試行します。

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM はバックアップが可能な状態にありません

エラー コード:UserErrorVmNotInDesirableState <br/>
エラー メッセージ:VM はバックアップできる状態ではありません。<br/>

VM が [失敗] 状態にあるため、バックアップ操作に失敗しました。 バックアップに成功するには、VM の状態が [実行中]、[停止済み]、または [停止済み (割り当て解除)] である必要があります。

* VM が **実行** と **シャットダウン** の間の一時的な状態である場合は、状態が変わるのを待ちます。 その後、バックアップ ジョブをトリガーします。
* VM が Linux VM で、Security-Enhanced Linux カーネル モジュールが使用されている場合は、Azure Linux エージェントのパス **/var/lib/waagent** をセキュリティ ポリシーから除外して、Backup 拡張機能が確実にインストールされるようにします。

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - ファイル システム整合性スナップショットの取得で VM の 1 つ以上のマウント ポイントの凍結に失敗しました

エラー コード:UserErrorFsFreezeFailed <br/>
エラー メッセージ:ファイル システム整合性スナップショットの取得で VM の 1 つ以上のマウント ポイントの凍結に失敗しました。

* **umount** コマンドを使用して、ファイル システム状態がクリーニングされなかったデバイスをマウント解除します。
* **fsck** コマンドを使用して、これらのデバイスに対してファイル システム整合性チェックを実行します。
* これらのデバイスを再度マウントし、バックアップ操作を再試行します。</ol>

デバイスのマウントを解除できない場合は、VM のバックアップ構成を更新して、特定のマウント ポイントを無視することができます。 たとえば、'/mnt/resource' マウント ポイントのマウントを解除できず、VM バックアップが失敗する場合は、次のように ```MountsToSkip``` プロパティを使用して VM バックアップ構成ファイルを更新できます。

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```


### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC - COM+ エラーのために拡張機能のインストール/操作に失敗しました

エラー コード:ExtensionSnapshotFailedCOM <br/>
エラー メッセージ:COM+ エラーが発生したため、スナップショット操作に失敗しました

エラー コード:ExtensionInstallationFailedCOM  <br/>
エラー メッセージ:COM+ エラーのために拡張機能のインストール/操作に失敗しました

エラー コード:ExtensionInstallationFailedMDTC <br/>
エラー メッセージ:"COM+: Microsoft 分散トランザクション コーディネーターと通信できませんでした" というエラーで拡張機能のインストールが失敗しました。 <br/>

Windows サービス **COM+ System** Application での問題のためにバックアップ操作に失敗しました。  この問題を解決するには、次の手順に従ってください。

* Windows サービス **COM+ System Application** の起動/再起動を試みます (管理者特権でのコマンド プロンプトから **- net start COMSysApp**)。
* **分散トランザクション コーディネーター** サービスが **ネットワーク サービス** アカウントとして実行されていることを確認します。 そうでない場合は、**ネットワーク サービス** アカウントとして実行されるように変更し、**COM+ System Application** を再起動します。
* このサービスを再起動できない場合は、次の手順に従って **分散トランザクション コーディネーター** サービスを再インストールします。
  * MSDTC サービスを停止します
  * コマンド プロンプト (cmd) を開きます
  * `msdtc -uninstall` コマンドを実行します。
  * `msdtc -install` コマンドを実行します。
  * MSDTC サービスを起動します
* Windows サービス **COM+ システム アプリケーション** を開始します。 **COM+ システム アプリケーション** が開始したら、Azure portal からバックアップ ジョブをトリガーします。</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - VSS ライターが正しくない状態にあるため、スナップショット操作に失敗しました

エラー コード:ExtensionFailedVssWriterInBadState <br/>
エラー メッセージ:VSS ライターが正しくない状態にあるため、スナップショット操作に失敗しました。

このエラーは、VSS ライターが正しくない状態にあったために発生します。 Azure Backup 拡張機能は、VSS ライターと連携してディスクのスナップショットを作成します。 この問題を解決するには、次の手順に従ってください。

手順 1:状態が正しくない VSS ライターを再起動します。

* 管理者特権でのコマンド プロンプトから、```vssadmin list writers``` を実行します。
* 出力には、すべての VSS ライターとそれらの状態が含まれています。 状態が **[[1] 安定]** ではない VSS ライターごとに、対応する VSS ライターのサービスを再起動します。
* サービスを再起動するには、管理者特権でのコマンド プロンプトから次のコマンドを実行します。

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> サービスによっては、再起動すると運用環境に影響を与えるものがあります。 承認プロセスが先に実行され、スケジュールされたダウンタイムにサービスが再起動されることを確認してください。

手順 2:VSS ライターを再起動しても問題が解決しなかった場合は、管理者特権でのコマンド プロンプトから (管理者として) 次のコマンドを実行して、BLOB スナップショットではスレッドが作成されないようにします。

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

手順 3:手順 1 と 2 で問題が解決されなかった場合、IOPS が制限されているために VSS ライターがタイムアウトしたことが原因である可能性があります。<br>

確認するには、"***システムおよびイベント ビューアーのアプリケーション ログ***" に移動し、次のエラー メッセージがあるかどうかを確認します。<br>
*The shadow copy provider timed out while holding writes to the volume being shadow copied. (シャドウ コピーされるボリュームへの書き込みを保持している間に、シャドウ コピー プロバイダーがタイムアウトしました。)This is probably due to excessive activity on the volume by an application or a system service. (これは、アプリケーションまたはシステム サービスによるボリュームでの過剰なアクティビティが原因である可能性があります。)Try again later when activity on the volume is reduced. (ボリュームのアクティビティが減ったら、後でもう一度お試しください。)*<br>

解決方法:

* VM ディスク全体に負荷を分散させることができるかどうかを確認します。 これにより、1 つのディスクの負荷が軽減されます。 [ストレージ レベルで診断メトリックを有効にすることによって、IOPS 調整を確認](../virtual-machines/troubleshooting/performance-diagnostics.md#install-and-run-performance-diagnostics-on-your-vm)できます。
* バックアップ ポリシーを変更して、VM の負荷が最も低いオフピーク時間帯にバックアップを実行します。
* より高い IOPS をサポートするように Azure ディスクをアップグレードします。 [詳しくはこちらをご覧ください](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState - VSS (ボリューム シャドウ コピー) サービスが正しくない状態にあるため、スナップショット操作に失敗しました

エラー コード:ExtensionFailedVssServiceInBadState <br/>
エラー メッセージ:VSS (ボリューム シャドウ コピー) サービスが正しくない状態にあるため、スナップショット操作に失敗しました。

このエラーは、VSS サービスが正しくない状態にあったために発生します。 Azure Backup 拡張機能は、VSS サービスと連携してディスクのスナップショットを作成します。 この問題を解決するには、次の手順に従ってください。

VSS (ボリューム シャドウ コピー) サービスを再起動します。

* Services.msc に移動し、'ボリューム シャドウ コピー サービス' を再起動します。<br>
(または)<br>
* 管理者特権のコマンド プロンプトで、次のコマンドを実行します。

 ```net stop VSS``` <br>
 ```net start VSS```

問題が引き続き発生する場合は、スケジュールされたダウンタイムで VM を再起動します。

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable - 選択された VM サイズを使用できないため、VM の作成に失敗しました

エラー コード:UserErrorSkuNotAvailable エラー メッセージ: 選択した VM サイズが使用できないため、VM の作成に失敗しました。

このエラーは、復元操作中に選択された VM サイズがサポートされていないサイズであるために発生します。 <br>

この問題を解決するには、復元操作中に [[ディスクの復元]](./backup-azure-arm-restore-vms.md#restore-disks) オプションを使用します。 これらのディスクは、[PowerShell コマンドレット](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)を使用して、[使用可能なサポートされている VM サイズ](./backup-support-matrix-iaas.md#vm-compute-support)の一覧から VM を作成するために使用します。

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported - Marketplace の購入要求が存在しないため、VM の作成に失敗しました

エラー コード:UserErrorMarketPlaceVMNotSupported エラー メッセージ: Marketplace の購入要求が存在しないため、VM の作成に失敗しました。

Azure Backup は、Azure Marketplace で入手できる VM のバックアップと復元をサポートしています。 このエラーは、Azure Marketplace で入手できなくなった (特定のプラン/発行元が設定された) VM を復元しようとしたときに発生します。詳細については、[ここ](/legal/marketplace/participation-policy#offering-suspension-and-removal)を参照してください。

* この問題を解決するには、復元操作中に [[ディスクの復元]](./backup-azure-arm-restore-vms.md#restore-disks) オプションを使用した後、[PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) または [Azure CLI](./tutorial-restore-disk.md) コマンドレットを使用して、その VM に対応する最新のマーケットプレース情報で VM を作成します。
* 発行元にマーケットプレース情報がない場合は、データ ディスクを使用してデータを取得し、それを既存の VM にアタッチできます。

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - バックアップ拡張機能の構成の解析に失敗しました

エラー コード:ExtensionConfigParsingFailure<br/>
エラー メッセージ:バックアップ拡張機能の構成の解析に失敗しました。

このエラーは、次の **MachineKeys** ディレクトリでアクセス許可が変更されたために発生します。 **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。
次のコマンドを実行し、**MachineKeys** ディレクトリのアクセス許可が既定のものであることを確認してください: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys`。

既定のアクセス許可は、次のとおりです。

* Everyone:(R,W)
* BUILTIN\Administrators:(F)

**MachineKeys** ディレクトリで既定以外のアクセス許可が表示される場合は、以下の手順に従って、アクセス許可の修正、証明書の削除、バックアップのトリガーを行ってください。

1. **MachineKeys** ディレクトリのアクセス許可を修正します。 ディレクトリで Explorer のセキュリティ プロパティやセキュリティの詳細設定を使用して、アクセス許可を既定値にリセットします。 ディレクトリから (既定値以外の) ユーザー オブジェクトをすべて削除し、**Everyone** アクセス許可に次のような特殊なアクセス許可があることを確認します。

   * フォルダーの一覧/データの読み取り
   * 属性の読み取り
   * 拡張属性の読み取り
   * ファイルの作成/データの書き込み
   * フォルダーの作成/データの追加
   * 属性の書き込み
   * 拡張属性の書き込み
   * 読み取りアクセス許可
2. **発行先** がクラシック デプロイ モデルまたは **Windows Azure CRP Certificate Generator** であるすべての証明書を削除します。

   * [ローカル コンピューターのコンソールで証明書を開きます](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)。
   * **[個人]**  >  **[証明書]** で、**発行先** がクラシック デプロイ モデルまたは **Windows Azure CRP Certificate Generator** であるすべての証明書を削除します。
3. VM バックアップ ジョブをトリガーします。

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - 拡張機能の状態がバックアップ操作に対応していません

エラー コード:ExtensionStuckInDeletionState <br/>
エラー メッセージ:拡張機能の状態がバックアップ操作に対応していません

バックアップ拡張機能の整合性のない状態のためにバックアップ操作に失敗しました。 この問題を解決するには、次の手順に従ってください。

* ゲスト エージェントがインストールされ、応答していることを確認します
* Azure portal から、 **[仮想マシン]**  >  **[すべての設定]**  >  **[拡張機能]** に移動します。
* バックアップ拡張機能 [VmSnapshot] または [VmSnapshotLinux] を選択し、 **[アンインストール]** を選択します。
* バックアップ拡張機能を削除した後、バックアップ操作を再試行します
* 以降のバックアップ操作によって、新しい拡張機能が適切な状態でインストールされます

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - 接続されている一部のディスクでスナップショットの制限を超えたため、スナップショット操作に失敗しました

エラー コード:ExtensionFailedSnapshotLimitReachedError  <br/>
エラー メッセージ:接続されている一部のディスクでスナップショットの制限を超えたため、スナップショット操作に失敗しました

接続されている一部のディスクでスナップショットの制限を超えたため、スナップショット操作に失敗しました。 次のトラブルシューティング手順を完了してから、必要な操作を再試行してください。

* 必要のないディスク BLOB スナップショットを削除します。必要のないディスク BLOB スナップショットを削除します。 ディスク BLOB を削除しないように注意してください。 スナップショット BLOB のみを削除する必要があります。
* VM ディスクのストレージ アカウントで論理的な削除が有効になっている場合は、論理的な削除のリテンション期間を、常に既存のスナップショットが許可される最大値未満になるように構成します。
* バックアップされた VM で Azure Site Recovery が有効になっている場合は、次の手順を実行します。

  * /etc/azure/vmbackup.conf で **isanysnapshotfailed** の値が false に設定されていることを確認します。
  * Azure Site Recovery をバックアップ操作と競合しないように別の時間にスケジュールします。

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - VM リソースの不足のためにスナップショット操作に失敗しました

エラー コード:ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
エラー メッセージ:VM リソースの不足のためにスナップショット操作に失敗しました。

スナップショット操作の実行中のネットワーク呼び出しの遅延のために、VM でのバックアップ操作に失敗しました。 この問題を解決するには、手順 1. を実行します。 問題が解決しない場合は、手順 2. と 3. をお試しください。

**手順 1.** :ホストを介してスナップショットを作成する

管理者特権での (管理者) コマンド プロンプトから、次のコマンドを実行します。

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

これにより、ゲストではなくホストを介して確実にスナップショットが作成されます。 バックアップ操作を再試行します。

**手順 2**:VM の負荷が少ない (たとえば、CPU または IOPS が低い) 時間帯へのバックアップ スケジュールの変更を試みます

**手順 3**:[VM のサイズの増加](../virtual-machines/windows/resize-vm.md)を試み、操作を再試行します。

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001、ResourceNotFound - VM が存在しないため、操作を実行できませんでした / 400094、BCMV2VMNotFound - 仮想マシンが存在しません / Azure 仮想マシンが見つかりませんでした

エラー コード:320001、ResourceNotFound <br/> エラー メッセージ:VM が存在しないため、操作を実行できませんでした。 <br/> <br/> エラー コード:400094、BCMV2VMNotFound <br/> エラー メッセージ:仮想マシンが存在しません <br/>
Azure 仮想マシンが見つかりませんでした。

このエラーは、プライマリ VM が削除されているのに、バックアップ ポリシーによってバックアップする VM が引き続き検索される場合に発生します。 このエラーを解決するには、次の手順を実行します。

* 同じ名前と同じリソース グループ名、**クラウド サービス名** を持つ仮想マシンを作成し直します。<br>or
* バックアップ データを削除して、または削除しないで、仮想マシンの保護を停止します。 詳細については、「[仮想マシンの保護を停止する](backup-azure-manage-vms.md#stop-protecting-a-vm)」を参照してください。</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError - ストレージ アカウント内の空き領域の不足のために、仮想マシンのスナップショットをコピーできませんでした

エラー コード:UserErrorBCMPremiumStorageQuotaError<br/> エラー メッセージ:ストレージ アカウント内の空き領域の不足のために、仮想マシンのスナップショットをコピーできませんでした

 VM バックアップ スタック V1 の Premium VM の場合、スナップショットはストレージ アカウントにコピーされます。 この手順により、スナップショット上で動作するバックアップ管理トラフィックが、プレミアム ディスクを使用するアプリケーションで利用できる IOPS の数を制限しないようになります。 <br><br>ストレージ アカウントの合計領域の 50% (17.5 TB) のみを割り当てることをお勧めします。 これにより、Azure Backup サービスが、スナップショットをストレージ アカウントにコピーし、ストレージ アカウント内のこのコピーされた場所からコンテナーにデータを転送することができます。

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008、AzureVmOffline - 仮想マシンが実行されていないため、Microsoft Recovery Services の拡張機能をインストールできませんでした

エラー コード:380008、AzureVmOffline <br/> エラー メッセージ:仮想マシンが実行されていないため、Microsoft Recovery Services の拡張機能をインストールできませんでした

VM エージェントは、Azure Recovery Services 拡張機能の前提条件です。 Azure 仮想マシン エージェントをインストールしてから、登録操作をやり直してください。 <br> <ol> <li>VM エージェントが正しくインストールされていることを確認します。 <li>VM 構成のフラグが正しく設定されていることを確認します。</ol> VM エージェントのインストール方法と、VM エージェントのインストールを検証する方法に関するセクションを参照してください。

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError - スナップショットの操作が、ボリューム シャドウ コピー サービス (VSS) の操作エラーで失敗しました。

エラー コード:ExtensionSnapshotBitlockerError <br/> エラー メッセージ:スナップショットの操作は、**このドライブは、BitLocker ドライブ暗号化でロックされています。コントロール パネルからドライブのロックを解除してください。** というボリューム シャドウ コピー サービス (VSS) 操作エラーで失敗しました。

VM 上のすべてのドライブで BitLocker をオフにして、VSS の問題が解決されたかどうかを確認します。

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState - VM はバックアップが可能な状態にありません

エラー コード:VmNotInDesirableState <br/> エラー メッセージ:VM はバックアップできる状態ではありません。

* VM が **実行** と **シャットダウン** の間の一時的な状態である場合は、状態が変わるのを待ちます。 その後、バックアップ ジョブをトリガーします。
* VM が Linux VM で、Security-Enhanced Linux カーネル モジュールが使用されている場合は、Azure Linux エージェントのパス **/var/lib/waagent** をセキュリティ ポリシーから除外して、Backup 拡張機能が確実にインストールされるようにします。

* VM エージェントが仮想マシンに存在しません: <br>前提条件と VM エージェントをインストールします。 その後、操作を再開します。 VM エージェントのインストール方法と、VM エージェントのインストールを検証する方法については、[こちら](#vm-agent)を参照してください。

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork - セキュリティで保護されたネットワーク通信チャネルを作成できないため、スナップショット操作が失敗しました

エラー コード:ExtensionSnapshotFailedNoSecureNetwork <br/> エラー メッセージ:セキュリティで保護されたネットワーク通信チャネルを作成できないため、スナップショット操作が失敗しました。

* 管理者特権モードで **regedit.exe** を実行してレジストリ エディターを開きます。
* お使いのシステムに存在する .NET Framework のすべてのバージョンを識別します。 それらは、レジストリ キーの階層 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** の下にあります。
* レジストリ キー内に存在する各 .NET Framework に対して、次のキーを追加します。 <br> **SchUseStrongCrypto"=dword:00000001**。 </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure - Visual Studio 2012 用の Visual C++ 再頒布可能パッケージをインストールできないため、スナップショット操作が失敗しました

エラー コード:ExtensionVCRedistInstallationFailure <br/> エラー メッセージ:Visual Studio 2012 用の Visual C++ 再頒布可能パッケージをインストールできないため、スナップショット操作が失敗しました。

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` に移動し、vcredist2013_x64 をインストールします。<br/>このサービスのインストールを許可するレジストリ キーの値が正しい値に設定されていることを確認します。 つまり、**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** の **Start** 値を **4** ではなく **3** に設定します。 <br><br>インストールに関する問題が解消されない場合は、管理者特権でコマンド プロンプトから **MSIEXEC /UNREGISTER** と **MSIEXEC /REGISTER** を続けて実行して、インストール サービスを再起動します。
* イベント ログを確認して、アクセス関連の問題が発生しているかどうかを確認します。 次に例を示します。*製品:Microsoft Visual C++ 2013 x64 Minimum Runtime - 12.0.21005 -- エラー 1401。キー Software\Classes を作成できませんでした。システム エラー 5。このキーへの十分なアクセス権があることを確認するか、またはサポート担当者に問い合わせてください。* <br><br> 管理者またはユーザーのアカウントに、レジストリ キー **HKEY_LOCAL_MACHINE\SOFTWARE\Classes** を更新するための十分なアクセス許可があることを確認します。 十分なアクセス許可を付与し、Windows Azure ゲスト エージェントを再起動します。<br><br> <li> ウイルス対策製品を使用している場合は、インストールを許可する適切な除外ルールがあることを確認します。

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy - VM に無効なポリシーが構成されており、スナップショット操作が妨げられています

エラー コード:UserErrorRequestDisallowedByPolicy <BR> エラー メッセージ:VM に無効なポリシーが構成されており、スナップショット操作が妨げられています。

[環境内のタグを管理する](../governance/policy/tutorials/govern-tags.md) Azure Policy がある場合は、ポリシーを [Deny 効果](../governance/policy/concepts/effects.md#deny)から [Modify 効果](../governance/policy/concepts/effects.md#modify)に変更することを検討するか、[Azure Backup で要求される名前付けスキーマ](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)に従って手動でリソース グループを作成してください。

## <a name="jobs"></a>ジョブ

| エラーの詳細 | 回避策 |
| --- | --- |
| このジョブの種類では、取り消しはサポートされていません。 <br>ジョブが完了するまでお待ちください。 |なし |
| ジョブは、取り消し可能な状態ではありません。 <br>ジョブが完了するまでお待ちください。 <br>**or**<br> 選択されたジョブは、取り消し可能な状態ではありません。 <br>ジョブが完了するまでお待ちください。 |ジョブは終了間近である可能性があります。 ジョブが完了するまでお待ちください。|
| ジョブは実行中ではないため、Backup で取り消すことができません: <br>取り消しは実行中のジョブに対してのみサポートされています。 実行中のジョブを取り消してみてください。 |このエラーは一時的な状態が原因で発生します。 しばらく待ってから、取り消し操作をやり直してください。 |
| Backup でジョブを取り消すことができませんでした: <br>ジョブが完了するまでお待ちください。 |なし |

## <a name="restore"></a>復元

### <a name="disks-appear-offline-after-file-restore"></a>ファイル復元後にディスクがオフラインと表示される

復元後、ディスクがオフラインになっている場合は、次のことを行います。

* スクリプトが実行されるマシンが OS の要件を満たしているかどうかを確認します。 [詳細については、こちらを参照してください](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script)。  
* 同じソースに復元していないことを確実にします。[詳細については、こちらを参照してください](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound - VM のスナップショットが見つからなかったため、復元に失敗しました

エラー コード:UserErrorInstantRpNotFound <br>
エラー メッセージ:VM のスナップショットが見つからなかったため、復元に失敗しました。 スナップショットが削除されている可能性があります。確認してください。<br>

このエラーは、コンテナーに転送されず、スナップショット フェーズで削除された復旧ポイントから復元しようとするときに発生します。 
<br>
この問題を解決するには、別の復旧ポイントから VM の復元を試してください。<br>

#### <a name="common-errors"></a>一般的なエラー 
| エラーの詳細 | 回避策 |
| --- | --- |
| クラウドの内部エラーで復元に失敗しました。 |<ol><li>復元を試みているクラウド サービスが DNS 設定で構成されています。 次のように入力して確認できます。 <br>**$deployment = Get-AzureDeployment -ServiceName "ServiceName" -Slot "Production"     Get-AzureDns -DnsSettings $deployment.DnsSettings**.<br>**[アドレス]** が構成済みの場合は、DNS 設定が構成済みです。<br> <li>復元を試みているクラウド サービスが **ReservedIP** で構成されていて、クラウド サービスの既存の VM が停止状態になっています。 次の PowerShell コマンドレットを使用して、クラウド サービスに IP が予約されていることを確認できます。 **$deployment = Get-AzureDeployment -ServiceName "servicename" -Slot "Production" $dep.ReservedIPName**。 <br><li>次の特殊なネットワーク構成の仮想マシンを同じクラウド サービスに復元しようとしています。 <ul><li>ロード バランサー構成 (内部および外部の) での仮想マシン。<li>複数の予約済み IP を持つ仮想マシン。 <li>複数の NIC を持つ仮想マシン。 </ul><li>UI で新しいクラウド サービスを選択するか、特殊なネットワーク構成の VM の[復元に関する考慮事項](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations)を参照してください。</ol> |
| 選択された DNS 名は既に登録されています: <br>別の DNS 名を指定してからやり直してください。 |この DNS 名はクラウド サービス名 (通常、末尾に **cloudapp.net** が付いています) を表します。 この名前は一意である必要があります。 このエラーが発生した場合は、復元中に別の VM の名前を選択する必要があります。 <br><br> このエラーは Azure Portal のユーザーのみに表示されます。 PowerShell による復元操作は、ディスクを復元するだけで VM を作成しないため、成功します。 ディスクの復元操作後に VM を明示的に作成すると、このエラーが発生します。 |
| 指定された仮想ネットワーク構成が正しくありません: <br>別の仮想ネットワークの構成を指定してからやり直してください。 |なし |
| 指定されたクラウド サービスは、復元する仮想マシンの構成と一致しない予約済み IP を使用しています: <br>予約済み IP を使用していない別のクラウド サービスを指定します。 または、復元に使用する別の復旧ポイントを選択します。 |なし |
| クラウド サービスが入力エンドポイントの数に制限に達しました: <br>別のクラウド サービスを指定するか既存のエンドポイントを使用して、操作をやり直してください。 |なし |
| Recovery Services コンテナーと対象のストレージ アカウントが 2 つの異なるリージョンに存在します: <br>復元操作で指定したストレージ アカウントが、Recovery Services コンテナーと同じ Azure リージョンに存在するようにしてください。 |なし |
| 復元操作に指定されたストレージ アカウントがサポートされていません: <br>サポートされているのは、ローカル冗長レプリケーションまたは geo 冗長レプリケーションの設定が指定された Basic または Standard ストレージ アカウントのみです。 サポートされているストレージ アカウントを選択してください。 |なし |
| 復元操作に指定したストレージ アカウントの種類がオンラインではありません: <br>復元操作に指定したストレージ アカウントの種類がオンラインであることを確認してください。 |このエラーは、Azure Storage の一時的なエラーや障害が原因で発生する可能性があります。 別のストレージ アカウントを選択してください。 |
| リソース グループのクォータに達しました: <br>Azure portal の一部のリソース グループを削除するか、Azure サポートに問い合わせて上限を引き上げてください。 |なし |
| 選択したサブネットが存在しません: <br>存在するサブネットを選択してください。 |なし |
| Backup サービスは、サブスクリプション内のリソースへのアクセスが承認されていません。 |このエラーを解決するには、「[バックアップ ディスクを復元する](backup-azure-arm-restore-vms.md#restore-disks)」で説明されている手順を使用して、最初にディスクを復元します。 その後、「[復元されたディスクからの VM の作成](backup-azure-vms-automation.md#restore-an-azure-vm)」で説明されている PowerShell の手順を行います。 |

## <a name="backup-or-restore-takes-time"></a>バックアップまたは復元に要する時間

バックアップに 12 時間以上、または復元に 6 時間以上かかる場合は、[ベスト プラクティス](backup-azure-vms-introduction.md#best-practices)と[パフォーマンスに関する考慮事項](backup-azure-vms-introduction.md#backup-performance)を確認してください。

## <a name="vm-agent"></a>VM エージェント

### <a name="set-up-the-vm-agent"></a>VM エージェントの設定

通常、VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。 しかし、オンプレミスのデータセンターから移行された仮想マシンには VM エージェントがインストールされていません。 それらの仮想マシンについては、VM エージェントを明示的にインストールする必要があります。

#### <a name="windows-vms---set-up-the-agent"></a>Windows VM - エージェントの設定

* [エージェント MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードしてインストールします。 インストールを完了するには、管理者特権が必要です。
* クラシック デプロイ モデルを使用して作成された仮想マシンの場合は、[VM のプロパティを更新](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms)して、エージェントがインストールされたことを示します。 この手順は、Azure Resource Manager 仮想マシンの場合は必要ありません。

#### <a name="linux-vms---set-up-the-agent"></a>Linux VM - エージェントの設定

* ディストリビューション リポジトリから最新バージョンのエージェントをインストールします。 パッケージ名について詳しくは、[Linux エージェント リポジトリ](https://github.com/Azure/WALinuxAgent)をご覧ください。
* クラシック デプロイ モデルを使用して作成された仮想マシンの場合は、[VM のプロパティを更新](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms)し、エージェントがインストールされていることを確認します。 このステップは、Resource Manager 仮想マシンの場合は必要ありません。

### <a name="update-the-vm-agent"></a>VM エージェントの更新

#### <a name="windows-vms---update-the-agent"></a>Windows VM - エージェントの更新

* VM エージェントを更新するには、[VM エージェント バイナリ](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)を再インストールします。 エージェントを更新する前に、VM エージェントの更新中にバックアップ操作が発生していないことを確認します。

#### <a name="linux-vms---update-the-agent"></a>Linux VM - エージェントの更新

* Linux VM エージェントを更新するには、[Linux VM エージェントの更新](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json)に関する記事の手順に従います。

    > [!NOTE]
    > 常にディストリビューション リポジトリを使用してエージェントを更新する必要があります。

    エージェントのコードを GitHub からダウンロードしないでください。 お使いのディストリビューションで最新のエージェントを使用できない場合は、最新のエージェントを入手する方法についてディストリビューション サポートに問い合わせてください。 GitHub リポジトリで [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)の最新情報を確認することもできます。

### <a name="validate-vm-agent-installation"></a>VM エージェントのインストールの検証

Windows VM 上で VM エージェントのバージョンを確認するには、次の手順に従います。

1. Azure 仮想マシンにサインインし、**C:\WindowsAzure\Packages** フォルダーに移動します。 **WaAppAgent.exe** ファイルを見つけてください。
2. ファイルを右クリックし、 **[プロパティ]** に移動します。 次に、 **[詳細]** タブを選択します。**製品バージョン** が 2.6.1198.718 以上であることを確認します。

## <a name="troubleshoot-vm-snapshot-issues"></a>VM スナップショットに関する問題のトラブルシューティング

VM のバックアップは、基礎をなすストレージへのスナップショット コマンドの発行に依存します。 ストレージにアクセスできなかったり、スナップショット タスクの実行が遅延したりすると、バックアップ ジョブが失敗することがあります。 次の場合にスナップショットのタスクが失敗することがあります。

* **SQL Server のバックアップが構成されている VM はスナップショット タスクの遅延を引き起こすことがあります**。 既定では、VM バックアップによって Windows VM 上に VSS フル バックアップが作成されます。 SQL Server を実行していて SQL Server のバックアップを構成されている VM では、スナップショットの遅延が発生する可能性があります。 スナップショットの遅延が原因でバックアップが失敗する場合は、次のレジストリ キーを設定します。

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **VM が RDP でシャットダウンされているため、VM の状態が正しく報告されません**。 リモート デスクトップを使用して仮想マシンをシャットダウンした場合は、ポータルで VM の状態が正しいことを確認します。 状態が正しくない場合は、ポータルの VM ダッシュボードの **[シャットダウン]** オプションを使用して VM をシャットダウンします。
* **5 つ以上の VM が同じクラウド サービスを共有している場合は、VM を複数のバックアップ ポリシーに分散します**。 4 つより多くの VM バックアップが同時に開始しないように、バックアップ時刻を調整します。 ポリシーで開始時刻が 1 時間以上離れるようにしてください。
* **VM の CPU またはメモリが高くなっています**。 仮想マシンのメモリまたは CPU の使用率が高くなり、90% を超えてると、スナップショット タスクがキューに格納されて遅延します。 最終的にはタイムアウトになります。この問題が発生した場合は、オンデマンド バックアップを試してください。

## <a name="networking"></a>ネットワーク

IaaS VM バックアップが正しく機能するためには、ゲスト内で DHCP が有効になっている必要があります。 静的プライベート IP が必要な場合は、Azure portal または PowerShell を使用して構成します。 VM 内の DHCP オプションが有効になっていることを確認します。
PowerShell を使用して静的 IP を設定する方法については、以下を参照してください。

* [既存の VM に静的内部 IP を追加する方法](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [ネットワーク インターフェイスに割り当てられているプライベート IP アドレスの割り当て方法を変更する](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)