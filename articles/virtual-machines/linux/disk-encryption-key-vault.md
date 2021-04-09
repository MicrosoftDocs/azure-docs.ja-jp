---
title: Azure Disk Encryption 用のキー コンテナーの作成と構成
description: この記事では、Linux VM の Azure Disk Encryption で使用するためのキー コンテナーを作成および構成する手順について説明します。
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: d163ee552d95322a45acd571966a22e99aa1512e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562607"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Azure Disk Encryption 用のキー コンテナーの作成と構成

Azure Disk Encryption では、Azure Key Vault を使用して、ディスク暗号化キーとシークレットを制御および管理します。  キー コンテナーの詳細については、「[Azure Key Vault の概要](../../key-vault/general/overview.md)」と「[キー コンテナーのセキュリティ保護](../../key-vault/general/secure-your-key-vault.md)」を参照してください。 

> [!WARNING]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 詳細については、「[Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](disk-encryption-key-vault-aad.md)」を参照してください。

Azure Disk Encryption で使用するためのキー コンテナーの作成と構成には、次の 3 つの手順が必要です。

1. 必要に応じて、リソース グループを作成する。
2. キー コンテナーを作成する。 
3. キー コンテナーの高度なアクセス ポリシーを設定する。

これらの手順は、次のクイックスタートで説明されています。

- [Azure CLI を使用して Linux VM を作成、暗号化する](disk-encryption-cli-quickstart.md)
- [Azure PowerShell を使用して Linux VM を作成、暗号化する](disk-encryption-powershell-quickstart.md)

また、必要に応じて、キー暗号化キー (KEK) を生成またはインポートすることもできます。

> [!Note]
> この記事の手順は、[Azure Disk Encryption の前提条件となる CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)および [Azure Disk Encryption の前提条件となる PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)に関するページで自動化されています。

## <a name="install-tools-and-connect-to-azure"></a>ツールをインストールし、Azure に接続する

この記事の手順を完了するには、[Azure CLI](/cli/azure/)、[Azure PowerShell Az モジュール](/powershell/azure/)、または [Azure portal](https://portal.azure.com) のいずれかを使用します。 

ポータルにはブラウザーからアクセスできますが、Azure CLI と Azure PowerShell ではローカル インストールが必要です。詳細については、[Linux 用の Azure Disk Encryption のインストール ツール](disk-encryption-linux.md#install-tools-and-connect-to-azure) に関するセクションを参照してください。

### <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

Azure CLI または Azure PowerShell を使用する前に、まず Azure サブスクリプションに接続する必要があります。 これは、[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)、[Azure PowerShell を使用してサインインする](/powershell/azure/authenticate-azureps)、または、メッセージが表示されたときに資格情報を Azure portal に提供する、のいずれかの方法で行います。

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption の前提条件の CLI スクリプト](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption の前提条件の PowerShell スクリプト](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Linux VM での Azure Disk Encryption シナリオ](disk-encryption-linux.md)を学習する
- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)の方法について学習する
- [Azure Disk Encryption のサンプル スクリプト](disk-encryption-sample-scripts.md)を読む
