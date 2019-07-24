---
title: トラブルシューティング - IaaS VM の Azure Disk Encryption | Microsoft Docs
description: この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption のトラブルシューティングのヒントについて説明します。
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 3c6c552a6605278d8ab31264f5d180206e0badac
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490043"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption トラブルシューティング ガイド

このガイドは、所属組織が Azure Disk Encryption を使用しいる IT プロフェッショナル、情報セキュリティ アナリスト、クラウド管理者を対象としています。 この記事は、ディスクの暗号化に関連する問題のトラブルシューティングを支援することを目的としています。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS ディスクの暗号化のトラブルシューティング

Linux オペレーティング システム (OS) ディスクの暗号化には、ディスク全体の暗号化プロセスを実行する前に OS ドライブをマウント解除する必要があります。 ドライブをマウント解除できない場合は、"次の操作後のマウント解除に失敗しました …" というエラー メッセージが表示されることがあります。

このエラーは、サポートされているストック ギャラリー イメージから変更されたターゲット VM 環境に対して OS ディスク暗号化が試行されるときに発生することがあります。 サポートされているイメージから逸脱すると、OS ドライブのマウントを解除する拡張機能に影響を与える場合があります。 逸脱の例として、次のような項目があります。
- カスタマイズされたイメージが、サポートされているファイル システムやパーティション構成と一致しなくなった。
- SAP、MongoDB、Apache Cassandra、Docker などの大規模なアプリケーションは、暗号化の前に OS にインストールされて実行されている場合、サポートされない。 ディスクを暗号化するとき、OS ドライブの準備としてそれらのプロセスを安全にシャットダウンする必要がありますが、Azure Disk Encryption でそれができません。 OS ドライブの開かれているファイル ハンドルを保持しているアクティブ プロセスが引き続き存在する場合は、OS ドライブをマウント解除できないため、OS ドライブの暗号化は失敗します。 
- 暗号化が有効化された時刻に近い時刻にカスタム スクリプトが実行された、または暗号化プロセス中に、VM でその他の変更が実行された。 この競合は、Azure Resource Manager テンプレートに、同時に実行する複数の拡張機能が定義されている場合、またはカスタム スクリプト拡張機能またはその他のアクションがディスクの暗号化と同時に実行された場合に発生する可能性があります。 このような手順を連続した複数の手順にわけるか分離して実行することで、問題が解決することがあります。
- 暗号化を有効にする前に Security Enhanced Linux (SELinux) が無効化されていないため、マウント解除手順が失敗する。 SELinux は暗号化が完了した後、再度有効にできます。
- OS ディスクが Logical Volume Manager (LVM) スキームを使用している。 一部の LVM データ ディスクはサポートされていますが、LVM OS ディスクはサポートされていません。
- 最小メモリ要件が満たされていない (OS ディスクの暗号化には 7 GB を推奨)。
- データ ドライブは、/mnt/ ディレクトリ下に、または相互に再帰的にマウントされます (例: /mnt/data1、/mnt/data2、/data3 + /data3/data4)。
- Linux 用のその他の Azure Disk Encryption の[前提条件](azure-security-disk-encryption-prerequisites.md)が満たされていない。

## <a name="bkmk_Ubuntu14"></a> Ubuntu 14.04 LTS の既定のカーネルの更新

