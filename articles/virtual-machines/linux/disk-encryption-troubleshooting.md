---
title: Linux VM の Azure Disk Encryption のトラブルシューティング
description: この記事では、Linux VM の Microsoft Azure Disk Encryption のトラブルシューティングのヒントについて説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563372"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Linux VM の Azure Disk Encryption のトラブルシューティング ガイド

このガイドは、所属組織が Azure Disk Encryption を使用しいる IT プロフェッショナル、情報セキュリティ アナリスト、クラウド管理者を対象としています。 この記事は、ディスクの暗号化に関連する問題のトラブルシューティングを支援することを目的としています。

次のいずれかの手順を実行する前に、暗号化しようとしている VM が、[サポートされている VM サイズとオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)であること、およびすべての前提条件を満たしていることを確認してください。

- [追加の VM 要件](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [ネットワーク要件](disk-encryption-overview.md#networking-requirements)
- [暗号化キーのストレージ要件](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS ディスクの暗号化のトラブルシューティング

Linux オペレーティング システム (OS) ディスクの暗号化には、ディスク全体の暗号化プロセスを実行する前に OS ドライブをマウント解除する必要があります。 ドライブをマウント解除できない場合は、"次の操作後のマウント解除に失敗しました …" というエラー メッセージが表示されることがあります。

このエラーは、サポートされているストック ギャラリー イメージから環境が変更された VM に対して OS ディスク暗号化が試行されるときに発生することがあります。 サポートされているイメージから逸脱すると、OS ドライブのマウントを解除する拡張機能に影響を与える場合があります。 逸脱の例として、次のような項目があります。
- カスタマイズされたイメージが、サポートされているファイル システムやパーティション構成と一致しなくなった。
- SAP、MongoDB、Apache Cassandra、Docker などの大規模なアプリケーションは、暗号化の前に OS にインストールされて実行されている場合、サポートされない。 ディスクを暗号化するとき、OS ドライブの準備としてそれらのプロセスを安全にシャットダウンする必要がありますが、Azure Disk Encryption でそれができません。 OS ドライブの開かれているファイル ハンドルを保持しているアクティブ プロセスが引き続き存在する場合は、OS ドライブをマウント解除できないため、OS ドライブの暗号化は失敗します。 
- 暗号化が有効化された時刻に近い時刻にカスタム スクリプトが実行された、または暗号化プロセス中に、VM でその他の変更が実行された。 この競合は、Azure Resource Manager テンプレートに、同時に実行する複数の拡張機能が定義されている場合、またはカスタム スクリプト拡張機能またはその他のアクションがディスクの暗号化と同時に実行された場合に発生する可能性があります。 このような手順を連続した複数の手順にわけるか分離して実行することで、問題が解決することがあります。
- 暗号化を有効にする前に Security Enhanced Linux (SELinux) が無効化されていないため、マウント解除手順が失敗する。 SELinux は暗号化が完了した後、再度有効にできます。
- OS ディスクが Logical Volume Manager (LVM) スキームを使用している。 一部の LVM データ ディスクはサポートされていますが、LVM OS ディスクはサポートされていません。
- 最小メモリ要件が満たされていない (OS ディスクの暗号化には 7 GB を推奨)。
- データ ドライブは、/mnt/ ディレクトリ下に、または相互に再帰的にマウントされます (例: /mnt/data1、/mnt/data2、/data3 + /data3/data4)。

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Ubuntu 14.04 LTS の既定のカーネルの更新

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

Azure 仮想マシン エージェントのサポートされていないバージョンを使用した仮想マシン イメージでは、Azure Disk Encryption 操作が失敗する場合があります。 2\.2.38 より前のエージェント バージョンを使用した Linux イメージは、暗号化を有効にする前に更新する必要があります。 詳細については、「[VM で Azure Linux エージェントを更新する方法](../extensions/update-linux-agent.md)」および「[Azure の仮想マシンのエージェントの最小バージョンのサポート](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)」を参照してください。

Microsoft.Azure.Security.AzureDiskEncryption または Microsoft.Azure.Security.AzureDiskEncryptionForLinux ゲスト エージェント拡張機能の適切なバージョンも必要です。 Azure 仮想マシン エージェントの前提条件が満たされ、サポートされているバージョンの仮想マシン エージェントが使用されている場合は、拡張機能のバージョンがプラットフォームによって自動的に管理および更新されます。

Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux 拡張機能は非推奨になったため、現在はサポートされていません。  

## <a name="unable-to-encrypt-linux-disks"></a>Linux のディスクを暗号化できない

場合によっては、ディスクの暗号化が「OS ディスクの暗号化が開始されました」というメッセージが表示されたまま先へ進まず、SSH が無効になっているように見えることがあります。 暗号化プロセスは、ストック ギャラリー イメージで終了するまでに 3 ～ 16 時間かかる可能性があります。 数テラバイトのデータ ディスクを追加した場合、プロセスは数日かかることもあります。

Linux OS ディスクの暗号化シーケンスは、OS ドライブを一時的にマウント解除します。 その後、OS ディスク全体のブロック単位の暗号化を実行した後、OS ディスクを暗号化された状態で再マウントします。 Linux のディスク暗号化では、暗号化が行われている間、VM を同時に使用することはできません。 暗号化を完了するために必要な時間は、VM のパフォーマンス特性によって大幅に異なる可能性があります。 これらの特性には、ディスクのサイズとストレージ アカウントのストレージが Standard であるか Premium (SSD) であるかが含まれます。

OS ドライブが暗号化されている間、VM はサービス状態に入り、進行中のプロセスの中断を防ぐため SSH を無効にします。  暗号化の状態を確認するには、Azure PowerShell の [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドを使用して **ProgressMessage** フィールドを確認します。 データおよび OS ディスクが暗号化される際に、**ProgressMessage** によって一連の状態が報告されます。

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

**ProgressMessage** は、暗号化プロセスの大部分にわたって "**OS disk encryption started**" (OS ディスクの暗号化が開始されました) のままです。  暗号化が正常に完了すると、**ProgressMessage** は戻って次のようになります。

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

完了メッセージが表示されたら、暗号化された OS ドライブを使用する準備ができ、VM を再び使用する準備も整っています。

ブート情報、進行状況のメッセージ、またはエラーによって、このプロセスの途中で OS 暗号化が失敗したことが報告される場合は、VM を暗号化の直前に作成されたスナップショットまたはバックアップに復元してください。 メッセージの一例は、このガイドで説明した "マウント解除に失敗しました" エラーです。

暗号化を再試行する前に、VM の特性を再評価して、すべての前提条件が満たされていることを確認してください。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>ファイアウォール内の Azure Disk Encryption のトラブルシューティング

[分離されたネットワークでのディスク暗号化](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>暗号化の状態のトラブルシューティング 

VM 内で非暗号化されたディスクが、ポータルで暗号化済みと表示される場合があります。  この状況は、高レベルの Azure Disk Encryption 管理コマンドを使用するのではなく、低レベルのコマンドを使用して VM 内から直接ディスクを非暗号化した場合に発生する可能性があります。  高レベルのコマンドでは、VM 内からディスクが非暗号化されるだけでなく、VM の外部で、重要なプラットフォーム レベルの暗号化の設定と VM に関連する拡張機能の設定が更新されます。  これらの設定が整合していないと、プラットフォームは暗号化の状態を報告できず、VM を適切にプロビジョニングすることもできません。

PowerShell で Azure Disk Encryption を無効にするには、最初に [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)、次に [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) を使用します。 暗号化を無効にする前に Remove-AzVMDiskEncryptionExtension を実行すると、失敗します。

CLI で Azure Disk Encryption を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption) を使用します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Disk Encryption で発生する一般的な問題の詳細と、それらの問題のトラブルシューティング方法について説明しました。 このサービスと機能の詳細については、次の記事を参照してください。

- [Azure Security Center でディスクの暗号化を適用する](../../security-center/asset-inventory.md)
- [保存時の Azure データの暗号化](../../security/fundamentals/encryption-atrest.md)