---
title: "Azure Disk Encryption のトラブルシューティング | Microsoft Docs"
description: "この記事では、Windows および Linux IaaS VM の Microsoft Azure Disk Encryption のトラブルシューティングのヒントについて説明します。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: ce0e23bd-07eb-43af-a56c-aa1a73bdb747
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 83821ed2f7db1c7dea88a1b4424d405959a206db
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption トラブルシューティング ガイド

このガイドは、所属組織が Azure Disk Encryption を使用しており、ディスクの暗号化に関連する問題をトラブルシューティングするためのガイダンスを必要としている IT プロフェッショナル、情報セキュリティ アナリスト、およびクラウド管理者を対象としています。

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux OS ディスクの暗号化のトラブルシューティング

Linux オペレーティング システム (OS) ディスクの暗号化には、ディスク全体の暗号化プロセスを実行する前に OS ドライブをマウント解除する必要があります。 ドライブをマウント解除できない場合は、エラー メッセージ "次の操作後のマウント解除に失敗しました …"  が表示される可能性があります。

このエラーが発生する可能性が最も高いのは、ターゲット VM 環境に対して OS ディスク暗号化を実行しようとしたが、その環境がサポートされているストック ギャラリー イメージから編集または変更されている場合です。 OS ドライブをマウント解除する拡張機能に影響を与える可能性のある、サポートされているイメージからの逸脱の例には、以下が含まれます。
- カスタマイズされたイメージが、サポートされているファイル システムやパーティション構成と一致しなくなった。
- 暗号化の前に、SAP、MongoDB、Apache Cassandra などの大規模なアプリケーションがインストールされ、OS で実行されている。 拡張機能は、これらのアプリケーションを適切にシャット ダウンすることができません。 アプリケーションが OS ドライブに対するファイル ハンドルのオープンを維持している場合、ドライブをマウント解除できず、それによってエラーが発生します。
- 暗号化が有効化された時刻に近い時刻にカスタム スクリプトが実行された、または暗号化プロセス中に、VM でその他の変更が実行された。 この競合は、Azure Resource Manager テンプレートに、同時に実行する複数の拡張機能が定義されている場合、またはカスタム スクリプト拡張機能またはその他のアクションがディスクの暗号化と同時に実行された場合に発生する可能性があります。 このような手順を連続した複数の手順にわけるか分離して実行することで、問題が解決することがあります。
- 暗号化を有効にする前に Security Enhanced Linux (SELinux) が無効化されていないため、マウント解除手順が失敗する。 SELinux は暗号化が完了した後、再度有効にできます。
- OS ディスクが Logical Volume Manager (LVM) スキームを使用している。 一部の LVM データ ディスクはサポートされていますが、LVM OS ディスクはサポートされていません。
- 最小メモリ要件が満たされていない (OS ディスクの暗号化には 7 GB を推奨)。
- データ ドライブは、/mnt/ ディレクトリ下に、または相互に再帰的にマウントされます (例: /mnt/data1、/mnt/data2、/data3 + /data3/data4)。
- Linux 用のその他の Azure Disk Encryption の[前提条件](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)が満たされていない。

## <a name="unable-to-encrypt"></a>暗号化できない

場合によっては、ディスクの暗号化が「OS ディスクの暗号化が開始されました」というメッセージが表示されたまま先へ進まず、SSH が無効になっているように見えることがあります。 暗号化プロセスは、ストック ギャラリー イメージで終了するまでに 3 ～ 16 時間かかる可能性があります。 数テラバイトのデータ ディスクを追加した場合、プロセスは数日かかることもあります。 

Linux OS ディスクの暗号化シーケンスは、OS ドライブを一時的にマウント解除します。 その後、OS ディスク全体のブロック単位の暗号化を実行した後、OS ディスクを暗号化された状態で再マウントします。 Windows の Azure Disk Encryption とは異なり、Linux のディスク暗号化では、暗号化が行われている間、VM を同時に使用することはできません。 暗号化を完了するために必要な時間は、VM のパフォーマンス特性によって大幅に異なる可能性があります。 これらの特性には、ディスクのサイズとストレージ アカウントのストレージが Standard であるか Premium (SSD) であるかが含まれます。

