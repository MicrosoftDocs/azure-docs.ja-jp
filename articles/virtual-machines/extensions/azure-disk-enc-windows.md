---
title: Windows 用の Azure Disk Encryption
description: 仮想マシン拡張機能を使用して、Azure Disk Encryption を Windows 仮想マシンにデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 10268f8041f21f74e8ebcfaee41d207a53618260
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566245"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概要

Azure Disk Encryption は、BitLocker を利用して、Windows を実行している Azure 仮想マシン上で完全なディスク暗号化を提供します。  このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで管理することができます。 

## <a name="prerequisites"></a>前提条件

前提条件の完全な一覧については、「[Windows VM 用の Azure Disk Encryption](../windows/disk-encryption-overview.md)」の特に次のセクションを参照してください。

- [サポートされている VM とオペレーティング システム](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [ネットワーク要件](../windows/disk-encryption-overview.md#networking-requirements)
- [グループ ポリシーの要件](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>拡張機能のスキーマ

Azure Disk Encryption (ADE) 用の拡張機能のスキーマには、次の 2 つのバージョンがあります。
- v2.2 - Azure Active Directory (AAD) のプロパティを使用しない、推奨される新しいスキーマ。
- v1.1 - Azure Active Directory (AAD) のプロパティを必要とする以前のスキーマ。 

ターゲット スキーマを選択するには、`typeHandlerVersion` プロパティを、使用するスキーマのバージョンと同じ値に設定する必要があります。

### <a name="schema-v22-no-aad-recommended"></a>スキーマ v2.2: AAD なし (推奨)

v2.2 スキーマはすべての新しい VM で推奨されており、Azure Active Directory のプロパティを必要としません。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>スキーマ v1.1: AAD を使用 

1.1 スキーマでは、`aadClientID` と、`aadClientSecret` または `AADClientCertificate` のいずれかを必要とします。新しい VM には推奨されません。

`aadClientSecret`の使用

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
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
| type | AzureDiskEncryption | string |
| typeHandlerVersion | 2.2、1.1 | string |
| (1.1 スキーマ) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 スキーマ) AADClientSecret | password | string |
| (1.1 スキーマ) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption | string | 
| (省略可能 - 既定値 RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (省略可能) KeyEncryptionKeyURL | url | string |
| (省略可能) KekVaultResourceId | url | string |
| (省略可能) SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | OS、Data、All | string |

## <a name="template-deployment"></a>テンプレートのデプロイ

スキーマ v2.2 に基づくテンプレートのデプロイの例については、Azure クイックスタート テンプレートの [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad) を参照してください。

スキーマ v1.1 に基づくテンプレートのデプロイの例については、Azure クイックスタート テンプレートの [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) を参照してください。

>[!NOTE]
> また、`VolumeType` パラメーターが All に設定されている場合は、正しくフォーマットされている場合にのみ、データ ディスクが暗号化されます。 

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

トラブルシューティングについては、「[Azure Disk Encryption トラブルシューティング ガイド](../windows/disk-encryption-troubleshooting.md)」を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 

または、Azure サポート インシデントを送信できます。 [Azure サポート](https://azure.microsoft.com/support/options/)に移動して、[サポートを受ける] を選択します。 Azure サポートの使用方法の詳細については、「 [Azure Support FAQ (Microsoft Azure サポートに関する FAQ)](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 拡張機能の詳細については、「[Windows 用の仮想マシン拡張機能とその機能](features-windows.md)」を参照してください。
* Windows 用の Azure Disk Encryption の詳細については、「[Windows Virtual Machines](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)」を参照してください。
