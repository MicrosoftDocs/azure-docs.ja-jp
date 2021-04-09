---
title: Azure Disk Encryption トラブルシューティング ガイド
description: この記事では、Windows VM 用の Microsoft Azure Disk Encryption のトラブルシューティングのヒントについて説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 057aba3e49d32694563f412101be499342f2aad0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550605"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption トラブルシューティング ガイド

このガイドは、所属組織が Azure Disk Encryption を使用しいる IT プロフェッショナル、情報セキュリティ アナリスト、クラウド管理者を対象としています。 この記事は、ディスクの暗号化に関連する問題のトラブルシューティングを支援することを目的としています。

次のいずれかの手順を実行する前に、暗号化しようとしている VM が、[サポートされている VM サイズとオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)であること、およびすべての前提条件を満たしていることを確認してください。

- [ネットワーク要件](disk-encryption-overview.md#networking-requirements)
- [グループ ポリシーの要件](disk-encryption-overview.md#group-policy-requirements)
- [暗号化キーのストレージ要件](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>ファイアウォール内の Azure Disk Encryption のトラブルシューティング

ファイアウォール、プロキシ要件、またはネットワーク セキュリティ グループ (NSG) 設定によって接続が制限されていると、必要なタスクを実行するための拡張機能が中断することがあります。 この中断によって、"拡張機能の状態が VM で取得できません" などのステータス メッセージが表示される可能性があります。 想定されるシナリオで、暗号化が完了できません。 以下のセクションで、調査することが推奨される一般的なファイアウォールの問題について説明します。

### <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
適用されるネットワーク セキュリティ グループ設定で、ディスクの暗号化のために規定されている、ネットワーク構成の[前提条件](disk-encryption-overview.md#networking-requirements)を満たすようエンドポイントが設定されている必要があります。

### <a name="azure-key-vault-behind-a-firewall"></a>ファイアウォールの内側にある Azure Key Vault

[Azure AD の資格情報](disk-encryption-windows-aad.md#)を使用して暗号化を有効にする場合、ターゲット VM は、Azure Active Directory のエンドポイントと Key Vault のエンドポイントの両方への接続を許可する必要があります。 現在の Azure Active Directory 認証エンドポイントは、[Microsoft 365 の URL と IP アドレスの範囲](/microsoft-365/enterprise/urls-and-ip-address-ranges)に関するドキュメントのセクション 56 と 59 に記載されています。 Key Vault の説明は、「[ファイアウォールの向こう側にある Access Azure Key Vault へのアクセス](../../key-vault/general/access-behind-firewall.md)」方法に関するドキュメンテーションにあります。

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
VM は、その VM 内からしかアクセスできない既知のルーティング不可能な IP アドレス (`169.254.169.254`) を使用する [Azure Instance Metadata サービス](../windows/instance-metadata-service.md) エンドポイントにアクセスできる必要があります。  ローカル HTTP トラフィックをこのアドレスに変更する (たとえば X-Forwarded-For ヘッダーを追加する) プロキシ構成はサポートされません。

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

次に例を示します。

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>暗号化の状態のトラブルシューティング 

VM 内で非暗号化されたディスクが、ポータルで暗号化済みと表示される場合があります。  この状況は、高レベルの Azure Disk Encryption 管理コマンドを使用するのではなく、低レベルのコマンドを使用して VM 内から直接ディスクを非暗号化した場合に発生する可能性があります。  高レベルのコマンドでは、VM 内からディスクが非暗号化されるだけでなく、VM の外部で、重要なプラットフォーム レベルの暗号化の設定と VM に関連する拡張機能の設定が更新されます。  これらの設定が整合していないと、プラットフォームは暗号化の状態を報告できず、VM を適切にプロビジョニングすることもできません。

PowerShell で Azure Disk Encryption を無効にするには、最初に [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption)、次に [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) を使用します。 暗号化を無効にする前に Remove-AzVMDiskEncryptionExtension を実行すると、失敗します。

CLI で Azure Disk Encryption を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption) を使用します。 

## 



## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Disk Encryption で発生する一般的な問題の詳細と、それらの問題のトラブルシューティング方法について説明しました。 このサービスと機能の詳細については、次の記事を参照してください。

- [Azure Security Center でディスクの暗号化を適用する](../../security-center/asset-inventory.md)
- [保存時の Azure データの暗号化](../../security/fundamentals/encryption-atrest.md)