暗号化の状態を確認するには、[Get-AzureRmVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) コマンドから返される **ProgressMessage** フィールドをポーリングします。 OS ドライブが暗号化されている間、VM はメンテナンス状態に入り、進行中のプロセスの中断を防ぐため SSH を無効にします。 暗号化が進行している時間の大半は、**EncryptionInProgress** メッセージによって状況がレポートされます。 数時間後、**VMRestartPending** メッセージによって VM を再起動するように求められます。 次に例を示します。


```
PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $resourceGroupName -VMName $vmName
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

VM を再起動するように求められ、それに従って VM を再起動する操作を実行したら、再起動が開始され、ターゲット上で最後の手順が実行されるまで、2 ～ 3 分待機する必要があります。 暗号化が最終的に完了すると、状態メッセージが変更されます。 完了メッセージが表示されたら、暗号化された OS ドライブを使用する準備ができ、VM を再び使用する準備も整っています。

以下に該当する場合は、暗号化の直前に作成したスナップショットまたはバックアップに VM を復元することお勧めします。
   - 前述の再起動シーケンスが発生しない場合。
   - ブート情報、進行状況メッセージ、またはその他のエラー インジケーターで、OS の暗号化がプロセスの途中で失敗したことが報告された場合。 メッセージの一例は、このガイドで説明した "マウント解除に失敗しました" エラーです。

再試行する前に、VM の特性を再評価して、すべての前提条件が満たされていることを確認してください。

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>ファイアウォール内の Azure Disk Encryption のトラブルシューティング
ファイアウォール、プロキシ要件、またはネットワーク セキュリティ グループ (NSG) 設定によって接続が制限されていると、必要なタスクを実行するための拡張機能が中断することがあります。 この中断によって、"拡張機能の状態が VM で取得できません" などのステータス メッセージが表示される可能性があります。 想定されるシナリオで、暗号化が完了できません。 以下のセクションで、調査することが推奨される一般的なファイアウォールの問題について説明します。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
適用されるネットワーク セキュリティ グループ設定で、ディスクの暗号化のために規定されている、ネットワーク構成の[前提条件](https://docs.microsoft.com/azure/security/azure-security-disk-encryption#prerequisites)を満たすようエンドポイントが設定されている必要があります。

### <a name="azure-key-vault-behind-a-firewall"></a>ファイアウォールの内側にある Azure Key Vault
VM は、キー コンテナーにアクセスできる必要があります。 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall) チームが管理しているファイアウォール内からのキー コンテナーへのアクセスに関するガイダンスを参照してください。

### <a name="linux-package-management-behind-a-firewall"></a>ファイアウォール内の Linux パッケージの管理

実行時の Linux の Azure Disk Encryption では、暗号化を有効にする前に、ターゲット ディストリビューションのパッケージ管理システムを使用して、必要な前提条件コンポーネントをインストールする必要があります。 ファイアウォールの設定が原因で、これらのコンポーネントを VM にダウンロードしてインストールできない場合、想定される次のシーケンスが失敗します。 このパッケージ管理システムを構成するための手順は、ディストリビューションによって大きく異なっている可能性があります。 Red hat では、プロキシが必要な場合は、サブスクリプション マネージャーと yum が正しく設定されていることを保証する必要があります。 詳細については、「[How to troubleshoot subscription-manager and yum problems](https://access.redhat.com/solutions/189533)」(subscription-manager と yum の問題をトラブルシューティングする方法) を参照してください。  

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core のトラブルシューティング

Windows Server 2016 Server Core では、**bdehdcfg** コンポーネントは既定では使用できません。 Azure Disk Encryption はこのコンポーネントを必要とします。 次の手順に従って、**bdehdcfg** コンポーネントを追加してください。

   1. 次の 4 つのファイルを Windows Server 2016 Data Center VM から Server Core イメージの **c:\windows\system32** フォルダーにコピーします。

   ```
   bdehdcfg.exe
   bdehdcfglib.dll
   bdehdcfglib.dll.mui
   bdehdcfg.exe.mui
   ```

   2. 次のコマンドを入力します。

   ```
   bdehdcfg.exe -target default
   ```

   3. このコマンドは、550 MB のシステム パーティションを作成します。 システムを再起動します。 
   
   4. DiskPart を使用してボリュームを確認した後、次に進みます。  

次に例を示します。

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```
## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Disk Encryption で発生する一般的な問題の詳細と、それらの問題のトラブルシューティング方法について説明しました。 このサービスと機能の詳細については、次の記事を参照してください。

- [Azure Security Center でディスクの暗号化を適用する](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Azure Virtual Machine を暗号化する](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [保存時の Azure データの暗号化](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