Ubuntu 14.04 LTS のイメージは、カーネルの既定のバージョン 4.4 に付属します。 このカーネル バージョンには、OS の暗号化の処理中に Out of Memory Killer により dd コマンドが不正に終了してしまう既知の問題があります。 このバグは、Azure 用にチューニングされた最新の Linux カーネルで修正されています。 このエラーを回避するには、イメージで暗号化を有効にする前に、次のコマンドを使用して [Azure 用にチューニングされたカーネル 4.15](https://packages.ubuntu.com/trusty/linux-azure) 以降に更新します。

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

新しいカーネルで VM が再起動されたら、新しいカーネル バージョンは次を使用して確認できます。

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Azure 仮想マシン エージェントおよび拡張機能のバージョンを更新する

Azure 仮想マシン エージェントのサポートされていないバージョンを使用した仮想マシン イメージでは、Azure Disk Encryption 操作が失敗する場合があります。 詳細については、「[Minimum version support for virtual machine agents in Azure (Azure での仮想マシン エージェントの最小バージョンのサポート)](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)」を参照してください。  

Microsoft.Azure.Security.AzureDiskEncryption または Microsoft.Azure.Security.AzureDiskEncryptionForLinux ゲスト エージェント拡張機能の適切なバージョンも必要です。 Azure 仮想マシン エージェントの前提条件が満たされ、サポートされているバージョンの仮想マシン エージェントが使用されている場合は、拡張機能のバージョンがプラットフォームによって自動的に管理および更新されます。

Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux 拡張機能は非推奨になったため、現在はサポートされていません。  

## <a name="unable-to-encrypt-linux-disks"></a>Linux のディスクを暗号化できない

場合によっては、ディスクの暗号化が「OS ディスクの暗号化が開始されました」というメッセージが表示されたまま先へ進まず、SSH が無効になっているように見えることがあります。 暗号化プロセスは、ストック ギャラリー イメージで終了するまでに 3 ～ 16 時間かかる可能性があります。 数テラバイトのデータ ディスクを追加した場合、プロセスは数日かかることもあります。

Linux OS ディスクの暗号化シーケンスは、OS ドライブを一時的にマウント解除します。 その後、OS ディスク全体のブロック単位の暗号化を実行した後、OS ディスクを暗号化された状態で再マウントします。 Windows の Azure Disk Encryption とは異なり、Linux のディスク暗号化では、暗号化が行われている間、VM を同時に使用することはできません。 暗号化を完了するために必要な時間は、VM のパフォーマンス特性によって大幅に異なる可能性があります。 これらの特性には、ディスクのサイズとストレージ アカウントのストレージが Standard であるか Premium (SSD) であるかが含まれます。

暗号化の状態を確認するには、[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドから返される **ProgressMessage** フィールドをポーリングします。 OS ドライブが暗号化されている間、VM はメンテナンス状態に入り、進行中のプロセスの中断を防ぐため SSH を無効にします。 暗号化が進行している時間の大半は、**EncryptionInProgress** メッセージによって状況がレポートされます。 数時間後、**VMRestartPending** メッセージによって VM を再起動するように求められます。 例: 


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

VM を再起動するように求められた後と VM の再開後、再起動と最後の手順がターゲットで実行されるまで 2、3 分待つ必要があります。 暗号化が最終的に完了すると、状態メッセージが変更されます。 完了メッセージが表示されたら、暗号化された OS ドライブを使用する準備ができ、VM を再び使用する準備も整っています。

以下に該当する場合は、暗号化の直前に作成したスナップショットまたはバックアップに VM を復元することお勧めします。
   - 前述の再起動シーケンスが発生しない場合。
   - ブート情報、進行状況メッセージ、またはその他のエラー インジケーターで、OS の暗号化がプロセスの途中で失敗したことが報告された場合。 メッセージの一例は、このガイドで説明した "マウント解除に失敗しました" エラーです。

次の試行の前に、VM の特性を再評価して、すべての前提条件が満たされていることを確認してください。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>ファイアウォール内の Azure Disk Encryption のトラブルシューティング
ファイアウォール、プロキシ要件、またはネットワーク セキュリティ グループ (NSG) 設定によって接続が制限されていると、必要なタスクを実行するための拡張機能が中断することがあります。 この中断によって、"拡張機能の状態が VM で取得できません" などのステータス メッセージが表示される可能性があります。 想定されるシナリオで、暗号化が完了できません。 以下のセクションで、調査することが推奨される一般的なファイアウォールの問題について説明します。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
適用されるネットワーク セキュリティ グループ設定で、ディスクの暗号化のために規定されている、ネットワーク構成の[前提条件](azure-security-disk-encryption-prerequisites.md#bkmk_GPO)を満たすようエンドポイントが設定されている必要があります。

### <a name="azure-key-vault-behind-a-firewall"></a>ファイアウォールの内側にある Azure Key Vault

[Azure AD の資格情報](azure-security-disk-encryption-prerequisites-aad.md)を使用して暗号化を有効にする場合、ターゲット VM は、Azure Active Directory のエンドポイントと Key Vault のエンドポイントの両方への接続を許可する必要があります。 現在の Azure Active Directory 認証エンドポイントは、「[Office 365 の URL と IP アドレスの範囲](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges)」ドキュメンテーションのセクション 56 と 59 に記載されています。 Key Vault の説明は、「[ファイアウォールの向こう側にある Access Azure Key Vault へのアクセス](../key-vault/key-vault-access-behind-firewall.md)」方法に関するドキュメンテーションにあります。

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
VM は、その VM 内からしかアクセスできない既知のルーティング不可能な IP アドレス (`169.254.169.254`) を使用する [Azure Instance Metadata サービス](../virtual-machines/windows/instance-metadata-service.md) エンドポイントにアクセスできる必要があります。  ローカル HTTP トラフィックをこのアドレスに変更する (たとえば X-Forwarded-For ヘッダーを追加する) プロキシ構成はサポートされません。

### <a name="linux-package-management-behind-a-firewall"></a>ファイアウォール内の Linux パッケージの管理

実行時の Linux 用 Azure Disk Encryption は、暗号化を有効にする前の必要な前提条件コンポーネントのインストールを、ターゲット ディストリビューションのパッケージ管理システムに依存しています。 ファイアウォールの設定が原因で、これらのコンポーネントを VM にダウンロードしてインストールできない場合、想定される次のシーケンスが失敗します。 このパッケージ管理システムを構成するための手順は、ディストリビューションによって大きく異なっている可能性があります。 Red Hat では、プロキシが必要な場合は、サブスクリプション マネージャーと yum が正しく設定されていることを確認する必要があります。 詳細については、「[How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533)」(subscription-manager と yum の問題をトラブルシューティングする方法) を参照してください。  


## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core のトラブルシューティング

Windows Server 2016 Server Core では既定で、bdehdcfg コンポーネントを使用できません。 このコンポーネントは、Azure Disk Encryption で必要です。 これは、VM の有効期間中 1 回のみ実行される、OS ボリュームからのシステム ボリュームの分割に使用されます。 これらのバイナリは、以後の暗号化操作には必要ありません。

この問題を解決するには、次の 4 つのファイルを Windows Server 2016 Data Center VM から Server Core 上の同じ場所にコピーします。

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. 次のコマンドを入力します。

   ```
   bdehdcfg.exe -target default
   ```

1. このコマンドは、550 MB のシステム パーティションを作成します。 システムを再起動します。

1. DiskPart を使用してボリュームを確認した後、次に進みます。  

例: 

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
<!-- ## Troubleshooting encryption status

If the expected encryption state does not match what is being reported in the portal, see the following support article:
[Encryption status is displayed incorrectly on the Azure Management Portal](https://support.microsoft.com/en-us/help/4058377/encryption-status-is-displayed-incorrectly-on-the-azure-management-por) --> 

## <a name="troubleshooting-encryption-status"></a>暗号化の状態のトラブルシューティング 

VM 内で非暗号化されたディスクが、ポータルで暗号化済みと表示される場合があります。  この状況は、高レベルの Azure Disk Encryption 管理コマンドを使用するのではなく、低レベルのコマンドを使用して VM 内から直接ディスクを非暗号化した場合に発生する可能性があります。  高レベルのコマンドでは、VM 内からディスクが非暗号化されるだけでなく、VM の外部で、重要なプラットフォーム レベルの暗号化の設定と VM に関連する拡張機能の設定が更新されます。  これらの設定が整合していないと、プラットフォームは暗号化の状態を報告できず、VM を適切にプロビジョニングすることもできません。   

Azure Disk Encryption を正しく無効にするには、暗号化が有効な既知の正常な状態から、Powershell コマンド [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) と [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) を使用するか、CLI コマンド [az vm encryption disable](/cli/azure/vm/encryption) を使用します。 

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Disk Encryption で発生する一般的な問題の詳細と、それらの問題のトラブルシューティング方法について説明しました。 このサービスと機能の詳細については、次の記事を参照してください。

- [Azure Security Center で Disk Encryption を適用する](../security-center/security-center-apply-disk-encryption.md)
- [保存時の Azure データの暗号化](azure-security-encryption-atrest.md)
