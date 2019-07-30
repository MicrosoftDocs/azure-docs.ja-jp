---
title: Azure 仮想マシンのバックアップ エラーのトラブルシューティング
description: Azure 仮想マシンのバックアップと復元のトラブルシューティング
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 4f95192aa2b3c5890a3cafbb442f9f15ebee9280
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465197"
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Azure 仮想マシンのバックアップのトラブルシューティング
次の情報を使って、Azure Backup の使用中に発生したエラーのトラブルシューティングを行うことができます。

## <a name="backup"></a>バックアップ
このセクションでは、Azure 仮想マシンのバックアップ操作のエラーについて説明します。

## <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime - コンテナーからのバックアップされたデータのコピーがタイムアウトしました

エラー コード:CopyingVHDsFromBackUpVaultTakingLongTime <br/>
エラー メッセージ:コンテナーからのバックアップされたデータのコピーがタイムアウトしました

これは、バックアップ サービスがタイムアウト期間内にデータをコンテナーに転送するときの一時的なストレージ エラーまたはストレージ アカウント IOPS の不足のために発生することがあります。 これらの[ベスト プラクティス](backup-azure-vms-introduction.md#best-practices)を使用して VM バックアップを構成し、バックアップ操作を再試行します。

## <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM がバックアップできる状態にありません

エラー コード:UserErrorVmNotInDesirableState <br/>
エラー メッセージ:VM はバックアップできる状態ではありません。<br/>

VM が [失敗] 状態にあるため、バックアップ操作に失敗しました。 バックアップに成功するには、VM の状態が [実行中]、[停止済み]、または [停止済み (割り当て解除)] である必要があります。

* VM が**実行**と**シャットダウン**の間の一時的な状態である場合は、状態が変わるのを待ちます。 その後、バックアップ ジョブをトリガーします。
*  VM が Linux VM で、Security-Enhanced Linux カーネル モジュールが使用されている場合は、Azure Linux エージェントのパス **/var/lib/waagent** をセキュリティ ポリシーから除外して、Backup 拡張機能が確実にインストールされるようにします。

## <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed - ファイル システム整合性スナップショットの取得で VM の 1 つ以上のマウント ポイントの凍結に失敗しました

エラー コード:UserErrorFsFreezeFailed <br/>
エラー メッセージ:ファイル システム整合性スナップショットの取得で VM の 1 つ以上のマウント ポイントの凍結に失敗しました。

* **tune2fs** コマンド (たとえば、**tune2fs -l /dev/sdb1 \\** .\| grep **Filesystem state**) を使用して、マウントされているすべてのデバイスのファイル システム状態を確認します。
* **umount** コマンドを使用して、ファイル システム状態がクリーニングされなかったデバイスをマウント解除します。
* **fsck** コマンドを使用して、これらのデバイスに対してファイル システム整合性チェックを実行します。
* これらのデバイスを再度マウントし、バックアップ操作を再試行します。</ol>

## <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC - COM+ エラーのために拡張機能のインストール/操作に失敗しました

エラー コード:ExtensionSnapshotFailedCOM <br/>
エラー メッセージ:COM+ エラーが発生したため、スナップショット操作に失敗しました

エラー コード:ExtensionInstallationFailedCOM  <br/>
エラー メッセージ:COM+ エラーのために拡張機能のインストール/操作に失敗しました

エラー コード:ExtensionInstallationFailedMDTC <br/>
エラー メッセージ:"COM+: Microsoft 分散トランザクション コーディネーターと通信できませんでした" というエラーで拡張機能のインストールが失敗しました。 <br/>

Windows サービス **COM+ System** Application での問題のためにバックアップ操作に失敗しました。  この問題を解決するには、次の手順に従ってください。

* Windows サービス **COM+ System Application** の起動/再起動を試みます (管理者特権でのコマンド プロンプトから **- net start COMSysApp**)。
* **分散トランザクション コーディネーター** サービスが**ネットワーク サービス** アカウントとして実行されていることを確認します。 そうでない場合は、**ネットワーク サービス** アカウントとして実行されるように変更し、**COM+ System Application** を再起動します。
* このサービスを再起動できない場合は、次の手順に従って**分散トランザクション コーディネーター** サービスを再インストールします。
    * MSDTC サービスを停止します
    * コマンド プロンプト (cmd) を開きます
    * コマンド "msdtc -uninstall" を実行します
    * コマンド "msdtc -install" を実行します
    * MSDTC サービスを起動します
* Windows サービス **COM+ システム アプリケーション**を開始します。 **COM+ システム アプリケーション**が開始したら、Azure portal からバックアップ ジョブをトリガーします。</ol>

## <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - VSS ライターが正しくない状態にあるため、スナップショット操作に失敗しました

エラー コード:ExtensionFailedVssWriterInBadState <br/>
エラー メッセージ:VSS ライターが正しくない状態にあるため、スナップショット操作に失敗しました。

状態が正しくない VSS ライターを再起動します。 管理者特権でのコマンド プロンプトから、```vssadmin list writers``` を実行します。 出力には、すべての VSS ライターとそれらの状態が含まれています。 **[1] 安定**状態ではない VSS ライターすべてに対して、VSS ライターを再起動するには、管理者特権でのコマンド プロンプトから次のコマンドを実行します。

  * ```net stop serviceName```
  * ```net start serviceName```

## <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure - バックアップ拡張機能の構成の解析に失敗しました

エラー コード:ExtensionConfigParsingFailure<br/>
エラー メッセージ:バックアップ拡張機能の構成の解析に失敗しました。

このエラーは、次の **MachineKeys** ディレクトリでアクセス許可が変更されたために発生します。 **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**。
コマンド **icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys** を実行し、**MachineKeys** ディレクトリのアクセス許可が既定のものであることを確認してください。

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
2. **発行先**がクラシック デプロイ モデルまたは **Windows Azure CRP Certificate Generator** であるすべての証明書を削除します。
    * [ローカル コンピューターのコンソールで証明書を開きます](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)。
    * **[個人]**  >  **[証明書]** で、**発行先**がクラシック デプロイ モデルまたは **Windows Azure CRP Certificate Generator** であるすべての証明書を削除します。
3. VM バックアップ ジョブをトリガーします。

## <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState - 拡張機能の状態がバックアップ操作に対応していません

エラー コード:ExtensionStuckInDeletionState <br/>
エラー メッセージ:拡張機能の状態がバックアップ操作に対応していません

バックアップ拡張機能の整合性のない状態のためにバックアップ操作に失敗しました。 この問題を解決するには、次の手順に従ってください。

* ゲスト エージェントがインストールされ、応答していることを確認します
* Azure portal で **[仮想マシン]**  >  **[すべての設定]**  >  **[拡張機能]** に移動します
* バックアップ拡張機能 VmSnapshot または VmSnapshotLinux を選択し、 **[アンインストール]** をクリックします
* バックアップ拡張機能の削除後、バックアップ操作を再試行します
* 以降のバックアップ操作によって、新しい拡張機能が適切な状態でインストールされます

## <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError - 接続されている一部のディスクでスナップショットの制限を超えたため、スナップショット操作に失敗しました

エラー コード:ExtensionFailedSnapshotLimitReachedError  <br/>
エラー メッセージ:接続されている一部のディスクでスナップショットの制限を超えたため、スナップショット操作に失敗しました

接続されている一部のディスクでスナップショットの制限を超えたため、スナップショット操作に失敗しました。 次のトラブルシューティング手順を完了してから、操作を再試行してください。

* 必要のないディスク BLOB スナップショットを削除します。 ディスク BLOB を削除しないように注意してください。削除するのはスナップショット BLOB だけです。
* VM ディスクのストレージ アカウントで論理的な削除が有効になっている場合は、論理的な削除のリテンション期間を、常に既存のスナップショットが許可される最大値未満になるように構成します。
* バックアップされた VM で Azure Site Recovery が有効になっている場合は、次を実行します。

    * /etc/azure/vmbackup.conf で **isanysnapshotfailed** の値が false に設定されていることを確認します。
    * Azure Site Recovery をバックアップ操作と競合しないように別の時間にスケジュールします。

## <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive - VM リソースの不足のためにスナップショット操作に失敗しました。

エラー コード:ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
エラー メッセージ:VM リソースの不足のためにスナップショット操作に失敗しました。

スナップショット操作の実行中のネットワーク呼び出しの遅延のために、VM でのバックアップ操作に失敗しました。 この問題を解決するには、手順 1. を実行します。 問題が解決しない場合は、手順 2. と 3. をお試しください。

**手順 1.** :ホストを介してスナップショットを作成する

管理者特権での (管理者) コマンド プロンプトで、次のコマンドを実行します。

```
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

これにより、ゲストではなくホストを介して確実にスナップショットが作成されます。 バックアップ操作を再試行します。

**手順 2**:VM の負荷が少ない (たとえば、CPU/IOps が低い) 時間帯へのバックアップ スケジュールの変更を試みます。

**手順 3**:[VM のサイズの増加](https://azure.microsoft.com/blog/resize-virtual-machines/)を試み、操作を再試行します。

## <a name="common-vm-backup-errors"></a>一般的な VM バックアップのエラー

| エラーの詳細 | 対処法 |
| ------ | --- |
| エラー コード:320001<br/> エラー メッセージ:VM が存在しないため、操作を実行できませんでした。 <br/> <br/> エラー コード:400094 <br/> エラー メッセージ:仮想マシンが存在しません <br/> <br/>  Azure 仮想マシンが見つかりませんでした。  |このエラーは、プライマリ VM が削除されているのに、バックアップ ポリシーによってバックアップする VM が引き続き検索される場合に発生します。 このエラーを解決するには、次の手順を実行します。 <ol><li> 同じ名前と同じリソース グループ名、**クラウド サービス名**を持つ仮想マシンを作成し直します。<br>**or**</li><li> バックアップ データを削除して、または削除しないで、仮想マシンの保護を停止します。 詳細については、「[仮想マシンの保護を停止する](backup-azure-manage-vms.md#stop-protecting-a-vm)」を参照してください。</li></ol>|
| VM はプロビジョニングに失敗した状態です: <br>VM を再起動し、VM が実行中とシャットダウンのいずれかの状態になっていることを確認します。 | このエラーは、いずれかの拡張機能が失敗して、VM がプロビジョニングに失敗した状態になる場合に発生します。 拡張機能の一覧に移動し、失敗した拡張機能があるかどうかを確認して、それを削除し、仮想マシンを再起動してみます。 すべての拡張機能が実行状態になっている場合は、VM エージェント サービスが実行されているかどうかを確認してください。 されていない場合は、VM エージェント サービスを再起動します。 |
|エラー コード:UserErrorBCMPremiumStorageQuotaError<br/> エラー メッセージ:ストレージ アカウント内の空き領域の不足のために、仮想マシンのスナップショットをコピーできませんでした | VM バックアップ スタック V1 の Premium VM の場合、スナップショットはストレージ アカウントにコピーされます。 この手順により、スナップショット上で動作するバックアップ管理トラフィックが、プレミアム ディスクを使用するアプリケーションで利用できる IOPS の数を制限しないようになります。 <br><br>ストレージ アカウントの合計領域の 50% (17.5 TB) のみを割り当てることをお勧めします。 これにより、Azure Backup サービスが、スナップショットをストレージ アカウントにコピーし、ストレージ アカウント内のこのコピーされた場所からコンテナーにデータを転送することができます。 |
| 仮想マシンが実行されていないため、Microsoft Recovery Services の拡張機能をインストールできませんでした <br>VM エージェントは、Azure Recovery Services 拡張機能の前提条件です。 Azure 仮想マシン エージェントをインストールしてから、登録操作をやり直してください。 |<ol> <li>VM エージェントが正しくインストールされていることを確認します。 <li>VM 構成のフラグが正しく設定されていることを確認します。</ol> VM エージェントのインストール方法と、VM エージェントのインストールを検証する方法に関するセクションを参照してください。 |
| スナップショットの操作は、**このドライブは、BitLocker ドライブ暗号化でロックされています。コントロール パネルからドライブのロックを解除してください。** というボリューム シャドウ コピー サービス (VSS) 操作エラーで失敗しました。 |VM 上のすべてのドライブで BitLocker をオフにして、VSS の問題が解決されたかどうかを確認します。 |
| VM はバックアップできる状態ではありません。 |<ul><li>VM が**実行**と**シャットダウン**の間の一時的な状態である場合は、状態が変わるのを待ちます。 その後、バックアップ ジョブをトリガーします。 <li> VM が Linux VM で、Security-Enhanced Linux カーネル モジュールが使用されている場合は、Azure Linux エージェントのパス **/var/lib/waagent** をセキュリティ ポリシーから除外して、Backup 拡張機能が確実にインストールされるようにします。  |
| VM エージェントが仮想マシンに存在しません: <br>前提条件と VM エージェントをインストールします。 その後、操作を再開します。 |VM エージェントのインストール方法と、VM エージェントのインストールを検証する方法については、[こちら](#vm-agent)を参照してください。 |
| Backup でファイル システムの一貫性のあるスナップショットの取得で VM の 1 つまたは複数のマウント ポイントをフリーズできませんでした。 | 次の手順を実行します。 <ul><li>**'tune2fs'** コマンドを使用して、マウントされているすべてのデバイスのファイル システムの状態を確認します。 たとえば、**tune2fs -l /dev/sdb1 \\** .\| grep **Filesystem state** のようにします。 <li>ファイル システムの状態がクリーンではないデバイスを、 **'umount'** コマンドを使用してマウント解除します。 <li> これらのデバイスで、 **'fsck'** コマンドを使用してファイルシステム整合性チェックを実行します。 <li> デバイスを再度マウントして、バックアップをやり直します。</ol> |
| セキュリティで保護されたネットワーク通信チャネルを作成できないため、スナップショット操作が失敗しました。 | <ol><li> 管理者特権モードで **regedit.exe** を実行してレジストリ エディターを開きます。 <li> お使いのシステムに存在する .NET Framework のすべてのバージョンを識別します。 それらは、レジストリ キーの階層 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** の下にあります。 <li> レジストリ キー内に存在する各 .NET Framework に対して、次のキーを追加します。 <br> **SchUseStrongCrypto"=dword:00000001**。 </ol>|
| Visual Studio 2012 用の Visual C++ 再頒布可能パッケージをインストールできないため、スナップショット操作が失敗しました。 | C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion に移動し、vcredist2012_x64 をインストールします。<br/>このサービスのインストールを許可するレジストリ キーの値が正しい値に設定されていることを確認します。 つまり、**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** の **Start** 値を **4** ではなく **3** に設定します。 <br><br>インストールに関する問題が解消されない場合は、管理者特権でコマンド プロンプトから **MSIEXEC /UNREGISTER** と **MSIEXEC /REGISTER** を続けて実行して、インストール サービスを再起動します。  |


## <a name="jobs"></a>[ジョブ]

| エラーの詳細 | 対処法 |
| --- | --- |
| このジョブの種類では、取り消しはサポートされていません。 <br>ジョブが完了するまでお待ちください。 |なし |
| ジョブは、取り消し可能な状態ではありません。 <br>ジョブが完了するまでお待ちください。 <br>**or**<br> 選択されたジョブは、取り消し可能な状態ではありません。 <br>ジョブが完了するまでお待ちください。 |ジョブは終了間近である可能性があります。 ジョブが完了するまでお待ちください。|
| ジョブは実行中ではないため、Backup で取り消すことができません: <br>取り消しは実行中のジョブに対してのみサポートされています。 実行中のジョブを取り消してみてください。 |このエラーは一時的な状態が原因で発生します。 しばらく待ってから、取り消し操作をやり直してください。 |
| Backup でジョブを取り消すことができませんでした: <br>ジョブが完了するまでお待ちください。 |なし |

## <a name="restore"></a>復元

| エラーの詳細 | 対処法 |
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
バックアップが 12 時間以上かかる場合、または復元が 6 時間以上かかる場合は、[ベスト プラクティス](backup-azure-vms-introduction.md#best-practices)と[パフォーマンスに関する考慮事項](backup-azure-vms-introduction.md#backup-performance)を確認してください

## <a name="vm-agent"></a>VM エージェント
### <a name="set-up-the-vm-agent"></a>VM エージェントの設定
通常、VM エージェントは、Azure ギャラリーから作成された仮想マシン内に既に存在しています。 しかし、オンプレミスのデータセンターから移行された仮想マシンには VM エージェントがインストールされていません。 それらの仮想マシンについては、VM エージェントを明示的にインストールする必要があります。

#### <a name="windows-vms"></a>Windows VM

* [エージェント MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)をダウンロードしてインストールします。 インストールを完了するには、管理者特権が必要です。
* クラシック デプロイ モデルを使用して作成された仮想マシンの場合は、[VM のプロパティを更新](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)して、エージェントがインストールされたことを示します。 この手順は、Azure Resource Manager 仮想マシンの場合は必要ありません。

#### <a name="linux-vms"></a>Linux VM

* ディストリビューション リポジトリから最新バージョンのエージェントをインストールします。 パッケージ名について詳しくは、[Linux エージェント リポジトリ](https://github.com/Azure/WALinuxAgent)をご覧ください。
* クラシック デプロイ モデルを使用して作成された仮想マシンの場合は、[こちらのブログに従って](https://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) VM のプロパティを更新し、エージェントがインストールされていることを確認します。 このステップは、Resource Manager 仮想マシンの場合は必要ありません。

### <a name="update-the-vm-agent"></a>VM エージェントの更新
#### <a name="windows-vms"></a>Windows VM

* VM エージェントを更新するには、[VM エージェント バイナリ](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)を再インストールします。 エージェントを更新する前に、VM エージェントの更新中にバックアップ操作が発生していないことを確認します。

#### <a name="linux-vms"></a>Linux VM

* Linux VM エージェントを更新するには、[Linux VM エージェントの更新](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の手順に従います。

    > [!NOTE]
    > 常にディストリビューション リポジトリを使用してエージェントを更新する必要があります。

    エージェントのコードを GitHub からダウンロードしないでください。 お使いのディストリビューションで最新のエージェントを使用できない場合は、最新のエージェントを入手する方法についてディストリビューション サポートに問い合わせてください。 GitHub リポジトリで [Windows Azure Linux エージェント](https://github.com/Azure/WALinuxAgent/releases)の最新情報を確認することもできます。

### <a name="validate-vm-agent-installation"></a>VM エージェントのインストールの検証

Windows VM 上で VM エージェントのバージョンを確認するには、次の手順に従います。

1. Azure 仮想マシンにサインインし、**C:\WindowsAzure\Packages** フォルダーに移動します。 **WaAppAgent.exe** ファイルを見つけてください。
2. ファイルを右クリックし、 **[プロパティ]** に移動します。 次に、 **[詳細]** タブを選択します。**製品バージョン**が 2.6.1198.718 以上であることを確認します。

## <a name="troubleshoot-vm-snapshot-issues"></a>VM スナップショットに関する問題のトラブルシューティング
VM のバックアップは、基礎をなすストレージへのスナップショット コマンドの発行に依存します。 ストレージにアクセスできなかったり、スナップショット タスクの実行が遅延したりすると、バックアップ ジョブが失敗することがあります。 次の場合にスナップショットのタスクが失敗することがあります。

- **NSG を使用してストレージへのネットワーク アクセスがブロックされています**。 IP の許可リストまたはプロキシ サーバーを使用してストレージへの[ネットワーク アクセスを有効にする](backup-azure-arm-vms-prepare.md#establish-network-connectivity)方法の詳細を参照してください。
- **SQL Server のバックアップが構成されている VM はスナップショット タスクの遅延を引き起こすことがあります**。 既定では、VM バックアップによって Windows VM 上に VSS フル バックアップが作成されます。 SQL Server を実行していて SQL Server のバックアップを構成されている VM では、スナップショットの遅延が発生する可能性があります。 スナップショットの遅延が原因でバックアップが失敗する場合は、次のレジストリ キーを設定します。

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

- **VM が RDP でシャットダウンされているため、VM の状態が正しく報告されません**。 リモート デスクトップを使用して仮想マシンをシャットダウンした場合は、ポータルで VM の状態が正しいことを確認します。 状態が正しくない場合は、ポータルの VM ダッシュボードの **[シャットダウン]** オプションを使用して VM をシャットダウンします。
- **5 つ以上の VM が同じクラウド サービスを共有している場合は、VM を複数のバックアップ ポリシーに分散します**。 4 つより多くの VM バックアップが同時に開始しないように、バックアップ時刻を調整します。 ポリシーで開始時刻が 1 時間以上離れるようにしてください。
- **VM の CPU またはメモリが高くなっています**。 仮想マシンのメモリまたは CPU の使用率が高くなり、90% を超えてると、スナップショット タスクがキューに格納されて遅延します。 最終的にはタイムアウトになります。この問題が発生した場合は、オンデマンド バックアップを試してください。

## <a name="networking"></a>ネットワーク
Backup 拡張機能は、他の拡張機能と同様に、パブリックなインターネットへのアクセスが必要です。 パブリック インターネットにアクセスできない場合、さまざまな問題が発生する可能性があります。

* 拡張機能のインストールが失敗する。
* ディスク スナップショットなどのバックアップ操作が失敗する。
* バックアップ操作の状態を表示できない。

パブリック インターネット アドレスを解決する必要性については、[この Azure サポート ブログ](https://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)をご覧ください。 VNET 用の DNS 構成を確認し、Azure の URI が解決できることを確認してください。

名前解決が正しく実行された後で、Azure IP へのアクセスも提供する必要があります。 Azure インフラストラクチャへのアクセスのブロックを解除するには、次のいずれかの手順に従います。

- Azure データセンターの IP 範囲の許可リスト:
   1. 許可リストに登録する [Azure データセンター IP](https://www.microsoft.com/download/details.aspx?id=41653) の一覧を取得します。
   1. [New-NetRoute](https://docs.microsoft.com/powershell/module/nettcpip/new-netroute) コマンドレットを使用して、IP アドレスのブロックを解除します。 管理者特権の PowerShell ウィンドウで、Azure VM 内でこのコマンドレットを実行します。 管理者として実行します。
   1. NSG を使用している場合は、規則を NSG に追加して IP にアクセスできるようにします。
- フローに対する HTTP トラフィック用のパスを作成します。
   1. 何らかのネットワーク制限を設定している場合は、トラフィックをルーティングするための HTTP プロキシ サーバーをデプロイします。 たとえば、ネットワーク セキュリティ グループです。 HTTP プロキシ サーバーをデプロイする手順は、「[ネットワーク接続を確立する](backup-azure-arm-vms-prepare.md#establish-network-connectivity)」を参照してください。
   1. NSG を使用している場合は、規則を NSG に追加して HTTP プロキシからインターネットにアクセスできるようにします。

> [!NOTE]
> IaaS VM バックアップが正しく機能するためには、ゲスト内で DHCP が有効になっている必要があります。 静的プライベート IP が必要な場合は、Azure portal または PowerShell を使用して構成します。 VM 内の DHCP オプションが有効になっていることを確認します。
> PowerShell を使用して静的 IP を設定する方法については、以下を参照してください。
> - [既存の VM に静的内部 IP を追加する方法](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm)
> - [ネットワーク インターフェイスに割り当てられているプライベート IP アドレスの割り当て方法を変更する](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)
>
>
