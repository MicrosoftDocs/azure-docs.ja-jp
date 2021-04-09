---
title: Linux に対する Azure Disk Encryption
description: 仮想マシン拡張機能を使用して、Linux 用 Azure Disk Encryption を仮想マシンにデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 7c79391e3459804a4b5ce72c2230d17af3269641
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566262"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概要

Azure Disk Encryption は、Linux の DM-Crypt サブシステムを活用して、[特定の Azure Linux ディストリビューション](../linux/disk-encryption-overview.md)でディスク全体の暗号化を提供します。  このソリューションは、ディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。

## <a name="prerequisites"></a>前提条件

前提条件の完全な一覧については、「[Linux VM に対する Azure Disk Encryption](../linux/disk-encryption-overview.md)」の特に次のセクションを参照してください。

- [サポートされている VM とオペレーティング システム](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [追加の VM 要件](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [ネットワーク要件](../linux/disk-encryption-overview.md#networking-requirements)
- [暗号化キーのストレージ要件](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>拡張機能のスキーマ

Azure Disk Encryption (ADE) 用の拡張スキーマには、次の 2 つのバージョンがあります。
- v1.1 - Azure Active Directory (AAD) のプロパティを使用しない、推奨される新しいスキーマ。
- v0.1 - Azure Active Directory (AAD) のプロパティを必要とする以前のスキーマ。 

ターゲット スキーマを選択するには、`typeHandlerVersion` プロパティを、使用するスキーマのバージョンと同じ値に設定する必要があります。

### <a name="schema-v11-no-aad-recommended"></a>スキーマ v1.1: AAD なし (推奨)

v1.1 スキーマは推奨されており、Azure Active Directory (AAD) のプロパティを必要としません。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>スキーマ v0.1: AAD を含む 

0.1 スキーマでは、`AADClientID` と、`AADClientSecret` または `AADClientCertificate` のいずれかを必要とします。

`AADClientSecret`の使用

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

`AADClientCertificate`の使用

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1、0.1 | INT |
| (0.1 スキーマ) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 スキーマ) AADClientSecret | password | string |
| (0.1 スキーマ) AADClientCertificate | thumbprint | string |
| (省略可能) (0.1 スキーマ) パスフレーズ | password | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 辞書 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | string | 
| (省略可能 - 既定値 RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (省略可能) KeyEncryptionKeyURL | url | string |
| (省略可能) KekVaultResourceId | url | string |
| (省略可能) SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | OS、Data、All | string |

## <a name="template-deployment"></a>テンプレートのデプロイ

スキーマ v1.1 に基づくテンプレートのデプロイの例については、Azure クイックスタート テンプレートの [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad) を参照してください。

スキーマ v0.1 に基づくテンプレートのデプロイの例については、Azure クイックスタート テンプレートの [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) を参照してください。

>[!WARNING]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。
> - Linux OS ボリュームを暗号化する場合、VM は利用不可と見なす必要があります。 暗号化プロセス中にアクセスする必要がある、開かれたファイルがブロックされる問題を回避するために、暗号化の進行中は SSH login を避けることを強くお勧めします。 進行状況を確認するには、[Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell コマンドレットまたは [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI コマンドを使用します。 30GB の OS ボリュームに対するこのプロセスの実行には、数時間に加え、データ ボリュームを暗号化するための時間がかかることが予測されます。 データ ボリュームの暗号化の時間は、暗号化形式の全オプションが使用されている場合を除いて、データ ボリュームのサイズと数量に比例します。 
> - Linux VM での暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。 

>[!NOTE]
> また、`VolumeType` パラメーターが All に設定されている場合は、正しくマウントされている場合にのみ、データ ディスクが暗号化されます。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

トラブルシューティングについては、「[Azure Disk Encryption トラブルシューティング ガイド](../linux/disk-encryption-troubleshooting.md)」を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 

または、Azure サポート インシデントを送信できます。 [Azure サポート](https://azure.microsoft.com/support/options/)に移動して、[サポートを受ける] を選択します。 Azure サポートの使用方法の詳細については、「 [Azure Support FAQ (Microsoft Azure サポートに関する FAQ)](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ

* VM 拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。
* Linux 用の Azure Disk Encryption の詳細については、「[Linux Virtual Machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)」を参照してください。