---
title: Windows 用の Azure Disk Encryption | Microsoft Docs
description: 仮想マシン拡張機能を使用して、Azure Disk Encryption を Windows 仮想マシンにデプロイします。
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 9a3e135172f0744c053da816b3c77762dbe783c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706114"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows 用 Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>概要

Azure Disk Encryption は、BitLocker を利用して、Windows を実行している Azure 仮想マシン上で完全なディスク暗号化を提供します。  このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで管理することができます。 

## <a name="prerequisites"></a>前提条件

前提条件の詳細な一覧については、[Azure Disk Encryption の前提条件](
../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

### <a name="operating-system"></a>オペレーティング システム

現在の Windows バージョンの一覧については、[Azure Disk Encryption の前提条件](../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

### <a name="internet-connectivity"></a>インターネット接続

Azure Disk Encryption では、Active Directory、Key Vault、Storage、パッケージ管理エンドポイントにアクセスするためにインターネット接続が必要です。  ネットワーク セキュリティ設定の詳細については、[Azure Disk Encryption の前提条件](
../../security/azure-security-disk-encryption-prerequisites.md)に関するセクションを参照してください。

## <a name="extension-schemata"></a>拡張機能のスキーマ

Azure Disk Encryption には 2 つのスキーマがあります。より新しい v1.1 は Azure Active Directory (AAD) のプロパティを使用しない推奨されるスキーマで、より古い v0.1 は AAD のプロパティを必要とします。 使用する拡張機能に対応するスキーマのバージョンを使う必要があります。スキーマ v1.1 は AzureDiskEncryption 拡張機能バージョン 1.1 用で、スキーマ v0.1 は AzureDiskEncryption 拡張機能バージョン 0.1 用です。

### <a name="schema-v11-no-aad-recommended"></a>スキーマ v1.1:AAD なし (推奨)

V1.1 スキーマは推奨されており、Azure Active Directory のプロパティを必要としません。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>スキーマ v0.1: AAD を含む 

0\.1 スキーマでは、`aadClientID` と、`aadClientSecret` または `AADClientCertificate` のいずれかを必要とします。

`aadClientSecret`コマンドを使用します。

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
| KeyVaultURL | url | string |
| (省略可能) Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | OS、Data、All | string |

## <a name="template-deployment"></a>テンプレートのデプロイ
テンプレートのデプロイの例については、「[Create a new encrypted Windows VM from gallery image (ギャラリー イメージから新しい暗号化された Windows VM を作成する)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)」を参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

手順については、最新の [Azure CLI ドキュメント](/cli/azure/vm/encryption?view=azure-cli-latest)を参照してください。 

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

[Azure Disk Encryption トラブルシューティング ガイド](../../security/azure-security-disk-encryption-tsg.md)を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順
拡張機能の詳細については、「[Windows 用の仮想マシン拡張機能とその機能](features-windows.md)」を参照してください。
