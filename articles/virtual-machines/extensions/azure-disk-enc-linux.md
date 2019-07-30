---
title: Linux 用 Azure Disk Encryption | Microsoft Docs
description: 仮想マシン拡張機能を使用して、Linux 用 Azure Disk Encryption を仮想マシンにデプロイします。
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: d544aae33faf60be00a2b4ea0a45f405efcedb39
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706143"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Linux 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>概要

Azure Disk Encryption は、Linux の DM-Crypt サブシステムを活用して、[特定の Azure Linux ディストリビューション](https://aka.ms/adelinux)でディスク全体の暗号化を提供します。  このソリューションは、ディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。

## <a name="prerequisites"></a>前提条件

前提条件の詳細な一覧については、[Azure Disk Encryption の前提条件](
../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

### <a name="operating-system"></a>オペレーティング システム

現在、Azure Disk Encryption は、特定のディストリビューションとバージョンでサポートされています。  [Azure Disk Encryption がサポートされているオペレーティング システム: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) に関するセクションで、サポートされている Linux ディストリビューションの一覧をご覧ください。

### <a name="internet-connectivity"></a>インターネット接続

Linux 用 Azure Disk Encryption では、Active Directory、Key Vault、Storage、パッケージ管理エンドポイントにアクセスするためにインターネット接続が必要です。  詳細については、[Azure Disk Encryption の前提条件](../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

## <a name="extension-schemata"></a>拡張機能のスキーマ

Azure Disk Encryption には 2 つのスキーマがあります。より新しい v1.1 は Azure Active Directory (AAD) のプロパティを使用しない推奨されるスキーマで、より古い v0.1 は AAD のプロパティを必要とします。 使用する拡張機能に対応するスキーマのバージョンを使用する必要があります。スキーマ v1.1 は AzureDiskEncryptionForLinux 拡張機能バージョン 1.1 用で、スキーマ v0.1 は AzureDiskEncryptionForLinux 拡張機能バージョン 0.1 用です。
### <a name="schema-v11-no-aad-recommended"></a>スキーマ v1.1:AAD なし (推奨)

V1.1 スキーマは推奨されていて、Azure Active Directory のプロパティを必要としません。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>スキーマ v0.1: AAD を含む 

0\.1 スキーマは、`aadClientID` と、`aadClientSecret` または `AADClientCertificate` のいずれかを必要とします。

`aadClientSecret`コマンドを使用します。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

`AADClientCertificate`コマンドを使用します。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>プロパティ値

| EnableAdfsAuthentication | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1、1.1 | int |
| (0.1 スキーマ) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 スキーマ) AADClientSecret | password | string |
| (0.1 スキーマ) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON 辞書 |
| EncryptionOperation | EnableEncryption、EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP'、'RSA-OAEP-256'、'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| (省略可能) KeyVaultURL | url | string |
| パスフレーズ | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | OS、Data、All | string |

## <a name="template-deployment"></a>テンプレートのデプロイ

テンプレートのデプロイの例については、「[Enable Encryption on a running Linux VM (実行中の Linux VM で暗号化を有効にする)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)」を参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

手順については、最新の [Azure CLI ドキュメント](/cli/azure/vm/encryption?view=azure-cli-latest)を参照してください。 

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

トラブルシューティングについては、「[Azure Disk Encryption トラブルシューティング ガイド](../../security/azure-security-disk-encryption-tsg.md)」を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順

VM 拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。